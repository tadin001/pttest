---
ID: 226026
post_title: 'NuGet:  In the Platform'
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-in-the-platform/
published: true
post_date: 2014-10-14 00:00:00
---
I recently declared that NuGet is "[Broken By Design][1]." Now, that was hyperbolic; I don't really think NuGet is broken. In fact, I'm very pleased with NuGet (and proud of it). But I wanted to make the point that NuGet's approach both earned it success but also came at a cost. NuGet can't presently offer some expected package management features because it's merely a package management façade over top of existing Visual Studio and MSBuild capabilities.

I also wanted you all to know that we recognize that NuGet has been stagnating and that we want to make some changes.

## The Right Approach?

For years, many have questioned the approach NuGet took bolting onto Visual Studio and MSBuild. Knowing the approach has led to being unable to provide some key new features, was it still the right approach?

**Absolutely!**

I whole-heartedly believe that the bolt-on approach that NuGet took directly led to its adoption and success. We do not at all regret the direction we went.

## Time To Change

Now that NuGet has gained broad adoption and our users are demanding actual *package management* features, it's time to change. As mentioned in the previous post, our partner teams across ASP.NET, Visual Studio, .NET (CLR), MSBuild, and C++ recognize this too. Their users are also demanding package management features, through NuGet.

Only because our collective users are demanding new features do we think we can now make NuGet part of the platform. Putting package management into the platform will result in some changes to how users build projects for the Microsoft development platform. Until recently, we didn't have the feedback indicating that our users were ready to accept the changes required; we now believe some disruptive changes will be welcomed.

## Problems To Fix

In addition to the package management workflows that we cannot presently improve, there are some concrete problems our users face as a result of package management not being a fundamental project concept. Here are some of the problems we aim to address.

### Re-Targeting and Multi-Targeting

Because project files end up with Hint Paths specific to target frameworks selected at package installation time, re-targeting your project can cause the Hint Paths to be stale. Additionally, you cannot multi-target the project by passing the target framework as a parameter to MSBuild, because the framework-specific Hint Paths are burned in for a single framework.

### Package Dependencies From Project References

Consider the following scenario:

![Multi-Headed Apps][2]

In this scenario, when you build the Windows Store App, MSBuild will detect the project reference to the Portable Class Library, and it will copy assemblies referenced by that library into the Windows Store App's output folder. The same will happen for the Windows Phone App. Unfortunately, the Portable Class Library will get the NuGet package's portable reference assembly rather than the Windows Store or Windows Phone implementation assemblies.

To make this scenario work, MSBuild will ensure NuGet selects the libraries in the context of the consuming project, regardless of whether the package is a direct or indirect dependency.

### New Build Artifact Types and Pivots

NuGet presently understands managed assemblies and getting references added to them. You can include assemblies in your package that don't get references, but the capabilities are quite limited. Furthermore, NuGet presently supports only pivoting your assembly selection by Target Framework/Version.

With NuGet packages being a fundamental concept, the .NET team wants to ship more and more of their work as NuGet packages. And the C++ team wants to be able to use NuGet packages for their libraries as well. In order to satisfy the requirements that come in these scenarios, NuGet must allow new artifact types to be expressed in packages so that those artifacts can be exposed to the build.

We are still working on the design for this, but we anticipate NuGet packages being able to express artifact groups that can pivot on the following properties:

1.  Architecture
2.  Configuration
3.  Reference vs. Copy-Local
4.  Language
5.  Design-Time vs. Runtime

For those of you familiar with [Extension SDKs][3], you'll notice the similarity. We plan to implement this as though NuGet could support *arbitrary* pivots, but we know of concrete scenarios for the properties listed above.

## NuGet Will Gain a First-Class Presence

What needs to change to make all of this happen? What does "putting package management into the platform" mean? Why is it disruptive? What is going to change?

**We plan to change Visual Studio and MSBuild to understand the concept of package management; NuGet will gain a first-class presence.** Here are some of the areas and features that will be affected:

### MSBuild

We are working on a design where MSBuild, for all project types, will understand that projects can have NuGet packages installed. MSBuild will understand that the installed NuGet packages can have assemblies and other artifacts that need to be included in the build, and that build and environment properties can alter which artifacts need to be included.

We expect the design will arrive at a workflow along these lines:

1.  An MSBuild target will call into NuGet to get the project's package manifest (as id/version pairs)
2.  MSBuild will also get the package manifests for indirect package references (through project references)
3.  An MSBuild target will call into NuGet to get the artifacts (references and other items) for each of the packages, respecting the project and environment properties for artifact selection

This approach is very similar to what [ASP.NET vNext][4] is already doing.

#### Package Manifest

