---
ID: 226047
post_title: >
  Announcing NuGet 3.1 with Support for
  Universal Windows Platform
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/announcing-nuget-3-1-with-support-for-universal-windows-platform/
published: true
post_date: 2015-07-29 00:00:00
---
Today the NuGet team in collaboration with several other teams at Microsoft is happy to announce the release of a new version of the NuGet clients supporting the Universal Windows Platform and the new Portable class libraries. The NuGet tools are available through Tools->Extensions and Updates->Update tab in Visual Studio 2015 as well as from our [GitHub repository][1]. Additionally, we have released a new version of the NuGet command-line tool that you can download from [NuGet.org][2].

In this post, we will review the new capabilities that package authors can use and the process that windows programmers need to follow in order to use NuGet with their projects.

## Introducing Project.Json

Beginning with ASP.NET 5, we introduced support for the project.json file to describe the dependencies of a project with a clear definition of the packages that you immediately depend on. In ASP.NET 5, this is the only file that defines how a project is configured, but with NuGet 3.1, you now use this file in your Universal Windows projects and modern portable class libraries (that target DNX, UWP, and .NET 4.6) to define your package references. The good news about this is that the ‘Manage Packages’ dialog in Visual Studio will maintain your packages.config or project.json file appropriately for you based on the type of project that you are working on.

This shift from the packages.config model also allows us to “reboot” the references in your projects, and use the new transitive dependency capabilities of NuGet. We have heard from developers and package authors that when you add packages to your projects that you end up with a packages.config file that gets polluted with dependencies from the packages you depend on. For example, NHibernate is a package that depends on the Iesi.Collections package. In packages.config, I have two references, NHibernate and Iesi.Collections. When I want to update NHibernate, I now have this question of “do I also update Iesi.Collections”? I also have the opposite problem: if there is an update for Iesi.Collections, do I need to update NHibernate to support the new features in Iesi.Collections? Developers could end up in this ugly cycle of managing their project’s package dependencies that were brought to them through a package reference.

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/cycle.png" alt="The cycle of decisions about updating packages" width="600" height="572" />

The transitive dependencies feature of NuGet abstracts this decision to update package references with improved support for semantic versioning in package definition files (nuspec documents). Package authors have been able to [specify a range of dependency versions that their packages support][3], but when NuGet clients installed those dependencies they added a hard reference to a specific version in the packages.config file and those referenced packages looked like any other package reference that you added to your project. A great example of this problem is the contents of a default ASP.NET MVC application’s packages.config file:

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/mvcPackagesConfig.png" width="600" height="383" alt="ASP.NET MVC default packages.config file" />

All of these things are added to my project, but I really just want Microsoft.AspNet.Mvc, Microsoft.AspNet.Identity.EntityFramework, Bootstrap, jQuery and Modernizr. The other packages referenced by these are noise to me, and I now have hard references to specific versions. With project.json, the versions of these noisy other packages goes away and I’m left with being able to manage just the five libraries that I will actually use in my project. The NuGet client will resolve and manage all of these other packages behind the scenes for you, and keep those references within the constraints of the dependent versions declared by the packages that you declared you are using in your project. This should dramatically simplify the project references experience for developers in their projects.

We also know that developers have a ‘tribe’ of packages and tools that they prefer. Why download and install them multiple times on a single developer workstation when you clearly already have them in project A and want to use them in project B? NuGet now downloads and stores a copy of the packages in a global packages folder located in your `%userprofile%.nugetpackages` folder. This should reduce disk space used on your workstation and prevent extra calls to fetch packages from NuGet.org to get items that you already have on disk.

Project.json support is available for ASP.NET with NuGet 3.0, and for other project types starting with NuGet 3.1 More details about how project.json is used with NuGet can be found in the [NuGet documentation][4]

## Deprecated Features

Starting with NuGet 3.1 when using project.json, we are deprecating support for executing the install.ps1/uninstall.ps1 scripts and delivering elements in the /content folder of packages. Installing packages that have these elements will not execute the install.ps1 file and will not copy content to your project. There are several reasons for these decisions:

