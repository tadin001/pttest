---
ID: 74
post_title: 'NuGet :  Broken By Design'
author: The NuGet Team
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-is-broken/
published: true
post_date: 2014-10-10 00:00:00
---
On January 13, 2011, NuGet 1.0 was released with ASP.NET MVC 3. On June 19, 2012, NuGet 2.0 shipped in the box with all editions of Visual Studio 2012 (including the free ones). That was a little over 2 years ago, and NuGet's adoption has been steadily increasing since then.

Here are some interesting figures for recent NuGet usage from [nuget.org][1]:

*   Over 260,000,000 total package downloads
*   An average of 1,500,000 package searches per day
*   About 700,000 package downloads per day
*   Well over 250,000 total packages
*   More than 27,000 unique packages

The NuGet is team quite happy with the adoption we've seen. But at almost 4 years old, NuGet is starting to show its age and some even claim that "NuGet is broken."

## Reasons for Success

Before we delve into the ways in which NuGet can be considered "broken," let's take a look at why NuGet has been successful. We'll see how some design choices made sense 4 years ago but can be questioned now. The challenge is to find ways to identify which choices should be revisited because circumstances have changed, and which ones need to be left alone to ensure NuGet's continued success.

### It's Just a Macro

When NuGet was first released, it didn't matter whether you were on a 1-person team or a 20-person team, because either way, you could start using NuGet on your project without disrupting the team's existing workflows. All NuGet did was automate what you were doing manually:

1.  Search the internet for the library you want
2.  Download it as a zip
3.  Right-click it, hit properties, and unblock the file
4.  Unzip it into a solution-level folder for your external dependencies (I always called mine `lib`)
5.  Look inside the expanded contents for: 
    1.  DLLs that need references added in your project (often involving some guess work)
    2.  Content files that you should copy into your project
    3.  Web.Config additions that need to be applied
    4.  Any README or other documentation listing out additional changes needed to your project

When I'm introducing NuGet to new audiences, I often explain that "NuGet is just a VS macro." It's honestly pretty true, both in spirit, and in how NuGet is implemented. Here's how NuGet supported the above workflow:

1.  A central repository at www.nuget.org is used for package discovery
2.  Packages are expanded into a `packages` folder at the solution-level
3.  The contents of the package are automatically handled: 
    1.  Assembly references are added from the package's `lib` folder, selecting the best target framework match and allowing specification of the subset of DLLs to reference when needed
    2.  Content files are copied into your project, with support for tokens getting replaced with values from the target project (such as root namespace)
    3.  Web.Config transforms are applied
    4.  PowerShell scripts can be run that automate any additional changes needed to the project

Automating package installation is great, but where NuGet really shined in the early days was when you needed to uninstall a package. NuGet would reverse the actions it took to install the package--and I don't know about you, but that's the part I would always mess up. Weeks or months could have passed since I referenced a library, and unwinding the steps I took to reference the library was always error-prone. NuGet nailed this.

Combine the clean uninstallation and the installation of a package, and you have NuGet's package update process. Clean, simple, and reliable. No guess-work for the package consumer--It Just Works<sup>TM</sup>!

### No Prompts or Choices

During the install/uninstall/update workflows, NuGet intentionally avoids prompting users for any choices. Here's the sum total of the prompts that NuGet has today:

1.  License Acceptance for packages that require it
2.  Whether to overwrite or ignore content files that already exist

That's it, and this is critical. If NuGet had prompts for all of the choices it needs to make, users would be intimidated and worried. NuGet took a "worry-free" approach so that package consumers had it easy. Here are some of the choices NuGet makes on your behalf:

1.  Which version of the package to install: The latest version is selected automatically
2.  How to resolve dependencies: Choose the lowest satisfactory dependency version
3.  Which set of DLLs need to be referenced: Select the best target framework match

### PowerShell Extensibility

