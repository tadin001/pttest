---
ID: 226095
post_title: Announcing NuGet 3.5 RC
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/announcing-nuget-3-5-rc/
published: true
post_date: 2016-08-11 00:00:00
---
NuGet 3.5 Beta 2 for Visual Studio 2015, nuget.exe and NuGet 2.12 RTM for Visual Studio 2013 releases provide quality improvements, performance improvements, .NET Core CLI support, and new target frameworks like netstandard and netcoreapp for our users.

## Downloads

All NuGet downloads are available on <http://nuget.org/downloads>.

*   2\.12 RTM version of the Visual Studio 2013 will be available via *Tools -> Extensions and Updates*

*   [3\.5 Beta 2 of nuget.exe][1]

*   3\.5 Beta 2 is available through the following channels.
    
    *   [NuGet Beta Channel][2]
    *   [3\.5 Beta 2 direct download][1].
    *   Integrated with [Microsoft ASP.NET and Web Tools Preview 2][3]

*   The [.NET CLI 3.5 Beta 2][4] dotnet restore command also has been updated to this version.

## New Features

### Support for new Target Frameworks

.NET Standard and Net Core App TFM support is now available for Visual Studio 2012 users via the 2.12 release. 3.5 Beta 2 also now supports netstandard1.6 TFM for Visual Studio 2015 users. [.NET Standard][5] provides a more concrete guarantee of binary portability to future .NET-capable platforms with an easier-to-understand platform versioning plan.

### PackageType

We have started to lay the groundwork for the support for a new PackageType property in nuspec which enables package authors to classify their packages (E.g Dependencies, Tools etc..). More information on this is available in the PackageType spec [here][6].

### MinClientVersion support in project.json

Project.json now supports [MinClientVersion][7]. You can now version packages that depend on newer NuGet versions so that you can prevent users from running into issues while trying to install packages using older versions of NuGet.

## Release Notes

[3\.5 Beta 2][8]

[2\.12 RTM][9]

## Issues List

[3\.5 Beta 2][10]

[2\.12 RTM][11]

## What's Next

The team is now shifting gears into transitioning .NET CLI from project.json into MSBuild and investing in performance and quality of the product.

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. If you would like share your pain points and your current and future needs, use the [calendly link][12] to set up a quick 15-30 min call with us. If you would like to send us an email instead, hit us up at <feedback@nuget.org>.

You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][13].

 [1]: http://nuget.org/downloads
 [2]: http://blog.nuget.org/20160502/Introducing-The-NuGet-Beta-Channel.html
 [3]: https://visualstudiogallery.msdn.microsoft.com/c94a02e9-f2e9-4bad-a952-a63a967e3935
 [4]: http://dot.net
 [5]: https://github.com/dotnet/corefx/blob/master/Documentation/architecture/net-platform-standard.md
 [6]: https://github.com/NuGet/Home/wiki/Package-Type
 [7]: https://docs.nuget.org/create/nuspec-reference
 [8]: https://docs.nuget.org/release-notes/nuget-3.5-beta2
 [9]: https://docs.nuget.org/release-notes/nuget-2.12
 [10]: https://github.com/Nuget/Home/issues?q=is%3Aissue+milestone%3A%223.5+Beta2%22+is%3Aclosed
 [11]: https://github.com/NuGet/Home/issues?q=is%3Aissue+milestone%3A2.12+is%3Aclosed
 [12]: https://calendly.com/harikm/nuget-feedback
 [13]: https://github.com/Nuget/Home