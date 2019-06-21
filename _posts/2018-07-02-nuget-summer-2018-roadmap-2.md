---
ID: 1026
post_title: NuGet Summer 2018 Roadmap
author: Serhii Rohatin
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-summer-2018-roadmap-2/
published: true
post_date: 2018-07-02 00:00:00
---
This blog post provides insights into the NuGet team plans for the upcoming quarter (July - Sep 2018). In the [March 2018 NuGet Spring 2018 Roadmap][1], we had outlined Package Signing, Organizations, Cross-platform credential provider support, Repeatable builds for PackageReference based projects, etc. as our immediate priorities. We were able to complete much of this work over the past few months and have made good progress on others. In this blog post, I would like to summarize our progress and share our plans for the next quarter.

## Looking back

Here is a quick summary of various experiences that we enabled over the last quarter.

### Package Signing - Signing and publishing signed packages

*Author signing - Status: [Implemented][2] | NuGet.org (repository) signing - Status: [In progress][3]*

We enabled authors to sign their packages, publish them to nuget.org and consume them from various NuGet clients. In this quarter, we plan to sign (or counter-sign already author signed) all packages with the [NuGet.org repo signature][3]. Once implemented, it will help ensure package integrity for all packages (signed or unsigned by their authors) from the time the package was uploaded to nuget.org to when it is consumed on the developer machine.

In upcoming Visual Studio releases, we will also add the ability to configure environments to enforce [various levels of package signing][4]. Note that we only support validation of packages signatures in Visual Studio 2017 Update 6 or later, with older clients ignoring the signature validation step. Support for signature validation for other NuGet clients such as `dotnet.exe` will come in the future.

### Two-factor authentication

*Status: [Implemented][5]*

[nuget.org now requires either MSA or AAD to sign in][6] and has fully transitioned away from NuGet.org’s home-grown authentication mechanism. We have also enabled, and strongly recommend, using two-factor authentication (2-FA) to sign in to nuget.org.

### Organizations

*Status: [Implemented][7]*

Organizations allow multiple users to manage the same set of packages. With *Administrator* and *Collaborator* roles combined with audit history of which member updated the packages, you now have better control and management of the packages owned by a team or group. [Learn more about Organizations on nuget.org][7]

### PackageReference migration tool

*Status: [Implemented][8]*

With Visual Studio Version 15.7, we introduced the ability to migrate existing projects that use the `packages.config` format to use `PackageReference`. While this functionality will let users quickly migrate to the new package format and leverage its benefits, we plan to make several improvements to address top feedback on `PackageReference` over the next quarter.

## Upcoming experiences

### Cross-platform credential provider support

*Status: [In progress][9]*

If you were to use an authenticated package feed like [VS Team Services Package Management][10] with Visual Studio or the NuGet CLI (including dotnet.exe and msbuild.exe), there is no easy way to restore packages on Linux or Mac. Solution such as specifying Personal Access Tokens or API keys in plain text in your `nuget.config` file are not good security practices. To address this limitation, we plan to build cross platform support for credential providers similar to the Windows experience.

### Improved debugging and symbols support for packages

*Status: [In progress][11]*

With a fast growing .NET ecosystem, we need to streamline the NuGet package debugging experience. Developers should be able to get meaningful debug information when consuming NuGet packages, and for open-source projects, they should even be able to step into the code without the need to clone a repository. We want to enable integrated debugging and source-stepping experiences for all NuGet package consumers.

### Improve validation times on package submissions

*Status: [In progress][12]*

In December 2017, we changed the NuGet.org [publishing pipeline to introduce a set of validation steps for packages][13] that resulted in longer turnaround times before packages were available for consumption. Our goal is to make packages available with a similar turnaround time as before this change. Over the last quarter, we were able reduce the overall validation time by cutting down the time it takes to index packages by half. We plan to reduce the overall validation time further this quarter.

### Enable repeatable builds for PackageReference based projects

*Status: [In progress][14]*

Projects that use PackageReference to manage NuGet dependencies only specify direct package dependencies. The transitive closure for the dependencies happen during restore resulting in potentially different build outputs for consecutive builds (for select corner cases). We plan to address this issue so that repetitive restores (builds) are consistent no matter when and where they happen.

### Deprecate external URLs for packages

*Status: [Planned][15] | Github issue(s): [#352][16], [#4628][17]*

We want to further improve support for portability and immutability of NuGet packages by allowing authors to embed icons, licenses and documentation within packages. The NuGet clients will also be updated to respect such content. Finally, we will encourage package authors to stop using external content URLs inside NuGet package specifications.

### Deprecate vulnerable, legacy packages

*Status: [Planned][18] | Github issue(s): [#2867][19]*

As the number of packages grows on nuget.org, packages start getting obsolete either because they are no longer maintained or because there were some issues with them (security, privacy, etc.). Though nuget.org has the ability to hide (unlist) packages, we want to improve this experience by enabling authors to explicitly deprecate packages with specific reasons. We also want to add the ability for package authors to suggest alternatives to consumers.

## We want to hear your feedback!

We would love to hear your feedback on these items, or any additional items you think we should be prioritizing. You can reach out to us either by [creating a new GitHub issue][20] or by tagging [@nuget][21] in your tweets. You can also email me at <anangaur@microsoft.com> or tag me - [@adgrv][22] in your tweets. We will be sure to announce any changes or updates to this plan on our [NuGet/Announcements][23] repo.

 [1]: https://blog.nuget.org/20180301/NuGet-Spring-2018-Roadmap.html
 [2]: https://github.com/NuGet/Announcements/issues/6
 [3]: https://github.com/NuGet/Announcements/issues/15
 [4]: https://github.com/NuGet/Home/wiki/%5BSpec%5D-NuGet-Package-Signing-Client-Policy
 [5]: https://github.com/NuGet/Announcements/issues/3
 [6]: https://blog.nuget.org/20180515/NuGet.org-will-only-support-MSA-AAD-starting-June.html
 [7]: https://docs.microsoft.com/en-us/nuget/reference/organizations-on-nuget-org
 [8]: https://docs.microsoft.com/en-us/nuget/reference/migrate-packages-config-to-package-reference
 [9]: https://aka.ms/nuget-xplat-auth
 [10]: https://www.visualstudio.com/team-services/package-management
 [11]: https://github.com/NuGet/Home/wiki/NuGet-Package-Debugging-&-Symbols-Improvements
 [12]: https://github.com/NuGet/NuGetGallery/issues/5560
 [13]: https://blog.nuget.org/20180201/NuGet-package-publishing-workflow-behind-the-scenes.html
 [14]: https://github.com/NuGet/Home/wiki/Enable-repeatable-package-restore-using-lock-file
 [15]: https://github.com/NuGet/Home/wiki/Deprecate-external-content-URLs-for-packages
 [16]: https://github.com/NuGet/Home/issues/352
 [17]: https://github.com/NuGet/Home/issues/4628
 [18]: https://github.com/NuGet/Home/wiki/Deprecate-packages
 [19]: https://github.com/NuGet/Home/issues/2867
 [20]: https://github.com/NuGet/Home/issues/new
 [21]: https://twitter.com/nuget
 [22]: https://twitter.com/adgrv
 [23]: https://github.com/NuGet/Announcements