NuGet will do the work of reading our `packages.config` file and returning the id/version pairs of packages that are installed. It's important to note that MSBuild itself *will not know* how to load and parse the package manifest from `packages.config`--only NuGet will. This allows us to add future features that change or extend the logic for the package manifest.

Here is some of our thinking for future features that could be introduced.

##### Direct vs. Indirect Dependencies

We could change `packages.config` to only include direct dependencies, rather than packages that are brought in as indirect dependencies. We would then perform dependency resolution at compile time when MSBuild queries NuGet for the list of installed packages.

We expect to add an annotation to our package manifest to indicate whether the list contains the full closure or just the direct dependencies. You'd then be able to control whether you want to resolve dependencies at package installation time and write them into the manifest, or resolve dependencies at compile time.

##### Floating Package Versions

Much like resolving dependencies at compile time, we could also choose package versions at compile time. Your package manifest could contain version ranges such as `[2.4.0,2.5.0)` so that the newest patch version of 2.4.x could be selected on each build.

While this feature is frequently requested, it can cause you some headaches too; it would be an opt-in feature.

##### Manifest Locking

For floating versions and compile-time dependency resolution, it would be important to allow you to override the variability of resolution, locking the build down to specific packages/versions when desired. We expect to add support for a `packages.lock.config` file that would take precedence over `packages.config` when NuGet is looking up the package manifest.

With this approach, you could easily commit a `lock` file into a release branch, but leave your development branch open. Of course, NuGet.exe would offer a feature such as `nuget.exe lock` where the resolution would execute and a `packages.lock.config` file would be written out for you.

##### JSON-Formatted Package Manifest

