---
ID: 167
post_title: NuGet 2.7 Released
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-2-7-released/
published: true
post_date: 2013-08-22 00:00:00
---
The NuGet team is excited to announce that NuGet 2.7 is now released in stable form. We published the release candidate on August 14th and we have not made any changes since then. For details about what's in the release, the [release notes][1] are published on [docs.nuget.org][2].

## NuGet 2.7 Features

1.  Package Restore by Default (with implicit consent)
2.  Automatic Package Restore in Visual Studio
3.  Simplified Package Restore from the Command-Line
4.  Project Retargeting and Upgrade Build Errors/Warnings
5.  NuGet Configuration Defaults
6.  Renaming the Default Package Source
7.  Performance Improvements
8.  New Extensibility APIs
9.  Development-Only Dependencies

Note that with NuGet 2.7, we have removed support for *Visual Studio 2010 Express for Windows Phone* due to technical limitations related to the new automatic package restore. A separate download is available for that version of Visual Studio and it can be found on the [NuGet 2.7 downloads][3] page on CodePlex. Support for *Visual Studio 2010 Express for Web* is still included in the primary extension published to the Visual Studio Extension Gallery.

## Visual Studio 2013

NuGet 2.7 is the version of NuGet that will be included in Visual Studio 2013, with the exception of minor updates such as refreshed localization. When those updates are completed, we will republish NuGet 2.7, but no new features will be added.

## Known Issues

There are two known issues with nuget.exe restore with the 2.7 release, and these will be fixed soon and the nuget.exe download will be updated along with the [NuGet.CommandLine package][4].

1.  [New package restore doesn't work on Mono when using SLN file][5]
2.  [New package restore doesn't work with Wix projects][6]

 [1]: http://docs.nuget.org/docs/release-notes/nuget-2.7
 [2]: http://docs.nuget.org
 [3]: https://nuget.codeplex.com/releases/view/107605
 [4]: http://www.nuget.org/packages/NuGet.CommandLine/
 [5]: https://nuget.codeplex.com/workitem/3596
 [6]: https://nuget.codeplex.com/workitem/3598