This is one of the most contentious topics NuGet has faced. Many are still shocked that NuGet allows packages to run arbitrary PowerShell scripts during package installation. This topic came up recently when the actions of the ever-popular [Newtonsoft.Json][2] package [were criticized][3]. That package uses PowerShell to determine if the user just used the NuGet Package Manager dialog to find and directly install Newtonsoft.Json; if so, a web browser is opened to a documentation page for the package that, including helpful topics, has advertising on the page. Note that the script went to great lengths (justifying the "yolo" comment at the end of the file) to *only* show this popup if the user *directly* installed the package.

While the actions of the Newtonsoft.Json package are questionable, they are not harmful, and therefore we support them. Many have argued, and some have demonstrated, that this power could be used for evil purposes. So why does NuGet support PowerShell scripts if it's so dangerous?

#### Escape Hatch

It's an extensibility point with unbounded functionality. Package authors could augment NuGet's package installation conventions to perform any additional actions their package needs to be referenced. We often refer to the feature as an "escape hatch."

Here are some examples that we've seen over the years where PowerShell scripts proved to be valuable:

##### Visual Studio Version-Specific Files

[jQuery][4] uses PowerShell to provide the right IntelliSense file for you, depending on your version of Visual Studio.

##### Target Framework or Language-Specific Content Files

Many packages need to choose content files based on target framework or project language. We ended up adding target framework selection to content files, but we've not yet added a project language-specific pivot.

##### MSBuild Imports

Scores of packages modified the project to import an MSBuild targets file. At one point, this was the most common use case of PowerShell, so we added a first-class `build` folder feature that identifies props and targets files that NuGet will automatically import.

##### Advanced Web.Config XML Transforms

A lot of packages needed to make intelligent transformations to web.config that couldn't be expressed in a transform file. We added XDT support to minimize the need for PowerShell in these cases.

##### Native Assemblies

[Microsoft.SqlServer.Compact][5] copies native assemblies into the project and hooks up a post-build event to ensure they're copied into the project's output folder.

##### Tooling and Scaffolding

[EntityFramework][6] exports a PowerShell Module so that cmdlets such as "Add-Migration" can be added into the NuGet PowerShell Console.

##### Opening README Files

At one point the second most common use of PowerShell was to open a README file. We added the recognition of a `README.txt` file in the package and when the package is directly installed, we'll open the file automatically.

We chose to only support TXT files and not HTML or Markdown. Perhaps because of this decision, some package authors still use PowerShell to open rich pages served from their websites. We've already mentioned Newtonsoft.Json, but Glimpse is another interesting example. They have some impressive logic:

1.  Upon new installation, a web page is opened with info about the specific package version that was installed.
2.  Their web page lists some additional Glimpse packages that could be beneficial to you.
3.  Upon package update, they perform some, hmm, clever maneuvers to track the old package version and the new package version, and they open a browser window with [Semantic Release Notes][7] applicable to the update path you just exercised.

#### Filling the Gaps

By adding features for target-framework-specific content files, MSBuild imports, XDT transforms, and README.txt files, we've covered a lot of the scenarios where authors had previously used PowerShell. But it's clear we'll never be able to fill all of the gaps.

### Requiring Visual Studio to Install Packages

I can't count the number of times someone has asked if they can install a package from the command-line, and then complained when they learn the answer is "No." As explained above, NuGet is implemented as though it's a Visual Studio macro. Our Visual Studio extension is where all of the logic exists for making modifications to make to the project, and this has been often criticized.

#### Tightly Coupled to Visual Studio

With the mindset of NuGet simply automating the actions you were taking, it's easy to understand why NuGet is implemented the way it is. The fact is, NuGet was built by a small team as a bolt-on to Visual Studio, allowing package management to be introduced to Visual Studio users with very low risk and no changes to Visual Studio itself.

NuGet uses Visual Studio's [DTE][8] ([Development Time Environment][9]) to automate the actions that you would have taken yourself. This is how assembly references are added, content files are copied into your project, and how MSBuild imports are applied. Even PowerShell scripts use the DTE to perform their custom actions against the projects. Using the DTE, any operations NuGet makes are handled just as though they were initiated manually.

