---
ID: 157
post_title: NuGet 2.5 Released
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-2-5-released/
published: true
post_date: 2013-04-25 00:00:00
---
## Download it now

NuGet was included in all editions of Visual Studio 2012 and it's also available for Visual Studio 2010 through the Visual Studio Extension Manager. To get the latest version of NuGet installed in Visual Studio, either check for updates in the extension manager or [download the latest version directly][1].

## Acknowledgements

We would like to thank the following external contributors for their significant contributions to NuGet 2.5:

1.  [Daniel Plaisted (dsplaisted)][2] - Add MonoAndroid, MonoTouch, and MonoMac to the list of known target framework identifiers. [#2847][3]
2.  [Andres G. Aragoneses (knocte)][4] - Fix spelling of NuGet.targets for a case-sensitive OS [#2865][5]
3.  [David Fowler (dfowler)][6] - Make the solution build on Mono.
4.  [Andrew Theken (atheken)][7] - Fix unit tests failing on Mono.
5.  [Olivier Dagenais (OliIsCool)][8] - nuget.exe pack command does not propagate Properties to msbuild [#2920][9]
6.  [Miroslav Bajtos (MiroslavBajtos)][10] - Modified XML handling code to preserve formatting. [#1511][11]
7.  [Adam Ralph (adamralph)][12] - Added recognized words to custom dictionary to allow build.cmd to succeed.
8.  [Bruno Roggeri (broggeri)][13] - Fix unit tests when running in localized VS.
9.  [Gareth Evans (garethevans)][14] - Extracted interface from PackageService
10. [Maxime Brugidou (brugidou)][15] - Handle project dependencies when packing [#936][16]
11. [Xavier Decoster (XavierDecoster)][17] - Support Clear Text Password when storing package source credentials in nuget.cofig files [#2991][18], [#3164][19]

We also appreciate the following individuals for finding bugs with NuGet 2.5 Beta/RC that were approved and fixed before the final release:

1.  [Tony Wall (CodeChief)][20] - MSTest broken with lastest NuGet 2.4 and 2.5 builds [#3200][21]

## What's Included in NuGet 2.5

This release was so big, we felt compelled to skip versions 2.3 and 2.4! To date, this is the largest release we've had for NuGet, with [169 work items][22] in the release.

For details about what's in the release, the [release notes][23] are published on [docs.nuget.org][24].

### Here are the most notable features:

1.  Allow users to overwrite content files that already exist
2.  Automatic import of msbuild targets and props files
3.  Specify different references per platform using <references></references> element
4.  Update All button to allow updating all packages at once
5.  Improved project reference support for NuGet.exe Pack
6.  Add a 'Minimum NuGet Version' property to packages
7.  Dependencies are no longer unnecessarily updated during package installation
8.  NuGet.exe outputs http requests with detailed verbosity
9.  NuGet.exe push now supports UNC and directory sources
10. NuGet.exe supports explicitly-specified Config files
11. Support for Native projects

## NuGet's description

We've outgrown NuGet's current description. According to [nuget.org][25], we have the following description and slogan:

> **Jump Start Your Projects with NuGet**
> 
> NuGet is a Visual Studio extension that makes it easy to install and update third-party libraries and tools in Visual Studio.
> 
> *So install NuGet and get a jump on your next project!*

We have come up with a new description for NuGet:

> NuGet is the package manager for the Microsoft development platform including .NET. The NuGet client tools provide the ability to produce and consume packages. The NuGet Gallery ([nuget.org][25]) is the central package repository used by all package authors and consumers.

You can expect the home page at [nuget.org][25] to reflect this new description soon.

 [1]: http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
 [2]: https://www.codeplex.com/site/users/view/dsplaisted
 [3]: https://nuget.codeplex.com/workitem/2847
 [4]: https://www.codeplex.com/site/users/view/knocte
 [5]: https://nuget.codeplex.com/workitem/2865
 [6]: https://www.codeplex.com/site/users/view/dfowler
 [7]: https://www.codeplex.com/site/users/view/atheken
 [8]: https://www.codeplex.com/site/users/view/OliIsCool
 [9]: https://nuget.codeplex.com/workitem/2920
 [10]: https://www.codeplex.com/site/users/view/MiroslavBajtos
 [11]: https://nuget.codeplex.com/workitem/1511
 [12]: https://www.codeplex.com/site/users/view/adamralph
 [13]: https://www.codeplex.com/site/users/view/broggeri
 [14]: https://www.codeplex.com/site/users/view/garethevans
 [15]: https://www.codeplex.com/site/users/view/brugidou
 [16]: https://nuget.codeplex.com/workitem/936
 [17]: https://www.codeplex.com/site/users/view/XavierDecoster
 [18]: https://nuget.codeplex.com/workitem/2991
 [19]: https://nuget.codeplex.com/workitem/3164
 [20]: https://www.codeplex.com/site/users/view/CodeChief
 [21]: https://nuget.codeplex.com/workitem/3200
 [22]: https://nuget.codeplex.com/workitem/list/advanced?release=NuGet%202.5&status=all
 [23]: http://docs.nuget.org/docs/release-notes/nuget-2.5
 [24]: http://docs.nuget.org
 [25]: http://nuget.org