A lot of users have requested that we move away from the XML-formatted `packages.config` file, and instead adopt JSON for our package manifest. With MSBuild querying into NuGet for the list of packages installed, we leave the door open for making this switch. And it's quite possible that instead of determining how we'll extend `packages.config` to support the new features of direct vs. indirect dependencies and floating package versions, we'll instead create a `nuget.json` file that has these concepts. We're even considering a merger of our `nuspec` file and this package manifest, along with potentially aligning with [ASP.NET vNext's `project.json` file][5].

If we go down the path of using a `nuget.json` file, we'd of course then use a `nuget.lock.json` file for the manifest locking.

##### Single Source of Truth

Today, projects that consume NuGet packages have at least 2, and sometimes 3 "sources of truth." In any system, having multiple sources of truth is just asking for trouble. Consider the following packages **Microsoft.AspNet.SignalR.Core** and **Microsoft.AspNet.SignalR.Redis**:

**Microsoft.AspNet.SignalR.Redis** depends on **Microsoft.AspNet.SignalR.Core**

Here is the layout of files that NuGet is managing package reference information in:

*   Microsoft.AspNet.SignalR.Core / 
    *   Microsoft.AspNet.SignalR.Core.csproj
    *   Microsoft.AspNet.SignalR.Core.nuspec
    *   packages.config
*   Microsoft.AspNet.SignalR.Redis / 
    *   Microsoft.AspNet.SignalR.Redis.csproj
    *   Microsoft.AspNet.SignalR.Redis.nuspec
    *   packages.config

There is duplication in each of the files. The `nuspec` has direct dependencies, the `packages.config` has the closure of packages (direct and indirect dependencies), and the `csproj` has the assemblies referenced as a result of installing the packages. The `nuspec` might have other things that differ from the `packages.config`, for example version ranges, extra files that should be copied into the package, extra metadata like release notes, project url etc.

We want to consolidate this package reference information into one place. Here's a potential approach for doing so:

1.  Completely eliminate assembly references (that came from packages) in the `csproj` file
2.  Combine the "package manifest" concept in the `packages.config` file with the "package authoring" metadata in the `nuspec` file
3.  Use this one file, presumably `nuget.json`, to control the package manifest and package authoring
4.  Determine the assembly references (that came from packages) at compile time

We considered putting the package manifest directly into the `csproj` file, as `<NuGetPackageReference>` items, but that would come with a few drawbacks:

1.  Installing a NuGet package would still result in updating the `csproj` file, which must be done through the Visual Studio project systems
2.  We'd still be married to the [DTE][6] because NuGet should not manipulate project files directly
3.  ASP.NET vNext, which doesn't use `csproj` files, would still need a different concept
4.  Problems like XML merge conflicts would remain
5.  It would be more difficult to implement tooling outside of MSBuild

For these reasons, and to enable the ideal situation of NuGet not needing to update `csproj` files *at all* when a package simply contains assemblies, we concluded it's far better for NuGet's package manifest to be separate from the project file itself. Of course, the separate package manifest should help with cross-platform scenarios too, which should help [Xamarin][7] out quite a bit.

#### Down-Level Support

We haven't figured out all of the details for how this will be implemented, or if/how we can update previous versions of MSBuild to support NuGet in this way. We don't expect the changes will be terribly invasive though, and we expect to use existing extensibility points in MSBuild to light this up. If we succeed therein, then down-level support should be possible. But it is quite likely down-level MSBuild support will land out of scope at first, so we might have to be creative.

#### Project Transitioning

We haven't yet worked out the details for how you'll transition your project over into the new "build-enlighted" NuGet package consumption mode. There will certainly need to be some sort of gesture to convert, but we don't know what it will look like yet.

### Visual Studio

Several changes are being made in Visual Studio to make NuGet a first-class citizen in the environment.

#### Package Management UI

During the Visual Studio "14" timeframe, the NuGet team is designing a brand-new package management user interface. Our 4-year-old modal dialog that inherits from the Extensions and Updates dialog is getting completely replaced. Our new user interface is a document window that *behaves* similarly to the Project Properties window, although it *looks* completely different.

![New Package Management UI][8]

This new UI is part of **NuGet 3.0** and it will be available for **Visual Studio 2012**, **Visual Studio 2013**, and **Visual Studio "14"**. As we've previously announced, we are going to [discontinue updates for Visual Studio 2010][9], so this new UI won't be available there.

Here are some of the new features available in the new user experience:

1.  Consolidated Installed/Online/Updates view, with uninstall/install/update actions together in one place
2.  Filters for viewing only installed packages or packages that have updates available, replacing the previously separate views
3.  Version selection for package installs and updates within the UI (instead of having to drop down to PowerShell)
4.  A preview feature that shows what packages will be uninstalled, installed, and left as-is for the selected action
5.  Control over the dependency version selection
6.  Ability to open NuGet Package Management windows for multiple projects at once
7.  Package version consolidation across multiple projects
8.  Improved performance and reliability when connected to nuget.org (using a new client/server protocol)
9.  Better use of screen real estate and the ability to leave the window open

After NuGet 3.0, we hope to update the UI again to show what artifact types the NuGet package carries. For instance, you could see ahead of time if the package has PowerShell scripts, web.config transforms, or other content files, or if it merely has assembly references. We could even add in installation options where you could disable the execution of the PowerShell scripts or the web.config transforms if desired.

#### Solution Explorer

When NuGet was merely bolting onto existing project systems' capabilities, that resulted in the assemblies from packages being added into a project's traditional `References` node; there were no actual NuGet package reference entries.

While we're not sure when the feature will land, we are going to work with Visual Studio to get NuGet packages to show up as nodes in Solution Explorer. These nodes will provide some context menu items for common actions such as uninstalling and updating packages, which will navigate into the package management UI with the package and action selected.

You'll see this integration first in ASP.NET vNext projects, and then later across all other project systems.

### NuGet In New Domains and Partnerships

In addition to this big drive to get NuGet into the Microsoft development platform, we've also recently seen a huge increase in the number of new domains where NuGet is being used as an implementation detail or as a feature within new products. [Chocolatey][10] has been around forever, but with the introduction of [OneGet][11], it's gaining more momentum. Additionally, systems such as [Octopus Deploy][12], [MyGet][13], [AppVeyor][14], [scriptcs][15], and many others have come to NuGet with requests to make our server and client bits more flexible for their scenarios.

While we are working on getting NuGet into the platform, we're also evaluating how much we should generalize NuGet packaging, libraries, and feeds to serve the needs of the new domains and partnerships. Those topics all fall much lower in priority, but it remains on our minds as we make significant code changes.

## Lots To Do

We have a lot to do to make all of this happen. We hope you like the plans and that you'll share your feedback with us eiher way. You have been very patient with us as we waited for the time to be right to change the general approach NuGet took from being a bolt-on to being adopted into the platform, and we do appreciate your patience.

As you can probably imagine, all of this work has the NuGet team pretty well consumed. We've fallen behind in reviewing pull requests, issues, discussions, and other forums, and our release cadence has also slowed down significantly. Please bear with us as we remain focused on making big changes. If you'd like to get more involved in the project to help us get through this transition, please let us know how you'd like to help.

 [1]: /20141010/nuget-is-broken.html
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/multi-headed-app.png
 [3]: http://msdn.microsoft.com/en-us/library/hh768146.aspx
 [4]: http://www.github.com/aspnet/home
 [5]: https://github.com/aspnet/Home/wiki/Project.json-file
 [6]: http://msdn.microsoft.com/en-us/library/envdte.aspx
 [7]: http://blog.xamarin.com/xamarin-studio-and-nuget/
 [8]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/new-ui.png
 [9]: /20141002/visual-studio-2010.html
 [10]: http://www.chocolatey.org
 [11]: https://github.com/OneGet/oneget/wiki/Q-and-A
 [12]: http://octopusdeploy.com/
 [13]: http://www.myget.org
 [14]: http://www.appveyor.com/
 [15]: http://scriptcs.net/