*   When connected to source control, Visual Studio takes care of invoking the necessary actions
*   Project systems handle content files by understanding their file extensions and setting up build actions and custom tools (like the ResXFileCodeGenerator)
*   Project systems handle adding assembly references, generating the hint paths, and writing them into the project file
*   Visual Studio manages the project file in memory so that changes resulting from package operations are coordinated with any other unsaved changes you have

#### Bolt-On Benefits

Using Visual Studio's automation APIs to mimic what manual actions would have done allowed NuGet to get down-stream benefits. No changes to remaining workflows were required, because the result of installing a package matches exactly the result if the actions had been taken manually.

1.  No new concepts are added to your project--other team members could be oblivious and unaffected
2.  Source control integration (generally) works as you expect
3.  No changes were required for your build to keep working (until you opted into Package Restore)
4.  Tools like Web Deploy work just fine and don't need to be aware of the NuGet packages

Had we tried to alter these workflows when NuGet was being introduced, there's no way we would have gotten traction--NuGet 1.0 would never have shipped. And even if it had shipped, it would have been considered too disruptive for a lot of users.

## Harmful Consequences

As a trade-off for the benefits NuGet realized with the bolt-on approach, there are some harmful consequences that resulted from the approach. As users adopt the package management concept in their projects, they then expect to have first-class package management experiences, but NuGet cannot offer them. All NuGet can offer is a package management façade over top of existing Visual Studio and .NET usage.

### Hint Paths in Projects

Because NuGet asks the project system to add references to assemblies from a package, rather than inventing a new mechanism from which the assemblies could be resolved, the project system ends up baking a hint path into the project for where the assembly can be found.

<pre><code class="language-xml"> &lt;Reference Include="Newtonsoft.Json"&gt; 
    &lt;HintPath&gt; ..\packages\Newtonsoft.Json.4.5.11\lib\net40\Newtonsoft.Json.dll&lt;/HintPath&gt; 
  &lt;/Reference&gt; 
  &lt;Reference Include="System.Net.Http"&gt; 
    &lt;Private&gt;True&lt;/Private&gt; 
   &lt;HintPath&gt;..\packages\Microsoft.Net.Http.2.0.30506.0\lib\net40\System.Net.Http.dll&lt;/HintPath&gt; 
  &lt;/Reference&gt;</code></pre>

If you had downloaded [Newtonsoft.Json][2] and [Microsoft.Net.Http][10], expanded them into a `lib` folder, and added references to their assemblies yourself, you would have ended up with virtually the same result as what you got through NuGet here. That's because Visual Studio and MSBuild don't understand the concepts of package at all, so we're just reusing the mechanisms that were understood. But this detail of Hint Paths causes quite a few problems and introduces several limitations.

### Fixed Package Versions

NuGet centralizes packages at the solution-level so that if packages are reused in multiple projects in the solution, only one copy of the package is needed. But since it's possible to have multiple package versions in use across a solution, we need to have the package versions in the paths. That results in the version being coded into the project file and all of the hint paths.

A lot of users would like package references to floating versions where the latest patch, minor, or major version can be used automatically. This would enable package updates to be consumed automatically in your project during development, while allowing you to lock the versions down as you stabilize for deployment. But with package versions embedded in the project file's hint paths, the versions become fixed.

### Frequent Merge Conflicts

On medium to large teams, we hear that merge conlicts arise often because of all of the hint paths and assembly references embedded in the project. Merge conflicts in XML files... gah!

### Global Package Installation

Large packages (especially packages for use in native projects) can take up a good chunk of your solid state hard drive. We've received many requests to support global package installations where the package is only on disk in one place but reused across many solutions. With relative paths to assemblies in the project files, it's virtually impossible to pull off global package installations.

### Multi-Homed Projects

It's pretty common to include a project in mutliple solutions. Unfortunately, NuGet cannot understand when this is the case. And because NuGet uses a `packages` folder at the solution level, the Hint Paths in projects become relative to the solution. If there are multiple solutions using a project, these relative paths can easily break.

### Project Re-Targeting

