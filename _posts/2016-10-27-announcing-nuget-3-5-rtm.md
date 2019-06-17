---
ID: 115
post_title: Announcing NuGet 3.5 RTM
author: Harikrishna Menon
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/announcing-nuget-3-5-rtm/
published: true
post_date: 2016-10-27 00:00:00
---
NuGet 3.5 RTM for Visual Studio 2015 and nuget.exe provide quality improvements, performance improvements, features and new target frameworks like netstandard and netcoreapp.

## Downloads

All NuGet downloads are available on <https://nuget.org/downloads>. NuGet.exe 3.5 RTM is not marked as the latest yet in the download page or uploaded as a package, so your version of NuGet.exe will not update to 3.5 RTM if you are using the update -self switch. Over the next few weeks once we get some more real world usage of the tools, we will make this change. This blog post will be updated and tweet will be sent out at that time.

> Note: nuget.exe /update -self will not work for a few more days until we push nuget.commandline to nuget.org

*   [3\.5 RTM of nuget.exe][1]

*   [3\.5 RTM of NuGet Package Manager Extension][2]

## No Auto Update

Going forward, for the near future, we will not be automatically updating the NuGet Package Manager Extension in Visual Studio 2015. Instead users will have to download the extension from our [downloads page][3] to update their NuGet extension. This maintains stability and consistent behavior in stable versions of Visual Studio 2015.

You can opt in to auto update or notifications from the gallery by using the beta channel, see details [here][4].

## New Features

### Support for new Target Frameworks

.NET Standard and Net Core App TFM support is now available in 3.5 (since RC) and supports netstandard1.6 TFM for Visual Studio 2015 users. [.NET Standard][5] provides a more concrete guarantee of binary portability to future .NET-capable platforms with an easier-to-understand platform versioning plan.

### PackageType

We have started to lay the groundwork to support a new PackageType property in the nuspec. This property allows package authors to classify their packages (E.g Dependencies, Tools etc..). More information on this is available in the PackageType spec [here][6].

### MinClientVersion support in project.json

Project.json now supports [MinClientVersion][7]. Packages.config scenarios allowed a package to define a required min version of client it can work with. This now works in project.json scenarios as well. You can now version packages that depend on newer NuGet versions so that you can prevent users from running into issues while trying to install packages using older versions of NuGet.

### Performance Improvements

The key performance scenarios we have improved are Restore, Package Manager Console Load and Updating packages.

Here are a few examples:

*   Restore times in portable apps deployed through [Kudu][8] was reduced from over 15 secs to 3 secs.

*   Package Manager Console Load in large solutions are now much faster. In one of our sample projects it reduced from over 132s to 10s.

*   Package Updates in Visual Studio with ReSharper installed has been significantly improved. In our tests we have seen it drop from over 140 secs to 68 secs. We working together with the resharper team on further improvements that require code changes on both sides.

### Better Mono Compatibility

The team has invested in improving compatibility of NuGet.exe and packages with Mono by rolling out a new CI pipeline dedicated to verifying our changes and builds on Mono. We have fixed several issues that were identified during our testing and continue to investigate and fix remaining issues in the next versions of NuGet and Mono.

### SemVer 2.0.0 Client Support

You can now create and publish SemVer 2.0.0 compatible packages using the NuGet Client. This enables the community to create and publish [SemVer 2.0.0][9] compliant packages to their own repositories. We are working on adding support for SemVer 2.0.0 to NuGet.Server and NuGet.org, and this will be made available in a future release.

## Release Notes

[3\.5 RTM][10]

## Issues List

[3\.5 RTM][11]

## What's Next

The team is continuning to focus on transitioning .NET CLI from project.json into MSBuild and and further investments in performance and quality of the product.

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. If you would like share your pain points and your current and future needs, hit us up at <feedback@nuget.org>. You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][12].

 [1]: https://dist.nuget.org/win-x86-commandline/v3.5.0/NuGet.exe
 [2]: https://dist.nuget.org/visualstudio-2015-vsix/v3.5.0/NuGet.Tools.vsix
 [3]: https://nuget.org/downloads
 [4]: http://blog.nuget.org/20160502/Introducing-The-NuGet-Beta-Channel.html
 [5]: https://github.com/dotnet/corefx/blob/master/Documentation/architecture/net-platform-standard.md
 [6]: https://github.com/NuGet/Home/wiki/Package-Type
 [7]: https://docs.nuget.org/create/nuspec-reference
 [8]: https://github.com/projectkudu/kudu/wiki
 [9]: http://semver.org/
 [10]: https://docs.nuget.org/ndocs/Release-Notes/NuGet-3.5-rtm
 [11]: https://github.com/Nuget/home/issues?q=is%3Aissue+milestone%3A%223.5+RTM%22+is%3Aclosed
 [12]: https://github.com/Nuget/Home