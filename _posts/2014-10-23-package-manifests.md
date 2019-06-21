---
ID: 226028
post_title: Package Manifests
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/package-manifests/
published: true
post_date: 2014-10-23 00:00:00
---
As we work on the designs for getting [NuGet in the platform][1], it has become clear that **NuGet needs better support for carrying arbitrary artifacts in packages and exposing them for consumption after the initial package installation**.

## Conventional Manifests

Since NuGet's initial release, it has supported conventional manifests and only a few concepts.

1.  Assembly references from the `lib` folder
2.  Content files from the `content` folder
3.  PowerShell scripts from the `tools` folder
4.  MSBuild props/targets imports from the `build` folder
5.  A `README.txt` file at the root of the package to be opened automatically after installation

All other files inside the package are completely ignored and NuGet's existing design makes it difficult to expand on these concepts or introduce new ones.

Before we dive into where we think NuGet is going, let's look closer at what NuGet supports today within its manifests. These are all implemented based on conventions for how the package is laid out--the manifests are not explicitly declared within the `nuspec`.

### Assembly References

The `lib` folder is reserved for assemblies that will get referenced by the project. During installation, NuGet gets the list of assemblies in the `lib` folder and calls the Visual Studio API for adding an assembly reference to each.

There are a couple of additional features this folder supports.

#### Target Framework Selection

If you provide a subfolder under `lib` that matches a framework name we recognize, we use that as an applicability filter. When finding references from the package, we'll choose the best `lib` folder for the project; if none of the folders match, then installation fails.

#### Deployment-Only Assemblies

If your package needs to carry assemblies that are used only for deployment and don't need actual Visual Studio/MSBuild references, then you can provide additional configuration within your `nuspec` to specific which set of assemblies actually do need references.

### Content Files

The `content` folder includes content files that will be copied into the project, putting those files directly into the project itself. This is how jQuery and other JavaScript/CSS files get into your project from a NuGet package.

This scenario has come under debate for a couple reasons. Many developers don't like that [NuGet packages get to specify where][2] in the project the content items will be placed. Others want to prevent the content files from getting copied into the project at all and [have Package Restore do the work instead][3].

Content files also support target framework filtering, XML [transformations for app configuration][4], and [source code transformations][5] for replacing tokens with project properties.

### PowerShell Scripts

When a package carries an `install.ps1` file within its `tools` folder, the script will be run after package installation. An `uninstall.ps1` is executed before uninstallation. Lastly, `init.ps1` is executed every time the solution is opened (assuming the NuGet PowerShell Console is open). Target framework filters apply to this folder too.

The mere existence of this feature is a blocker for allowing package installation outside the Visual Studio context. These scripts are executed through our PowerShell Console within Visual Studio, they reference Visual Studio DTE API, and they rely on the MSBuild project being loaded and available.

This PowerShell capability has always been an escape hatch to allow a package to express something beyond the capabilities NuGet had for assembly references and content files. NuGet has no way of knowing whether these scripts are optional or if they are truly required for the package to work.

### MSBuild Imports

NuGet looks in the `build` folder for props and targets files where the filename matches the package id. These are automatically imported into the project (at the top or bottom, respectively) using Visual Studio's DTE API and applying the target framework filtering. We add the imports in a way that is friendly for Package Restore, but the workflow still isn't great.

This props/targets feature was added after we observed it was the most common use of PowerShell `install.ps1` scripts. But oddly enough, this feature itself is another escape hatch to allow a package to express something beyond the capabilities NuGet had for assembly references and content files!

### README.txt

If the package has a `README.txt` file at the root of the package, NuGet will automatically open this file when the package is installed (directly, not as a dependency). This is the only file we recognize for this feature--we don't recognize Markdown or HTML files, and there are no conventions for determining applicability of showing this file. We've seen requests for conditionally opening this file in certain configurations, but we've had to tell those authors just to use PowerShell.

## NuGet Package Capabilities

If you were paying attention, you noticed that two of NuGet's five package conventions exist solely to augment NuGet's built-in functionality.