In the last few years, project re-targeting has become much more common--that is when you need to change the target framework(s) you're building your project for. Mobile platforms and portable class libraries have introduced the need to re-target your project on a somewhat regular basis. But since NuGet performed target framework selection at package installation time, projects end up with Hint Paths specific to the framework(s) that were targeted at the time of installation.

NuGet records into `packages.config` the target framework that was in use when the package was installed. When the project is re-targeted, the tooling can detect which packages need to be reinstalled. Unfortunately, [reinstalling packages][11] is sometimes cumbersome and error-prone; you're left not quite sure what to do.

## Part of the Platform

When NuGet was introduced, we simply bolted it onto the existing Visual Studio and MSBuild. It was the only way to ship and gain initial adoption. Almost 4 years later, we've accomplished those goals and then some. But without being part of the platform, NuGet cannot offer expected package management features to our users.

The good news is that the NuGet, ASP.NET, Visual Studio, CLR, MSBuild, and C++ teams (among others) recognize this. We are all partnering to get NuGet package management adopted into the platform. ASP.NET vNext has been architected around NuGet packages from day one and the benefits are obvious. We're hoping to gain many of the same benefits on other platforms.

### Goals

While ASP.NET vNext has already gained a lot by understanding NuGet packages in its core, making progress in all other project systems is a bit trickier. Nonetheless, here are some of the features we're hoping to unlock with the partnerships:

#### Leave Project Files Alone

Instead of adding assembly references with Hint Paths into projects through the DTE, we want to leave project files alone. This would avoid the XML merge conflicts that arise far too often. Only the package manifest (`packages.config`) would be updated when a package is installed.

#### Automatic Re-Targeting

Rather than choosing the right target framework at package installation time, we want to make the choice at compile time. This would enable automatic re-targeting for packages, so that when you change your target framework, the next compile would automatically use the appropriate assembly references.

#### Global Package Installation

If NuGet is resolving assembly paths at compile time anyway, then the global package installation feature is unblocked. NuGet would be able to look up paths from a global installation folder as well as from a solution-level folder.

We expect we'll search several paths, with the solution-level location having the highest precedence.

#### Multi-Homing

With compile-time reference resolution, we can also better support projects being reused in multiple solutions. We'd no longer have relative hint paths causing problems.

#### Floating Package Versions

We want to allow package references to be configured to automatically pick up new versions within a specified version range, rather than always being fixed to a specific version. When we introduce this, we'd also need to introduce a "lock" mechanism where you could override the floating versions in a separate manifest file, perhaps `packages.lock.config`.

#### Command-Line Installation

If NuGet is no longer modifying project files during package installations, then we could allow package installation from the command-line. Imagine if `nuget.exe install` could just update `packages.config` and the package would be used at the next compile; this could finally be doable.

### Limitations

Many of the benefits we outlined above should work out very well for packages that contain assembly references only. For packages that contain content files, the existing behavior would remain the same. MSBuild imports would continue to work as they do today as well. And PowerShell? Well, we expect we'll keep PowerShell as-is for now, but we know we need to think about this topic some more.

## Next Steps

We've made good progress with our partner teams on all of this, but there's a lot left to do. We'll follow up soon with notes on the designs we believe will accomplish the goal of getting NuGet package management deeper into the platform.

 [1]: http://www.nuget.org
 [2]: http://www.nuget.org/packages/Newtonsoft.Json
 [3]: https://github.com/JamesNK/Newtonsoft.Json/pull/387
 [4]: http://www.nuget.org/packages/jquery
 [5]: http://www.nuget.org/packages/Microsoft.SqlServer.Compact
 [6]: http://www.nuget.org/packages/EntityFramework
 [7]: http://www.semanticreleasenotes.org/
 [8]: http://msdn.microsoft.com/en-us/library/envdte.aspx
 [9]: http://stackoverflow.com/questions/17239760/what-is-the-visual-studio-dte
 [10]: http://www.nuget.org/packages/Microsoft.Net.Http
 [11]: http://docs.nuget.org/docs/workflows/reinstalling-packages