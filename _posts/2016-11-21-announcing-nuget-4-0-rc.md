---
ID: 226104
post_title: Announcing NuGet 4.0 RC
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/announcing-nuget-4-0-rc/
published: true
post_date: 2016-11-21 00:00:00
---
NuGet 4.0 RC for Visual Studio 2017 is focused on adding support for .NET Core scenarios, addressing key customer feedback and improving performance in a variety of scenarios. This release brings several improvements like support for PackageReference, NuGet commands as MSBuild targets, background package restore, and more.

This post gives an overview of the new features and improvements available in NuGet 4.0 RC.

## Downloads

NuGet.exe 4.0 RC will be available for download very soon. This blog post will be updated and we'll tweet about it at that time.

### NuGet Package Manager Extension in Visual Studio 2017

Starting with NuGet 4.0 in Visual Studio 2017, the NuGet Package Manager will be shipped as a part of Visual Studio, and newer versions will not be available for download from the VS extensions gallery. NuGet updates will be pulled in automatically along with other Visual Studio updates.

NuGet has grown to be a core part of the Visual Studio environment, and going forward, we want to treat NuGet just like any other platform component and maintain a high bar for quality and compatibility. With rapid Visual Studio releases, we will continue to bring new experiences and improvements to you quickly. You can continue to get Preview builds of NuGet if you have opted into the [NuGet beta channel][1].

The NuGet 4.0 Package Manager Extension is currently not available for Visual Studio 2015. We will send out an update when it is made available. Since NuGet 4.0 builds upon several new features and bug fixes in Visual Studio 2017, not all NuGet 4.0 features will be available in Visual Studio 2015.

## New Features

### PackageReference

[PackageReference][2] will be the standard way to manage NuGet dependencies across all project types. With NuGet 4.0 RC, PackageReference is fully supported for .NET Core. This allows you to use MSBuild conditions to choose package references per target framework, configuration, platform, or other pivots. It also allows for fine-grained control over dependencies and content flow. Here is an example of how you would add a PackageReference in your MSBuild-based project file:

    <ItemGroup> 
     ... 
       <PackageReference Include="Contoso.Utility.UsefulStuff"> 
         <Version>3.6.0</Version> 
       </PackageReference> 
    ... 
    </ItemGroup>
    

In NuGet 4.0 RTM that will coincide with Visual Studio 2017 RTM, we aim to fully support PackageReference for other project types such as WinForms, Windows Presentation Foundation (WPF), and Universal Windows Platform (UWP).

### First class MSBuild citizen

In the past, it was a little tricky to make NuGet work with MSBuild. In this release, we are shipping Restore and Pack as first class MSBuild targets. This will allow you to easily work with NuGet as you would with any task or target in MSBuild.

*   You can now build packages directly from a project. For example, you can do `msbuild /t:pack` in a project directory which would generate a NuGet package using the properties and metadata declared in the csproj file of the project.
*   In CI systems, you no longer need to download nuget.exe and run `nuget restore` to restore packages. You can now use `msbuild /t:restore` to restore packages.

### Background Package Restore

In the past, you had to perform a build or an explicit restore to restore NuGet packages. With NuGet 4.0, background package restore automatically downloads and adds or removes NuGet packages as you edit PackageReference and save your project files.

### New commands in the .NET CLI

We have added new commands for the .NET CLI - `dotnet nuget locals`, `dotnet nuget push`, and `dotnet nuget delete`. dotnet nuget locals allows you to manipulate and work with the nuget caches on your machine. dotnet nuget push / delete – enables pushing packages to or deleting packages from NuGet servers. Additionally, dotnet restore and dotnet pack now have the same behavior as MSBuild restore and MSBuild pack providing a consistent and reliable experience.

`dotnet restore` == `msbuild /t:restore`

`dotnet pack` == `msbuild /t:pack`

### Performance Improvements

The following are some performance improvements that you might notice with NuGet 4.0:

*   nuget update for UWP, WPF and other project.json based projects is now much faster. In our sample solution with 20 UWP projects, it now takes about 1.2 mins for updating packages, where previously it would have taken about 7.5 mins to update 3 packages. For packages.config based projects, nuget update is now about 20% faster.
*   nuget restore for UWP, WPF and other project.json based projects has been improved. In one of our sample projects, restore times have been reduced from about 3800ms to about 400ms.
*   nuget update/install for packages that have deep dependencies is now faster by an order of magnitude. For a sample scenario with 5 targets each having 20-30 deep dependency chains, update/install used to take about 10 mins to complete, compared to about 30ms now.
*   In .NET Core, tool restores have been optimized so that tool references are restored only once per solution instead of once for every project.

## Breaking Changes

### Default Location for the machine-wide NuGet.config

In Visual Studio 2017 and above, the machine-wide NuGet.config is located at `%ProgramFiles(x86)%NuGetConfig`. Going forward, nuget.exe v4.0.0+ will also treat this as the new location for the machine-wide configuration. The primary driver for the change is to improve security in a multi-user scenario. Previously we would write to the `%ProgramData%` folders which don’t require Admin privileges to modify. `%ProgramFiles(x86)%` folders are protected and only users with Administrative privileges, or those granted permissions by an administrator can change their contents.

*   Impact - NuGet.config in `%ProgramData%NuGetConfig` will no longer be implicitly referenced or considered for hierarchical merging of NuGet.config. This is only applicable if you were previously using machine-wide NuGet configuration files.
*   Solution - You must manually migrate existing config files from `%ProgramData%` to `%ProgramFiles(x86)%`

## Release Notes

[4\.0 RC][3]

## Issues List

Issues fixed in 4.0 RC can be found [here][4] and [here][5]

## What's Next

The team is going to focus on finalizing .NET Core support, enabling PackageReference support for all project types, and making further improvements to the performance and quality of the product.

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. If you would like to share your pain points, and your current or future needs, hit us up at <feedback@nuget.org>. You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][6].

 [1]: http://blog.nuget.org/20160502/Introducing-The-NuGet-Beta-Channel.html
 [2]: https://docs.nuget.org/ndocs/consume-packages/package-references-in-project-files
 [3]: https://docs.nuget.org/ndocs/Release-Notes/NuGet-4.0-RC
 [4]: https://github.com/Nuget/home/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%224.0+RC%22
 [5]: https://github.com/Nuget/home/issues?q=is%3Aissue+is%3Aclosed+milestone%3A%224.0+Beta1%22
 [6]: https://github.com/Nuget/Home