#### Note: install.ps1/uninstall.ps1/content are still supported for projects using packages.config, and will be suppported going forward.

*   With transitive package restore picking what to uninstall and install is impossible to do reliably.

*   Similarly content was a mechanism copying content into the user project, when packages are updated there is an implicit uninstall process that cannot be reliably run.

*   NuGet needs to be able to fully support development outside of Visual Studio. With the movement to support a full cross-platform .NET development experience, we know Powershell is not available in other environments that developers want to work on. We also know that more developers are working outside of Visual Studio on .NET code, and we need to support those developers.

*   There are other package managers available that deliver a great experience for managing and delivering content. We know that NuGet is a great package manager for .NET and want to encourage using the right tools for the right job; we believe you should also use those package managers to deliver and manage that content.

*   There is no longer support for the “any” framework, and you can no longer place files directly on the root of the build and lib folders of your package and have them delivered to a project. It is important that you declare which framework(s) your files support so that NuGet knows the priority order to resolve those references.

*   Solution packages are no longer supported, as these packages are not modifying any specific project's capabilities and were typically used to deliver shared resources that were re-used across projects. With the new shared packages folder, these resources may already be on disk from another project.

More details about what features are being deprecated with project.json and recommendations can be found in the [NuGet documentation][4]

## New Target Frameworks

For package authors, another addition with this version of NuGet is support for new development frameworks and improved native package support across operating systems and architectures. This is an exciting development for NuGet, as we reach further outside of the managed .NET framework model to support more ecosystems and empower our package authors to take their libraries to environments they previously could not reach.

Target framework monikers (TFM) are a shorthand code used in the creation of a package to declare which frameworks binaries support and which dependencies are needed for each framework. You will find folder names in the package’s lib and ref folders that use this notation as well as elements in the package’s nuspec dependencies element that declare a targetFramework attribute with one of the TFM values to direct the NuGet client to deliver an appropriate library to a consuming project.

The following TFMs continue to be available for use and the new TFMs being introduced are listed in the following table:

| Description                                                                       | Base Code | Available versions                                                                                                         |
| --------------------------------------------------------------------------------- | --------- | -------------------------------------------------------------------------------------------------------------------------- |
| **Managed framework applications (WinForms, Console Applications, WPF, ASP.NET)** | net       | net11, net20, net35, net35-client, net35-full, net4, net40, net40-client, net40-full, net403, net45, net451, net452, net46 |
| **ASP.NET 5**                                                                     | dnxcore   | dnxcore50                                                                                                                  |
| **Windows Store**                                                                 | netcore   | win8 = netcore45, win81 = netcore451, uap10.0                                                                              |
| **Windows Phone (appx model)**                                                    | wpa       | wpa81                                                                                                                      |
| **Windows Phone (Silverlight)**                                                   | wp        | wp7 = sl3-wp, wp71 = sl4-wp71, sl4-wp, wp8 = wp8-, wp81                                                                    |
| **Silverlight**                                                                   | sl        | sl2, sl3 = sl30, sl4 = sl40, sl5 = sl50                                                                                    |
| **Xamarin**                                                                       |           | mono, MonoMac, Xamarin.Mac, MonoAndroid10, MonoTouch10, Xamarin.iOS10                                                      |
| **Compact Framework**                                                             | net-cf    | net20-cf, net35-cf = cf35, net40-cf                                                                                        |
| **Micro Framework**                                                               | netmf     | netmf41, netmf42, netmf43                                                                                                  |

Those items listed with an equals (=) symbol are synonyms that NuGet supports. That's a lot of support for a lot of different frameworks, and it can get confusing. Do I need to provide support for micro-framework in my managed framework package? How much Silverlight support do I need? These are questions that you as a package author need to determine to best fit the needs of your package consumers.

You will notice that there is no explicit call to support portable class libraries in the table. While those combinations of frameworks are supported, we want you to have a more forward-compatible moniker for modern portable class libraries that will give you greater flexibility in the construction of your packages and definition of the frameworks you support. Starting with NuGet 3.1, we are introducing the dotnet target moniker for modern portable class libraries.

