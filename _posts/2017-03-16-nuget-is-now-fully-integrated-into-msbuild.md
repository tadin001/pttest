---
ID: 226114
post_title: >
  NuGet is now fully integrated into
  MSBuild
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-is-now-fully-integrated-into-msbuild/
published: true
post_date: 2017-03-16 00:00:00
---
In the past, NuGet packages were managed in two different ways - packages.config and project.json - each with their own sets of advantages and limitations. With Visual Studio 2017 and .NET Core, we have improved the NuGet package management experience by introducing the PackageReference feature in MSBuild. PackageReference brings new and improved capabilities such as deep MSBuild integration, improved performance for everyday tasks such as install and restore, multi-targeting and more.

## Try out PackageReference today!

To get a piece of the PackageReference goodness, [download Visual Studio 2017][1] and create a .NET Standard class library or .NET Core project. These projects come with PackageReference enabled by default. You can also [try PackageReference with other project types][2] .

Install your favorite NuGet packages, try out different scenarios and workflows, and if you run into an issue or find something broken, [open an issue on GitHub][3].

## PackageReference – the one NuGet format to rule them all

### Manage all project dependencies in one place

Just like project to project references or assembly references, you can now view and manage NuGet package references from the MSBuild project file (e.g. csproj).

![pr-in-action][4]

### See only those dependencies you care about

In the past, if your project referenced package A, which in-turn referenced packages B, C and D, you would see all of them listed as your dependencies. With Transitive Package Restore, NuGet dynamically resolves dependencies giving you an uncluttered view of the packages you care about. Additionally, project files are not modified on restore, avoiding merge conflicts and file churn on commits. This also allows the project system to evolve independent of NuGet.

### Performance improvements

Package installs/updates are now at least 5x faster. With PackageReference and Transitive Package Restore, we no longer stamp every down-level dependency into the project file. In a test project that references 5 packages, with each package having 20-30 dependencies of their own, update/install used to take ~10 mins to complete. With these new features, our tests show that these operations now take ~30ms.

Solution-local packages folders are no longer used – Packages are now resolved against the user’s cache at `%userdata%&#046;nuget`, rather than a solution specific packages folder. This makes PackageReference perform faster and consume less disk space by using a shared folder of packages on your workstation.

### Fine control over dependencies and content flow

*   With the existing features of MSBuild, you can conditionally reference a NuGet package. This allows you to choose package references per target framework, configuration, platform, or other pivots. For example: 

*   You might be using a dependency purely as a development harness and might not want to expose that to projects that will consume your package. In this scenario, you can use the PrivateAssets metadata to control this behavior. For example: All 

*   You can control the flow of dependency assets using Include/Exclude Assets metadata. In the following example, everything except the content files from the package would be consumed by the project and everything except content files and analyzers would flow to the parent project. All contentFiles contentFiles;analyzers 

*   You now have flexibilty to express dependency versions using either version ranges or floating versions. In the first example, using version range, you can restrict the package version range which NuGet accepts from 1.3.2 up to 1.4.x, but not 1.5 and higher. In the second example, using floating version, NuGet will always bring the latest version of the ExamplePackage that matches the pattern 1.4.x. 
    
         <PackageReference Include ="ExamplePackage" version="1.4.*"/>
        

### Define nuspec Metadata in project files

You can now author NuGet packages by specifying NuGet properties in your project file. You can also define these properties from the project properties. Additionally, you can choose to auto-generate a NuGet package on every build by checking the “Generate NuGet package on build” property.

![nuspecprops][5]

### Build packages directly from a project

Pack and Restore are msbuild targets, making NuGet a first class MSBuild citizen. For example, you can execute `msbuild /t:pack` in a project directory to generate a NuGet package using the properties and metadata declared in the project file. In a CI/CD scenario, NuGet error and warnings are collated with the MSBuild output, giving you a single view of your build output.

### Background Package Restore

In prior configurations, you had to perform a build or an explicit restore to restore NuGet packages. With NuGet 4.0, background package restore automatically downloads and adds or removes NuGet packages as you edit PackageReference and save your project files.

### Package project duality

As a package author, you can now expect the exact same behavior when referencing a library either as a Project to Project reference or as NuGet package. This streamlines your inner loop when authoring NuGet packages as you no longer need to go through the change-build-pack-restore loop to test a change.

### Develop against multiple TFMs

You can now specify multiple target frameworks (TFMs) and at pack time, NuGet will do the right thing to create a nupkg with the correct package structure. For example:

    <PropertyGroup> <TargetFrameworks>netstandard1.4;net452;netcoreapp1.0</TargetFrameworks> </PropertyGroup>
    

## What about other project types that are not .NET Core?

You can get a first look of what will come in the next NuGet update. Hop on to the [Visual Studio 2017 Update 1 Preview 2][6] bandwagon. Once you have the VS 2017 U1 Preview 2, you can try PackageReference with any packages.config based project. Navigate to `Tools > Options> NuGet Package Manager > General` and check the “Allow format selection on first package install” option.

![trypackageref][7]

Create a new packages.config based project and attempt to install a NuGet package. You will be prompted by the dialog below. Select the PackageReference option and click ok.

![nuget-format-selection-dialog][8]

Some of the features discussed earlier are not yet fully supported for packages.config based projects such as defining nuspec metadata in project files, building packages directly from a project, background package restore, developing against multiple TFMs, and package project duality. We are hard at work to bring full PackageReference support to these project types. Our goal is to eventually make PackageReference the default and move away from all other formats.

## Will existing NuGet package management formats continue to work?

The PackageReference feature is only supported in Visual Studio 2017. We remain fully committed to maintaining compatibility of existing projects created with Visual Studio 2015 in Visual Studio 2017. Your packages.config and project.json managed projects will continue to work in both versions of Visual Studio.

## Will existing NuGet packages work if I am using PackageReference for WPF, Windows Forms or ASP.NET projects?

While we have done a lot of work to ensure that existing packages on NuGet.org just work with the PackageReference format, there are a few scenarios that will not be supported in the PackageReference world and might impact your ability to migrate away from packages.config. Some examples of scenarios that will not be supported include content folders (we have introduced [ContentFiles][9]), XDT transforms, PowerShell scripts i.e. install.ps1 and uninstall.ps1 (only init.ps1 is supported) . If you come across a package you consume in your WPF or Windows Forms app that worked previously but does not work on migrating your project to PackageReference, we would love to hear from you, so that we can investigate what the incompatibilities might be.

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. If you would like to share your pain points, and your current or future needs, hit us up at [feedback@NuGet.org][10]. You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][3].

 [1]: https://www.visualstudio.com/vs/whatsnew/
 [2]: #what-about-other-project-types-that-are-not-net-core
 [3]: https://github.com/Nuget/Home/issues
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/pr-in-action.png
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/nuspecprops.gif
 [6]: https://blogs.msdn.microsoft.com/visualstudio/2017/03/16/visual-studio-2017-update-preview-and-windows-10-creators-update-sdk/
 [7]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/trypackageref.gif
 [8]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/nuget-format-selection-dialog.png
 [9]: https://docs.microsoft.com/en-us/nuget/schema/nuspec#including-content-files
 [10]: mailto:feedback@nuget.org