**Something's wrong there.**

Merging the two extensibility points into one, let's recap the *actual* features NuGet packages offer:

1.  Assemblies: either referenced or deployed
2.  Content files: copied into your project
3.  Extensibility points for other types of artifacts
4.  A readme experience that is too restrictive

That's it. If you think about it, it's pretty amazing that NuGet has had the adoption it has with its functionality boiling down to that.

## Revisiting Fundamentals

When we look at the feedback we get from people who have invested heavily in using NuGet, we hear two common themes:

1.  NuGet should resolve the assemblies at build-time instead of install-time
2.  NuGet should let package consumers [control how content files are brought in][2] from packages, or [not bring them in][3] at all

The feedback on the install-time actions goes all the way back to [NuGet's initial design][6] and this is being revisited as we move into the platform, at least for the handling of assemblies from the `lib` folder.

It's time to revisit the fundamentals of what is carried inside a NuGet package and design a new package manifest.

### Goals

There are several salient goals for creating a new package manifest design.

1.  Enable build-time manifest discovery and selection
2.  Make NuGet ignorant of target frameworks so that it doesn't have to be updated every time Xamarin or Microsoft adds a new framework
3.  Allow applicability filters beyond target framework, including configuration, architecture, etc., and allow them to be nested
4.  Make NuGet ignorant of the filters to make NuGet reusable in new domains and scenarios
5.  Make NuGet ignorant of *the manifest artifacts themselves* to make NuGet reusable in new domains and scenarios
6.  Ensure that the gallery and feed both expose all of the manifest information so that tools don't have to download and unpack `nupkg` files (pronounced "NUP-KEG" by the way) to discover what artifacts are in the package
7.  Switch from XML-formatted `nuspec` files to JSON (because people like authoring and reading JSON better, it's consistent with the direction we're taking the feed, and it could potentially align with [ASP.NET vNext's project.json file][7])

### Separating Authoring from Protocol

The `nuspec` has always been NuGet's "protocol". It is how packages are understood. Except it is incomplete because it doesn't include the package manifest--that part was just based on the files within the package itself. This approach allowed the files within the package to be self-describing and the single source of truth. But it has caused us some problems.

Just to name a few of the issues we've faced:

1.  The package contents are required to get the manifest
2.  The manifest is needed to determine target framework compatibility
3.  Since conventions are applied at package installation time (rather than pack time), adding new conventions is a backwards-compatibility nightmare!

We want NuGet packages to have a clean authoring experience; you shouldn't have to author the file manifest if it can be gleaned from conventions. But we need to reduce the protocol down to a single source of information (a package manifest) rather than requiring both the `nuspec` and the package contents. We will also benefit greatly from applying any conventions at pack time and making packages' behavior deterministic rather than vulnerable to the client's understanding of conventions.

### Backwards Compatibility

As we explore producing an authoritative package manifest at the time of packing, we have to be thoughtful of the NuGet clients that exist today--the ones that wouldn't know to look for this new manifest. We want newly created packages to contain both the authoritative manifest and also be backwards compatible--except for when the packages use *new* features.

### Proposal

The most common scenario for packing today is when you have a `nuspec` file and then artifacts on disk. You run `nuget.exe pack` against your nuspec and you get the following result (ignoring the actual package contents):

*   `id.version.nupkg` 
    *   `id.nuspec`

With the proposal, the result would be changed to the following:

*   `id.version.nupkg` 
    *   `id.nuspec`
    *   `nuget.manifest.json`

#### Keeping the nuspec

The `nuspec` file inside the package would still be XML-based and it would contain the following critical components:

1.  Id
2.  Version
3.  Authors
4.  Description
5.  NuGet minClientVersion attribute

If the package does not use any new NuGet features, then all of the rest of the `nuspec` would be filled in too (including fields such as `Dependencies` and `References`), and the `minClientVersion` would not be set to require NuGet 3.0. That provides backwards-compatibility with old NuGet clients.

If the package does use new NuGet features, then no other fields would be provided in the `nuspec` and the `minClientVersion` would be set to 3.0 (or newer). This compromise allows old NuGet clients to still load the package for display purposes but then respect the `minClientVersion` when the user tries to install the package.

#### Introducing the Manifest

The `nuget.manifest.json` file would contain all of the information carried in the `nuspec` but it would also carry the important artifact manifest. The conventions that NuGet applies at install-time today would be applied at pack time and then persisted into this manifest.

When a NuGet 3.0 client reads a package that contains a manifest, the manifest would win and explicitly state what should be done with the package contents--no conventions would be applied at package installation time.

#### Authoring a Manifest Directly

Are you tired of maintaining an XML-based `nuspec` file? Do you prefer JSON? Do you want to use new manifest features that we'll be introducing? Great; you can author the manifest directly!

<blockquote class="twitter-tweet" lang="en">
  <p>
    Conventions are an awesome way to imply configuration. But make sure you also allow explicit configuration to deviate from conventions.
  </p>— Jeff Handley (@JeffHandley) 
  
  <a href="https://twitter.com/JeffHandley/status/522981703842021376">October 17, 2014</a>
</blockquote>

If you want to take over manifest authoring, you'll be able to. Instead of creating a `nuspec` file, you would author a `nuget.json` file (or perhaps use ASP.NET's `project.json` file). Authoring in this format allows you to either retain the pack-time conventions that NuGet will apply or override them as needed. When the package would be created, it would then have the following files in it:

*   `id.version.nupkg` 
    *   `id.nuspec`
    *   `nuget.json`
    *   `nuget.manifest.json`

Three things happened here. First, the original `nuget.json` file was brought into the package. Second, a `nuget.manifest.json` file was created. And third, the `id.nuspec` file was also created.

We'd create the `nuspec` file for backwards-compatibility reasons stated above. We'd create the `nuget.manifest.json` file as a result of your author-time `nuget.json` and the authoritative manifest. That allows for your author-time `nuget.json` file to take some shortcuts and still use conventions as you desire, while retaining the authoritative nature of the manifest file. We'd also include your author-time `nuget.json` file to still provide the nice package editing features that you get with tools like [NuGet Package Explorer][8]--that allows the `nupkg` to remain a unit that can be copied around, edited, re-packed, and consumed. It could however be an option to omit the `nuget.json` file from the package if you don't want to ship your author-time format.

*Side-note... We're also thinking that the `nuget.manifest.json` file will utilize [JSON-LD][9] the same way our API v3 work does.*

#### Relying only on the Manifest

It's critical to note that at package consumption time, NuGet 3.0 would *only* use the `nuget.manifest.json` file. Even though the package still contains the `nuspec` and potentially the author-time `nuget.json` file, the *protocol* for consuming packages would be based on the `nuget.manifest.json` file.

We'd end up with a simple if block in the NuGet code:

1.  If the package contains a `nuget.manifest.json` file then use **only** that file for understanding the manifest and don't apply any conventions at consumption time
2.  Otherwise, read the `nuspec` and the file list like NuGet always has, applying conventions at consumption time

## More to Discuss

We have a lot more to discuss about the plans for NuGet 3.0. We need to talk about what these arbitrary package manifests look like, how they're configurable, how NuGet-based tools would read and understand the information, and more. We'll keep working on writing up our thoughts on these topics, but please let us know what you think so far.

 [1]: /20141014/in-the-platform.html
 [2]: http://nuget.codeplex.com/workitem/1914
 [3]: http://nuget.codeplex.com/workitem/1239
 [4]: http://docs.nuget.org/docs/creating-packages/configuration-file-and-source-code-transformations#Configuration_File_Transformations
 [5]: http://docs.nuget.org/docs/creating-packages/configuration-file-and-source-code-transformations#Specifying_Source_Code_Transformations
 [6]: /20141010/nuget-is-broken.html
 [7]: https://github.com/aspnet/Home/wiki/Project.json-file
 [8]: http://npe.codeplex.com
 [9]: http://json-ld.org