### dotnet Target Moniker

In previous versions of NuGet, package authors could specify the frameworks that a portable class library worked with as a collection of TFM abbreviations joined with plus symbols. You could end up with folder names like “portable-net45+win8+wpa81+wp8” that could be confusing and lead to incompatibility issues for consumers of your package. To make the portable class library and cross-platform development experience easier for package authors, we are introducing the dotnet moniker.

This moniker is not directly tied to any specific version or framework capabilities, but rather is an indirect reference that tells NuGet: “this is the reference you should use if it supports the framework and runtime capabilities that you have”. The NuGet client will then investigate that reference to determine what features and frameworks it supports. This process continues until the NuGet client resolves the exact features supported by the dotnet reference and will then apply it if and only if it matches the features and requirements of your project. The dotnet moniker can be referenced by .NET Framework 4.5 and later derived framework versions including Xamarin Android and Xamarin iOS.

This doesn’t mean that you can simply build a portable class library (PCL), bundle it up with dotnet dependencies declared and be done. If you want to be able to support projects using older versions of Visual Studio and NuGet clients that are building with traditional portable class libraries, you should still create and place a reference to the full PCL target framework moniker.

When installing a package into a project type that is fully compatible with consuming the dotnet moniker (.NET 4.6, UWP, or ASP.NET 5), the dotnet moniker will be sought last, after attempting to find a reference that matches the framework or less specific framework of the project you are working on. This hierarchy walk looks like the following:

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/uapProject.png" alt="UAP dependency resolution" width="600" height="532" />

If your project is a modern PCL using project.json that targets any of these frameworks, and no other framework, then the dotnet moniker will be analyzed first, followed by the standard PCL resolution strategy:

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/pclProject.png" alt="PCL dependency resolution" width="600" height="520" />

More details about how to use the new dotnet moniker with project.json and how it resolves can be found in the [NuGet documentation][5]

## NuGet Command-Line

The command-like executable for NuGet, nuget.exe is now available with support to install, update, and restore packages to a project with either a packages.config or project.json file. The pack command continues to work with nuspec files on disk and packages.config files, but has not been updated to generate a nuspec file based on a project.json file. To work-around this, you will need to craft your own nuspec file for any new package content that you construct with a project.json packages reference. We will issue an update that includes this feature in a future release.

Additionally, this version of the command-line executable provides support for the NuGet.org v3 endpoints. This new version of the nuget.org feed provides faster interactions and is a more reliable service with redundancy built-in and a content-delivery network enabled to assist in quickly delivering packages.

Download a copy of the updated NuGet.exe at: <http://dist.nuget.org/win-x86-commandline/v3.1.0-beta/nuget.exe>

## Upgrade known issue

If you installed Windows 10 SDK/Windows 10 tools after upgrading the NuGet extension, the installer will downgrade the extension back to Version 3.1. You will need to update it again to 3.1.1.

Note: The version showing up in the Extensions and Updates dialog is 3.1.60724.766, and in the powershell console is 3.1.1.0. We plan to unify the version numbers in one of the next releases, by moving to a model where the version numbers are just {Major}.{Minor} and the last two numbers represent roughly the date/time of the build and the build number.

## Summary

These features that support the Windows 10 UWP application development and portable class library projects are now available for package authors and for package consumers. We know that these changes are the first step on broader use of our package manager and .NET framework and will continue to improve that experience. We are continuing to improve the .NET development experience and will be focused on delivering a package manager that supports all .NET developers on any platform building any type of project.

 [1]: https://github.com/nuget/home/releases
 [2]: http://dist.nuget.org/win-x86-commandline/v3.1.0-beta/nuget.exe
 [3]: http://docs.nuget.org/Create/Versioning#constraining-upgrades-to-allowed-versions
 [4]: http://docs.nuget.org/consume/ProjectJson-Intro
 [5]: http://docs.nuget.org/consume/ProjectJson-Dependency