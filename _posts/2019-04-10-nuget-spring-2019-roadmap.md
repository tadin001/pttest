---
ID: 142
post_title: NuGet Spring 2019 Roadmap
author: Anand Gaurav
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-spring-2019-roadmap/
published: true
post_date: 2019-04-10 00:00:00
---
We published our last NuGet roadmap in June last year. Many of the features announced were major additions to NuGet and we have been hard at work to implement those over the last few months. In this post, we will start by summarizing the features we have completed and then peek into the next wave of work planned.

## Looking back Here are some features that were announced in our last roadmap blog post and have since been released.

### Cross-platform credential provider support for Azure Artifacts

*Status: Implemented | [Documentation][1]* Before, there was no easy way to restore packages on Linux or macOS if you used [Azure Artifacts][2] with Visual Studio or the NuGet CLI (including dotnet.exe and msbuild.exe). We have now implemented cross-platform support for credential providers, similar to the Windows experience.

### Improved package debugging support

*Status: Implemented | [Blog post][3] | [Documentation][4]* With a fast-growing .NET ecosystem, we needed a streamlined NuGet package debugging experience. We now offer a NuGet Symbol Server where you can push the new symbols package (.snupkgs). This new symbol package format supports portable PDBs that are cross-platform, secure, and significantly smaller than the existing Windows PDBs. If you are not using symbol packages yet, [try it now][4]! We are also working towards having a default `NuGet Symbol Server` option available in the `Debugging->Symbols` settings on Visual Studio 2019. This will further streamline the overall package debugging experience.

### Trust policies based on package signing

*Status: Implemented | [Blog post][5] | [Documentation][6]* With this feature, developers can now customize their environment to define package authors and/or package repositories they trust thereby allowing only trusted packages to be installed. This information is stored in the `nuget.config` file and can be configured to match your needs.

### Enable repeatable restores using a lock file

*Status: Implemented | [Blog post][7] | [Documentation][8]* Projects that use `PackageReference` to manage NuGet dependencies specify only their direct package dependencies. The full closure of package dependencies is determined at build-time and may differ across builds due to a few rare corner cases. With the [per project lock file feature][8], we have now addressed this issue so that restores (builds) are consistent no matter when and where they happen. We want to expand this experience and allow [locking the dependencies centrally i.e. at a solution or repo level][9].

## Upcoming experiences Here is a list of experiences that are either in progress or planned. Note that this list is not in any particular order - when a work item is committed, it is announced on our

[NuGet/Announcements][10] repo with its feature specification, discussion issue, and any other relevant links.

### Improved NuGet.org search

*Status: In progress | GitHub issue(s): [#4124][11]* We often get feedback to improve the search relevancy and ranking on NuGet.org. This has been high on our roadmap for a long time. We are currently working on changing our search backend to Azure search. As part of this, we are also modifying the search relevancy and ranking algorithms. Very soon we will be ready to test the new search and fine tune it further based on your inputs. Watch out for an [announcement][10] in this regard.

### Deprecate external URLs for packages

*Status: In progress | [Announcement][12] | GitHub issue(s): [#352][13], [#4628][14], [#6873][15]* With package signing, signed packages are now immutable and any tampering to change its content will be detected by the NuGet clients. We want to further improve support for portability and immutability of NuGet packages by allowing authors to embed icons, licenses and documentation within packages. The NuGet clients will also be updated to respect such content. Finally, we will encourage package authors to stop using external content URLs inside NuGet package specifications. We have already implemented [embedded licensing in NuGet packages][16], and we are now continuing our efforts to embed icon and ReadMe (markdown description/documentation) inside the package.

### Deprecate packages

*Status: In progress | [Announcement][17] | GitHub issue(s): [#2867][18]* As the number of packages grows on NuGet.org, a number of packages start getting obsolete either because they are no longer maintained or because there were some issues with them (privacy, critical bugs etc.). Though NuGet.org has the ability to hide (unlist) packages, we want to improve this experience by enabling authors to explicitly deprecate packages with specific reasons. We also want to add the ability for package authors to suggest alternatives to consumers.

### Cross-platform support for Package Signing

*Status: In progress | GitHub issue(s):[#7939][19]* We implemented [author signing of packages][20] last year and later came up with the support for NuGet.org repo-signing all packages uploaded to it. With signed packages, NuGet clients are able to prevent usage of tampered packages and also enable you to define a trust boundary in terms of package authors, repos, and owners of repos you trust using [trust policies][5]. Currently the benefits of package signing are only available on Windows with NuGet.exe or with Visual Studio (15.9 and above). We want to make sure that NuGet is able to verify the packages for signatures across all platforms by supporting the functionality with `dotnet.exe` and on `Mono`.

### Improved NuGet settings and configuration experience with Visual Studio

*Status: Planned | GitHub issue(s): [#7284][21]* We continuously receive feedback to enhance the NuGet package manager experience around managing packages, NuGet settings, sources and other configurations. We plan to enhance some of these experiences starting with NuGet settings, sources and configuration management. These enhancements are also necessary for us to enable the [trust policies][5] experience on Visual Studio.

## We want to hear your feedback! We would love to hear your feedback on these or any additional items you think we should be prioritizing. You can reach out to us either by

[creating a new GitHub issue][22] or by tagging [@nuget][23] in your tweets. You can also email me at <anangaur@microsoft.com> or tag me, [@adgrv][24], in your tweets. We will announce any changes or updates to our plan on our [NuGet/Announcements][25] repo.

 [1]: https://github.com/Microsoft/artifacts-credprovider/blob/master/README.md
 [2]: https://www.visualstudio.com/team-services/package-management
 [3]: https://blog.nuget.org/20181116/Improved-debugging-experience-with-the-NuGet-org-symbol-server-and-snupkg.html
 [4]: https://docs.microsoft.com/en-us/nuget/create-packages/symbol-packages-snupkg
 [5]: https://blog.nuget.org/20181205/Lock-down-your-dependencies-using-configurable-trust-policies.html
 [6]: https://docs.microsoft.com/en-us/nuget/consume-packages/installing-signed-packages#configure-package-signature-requirements
 [7]: https://blog.nuget.org/20181217/Enable-repeatable-package-restores-using-a-lock-file.html
 [8]: https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files#locking-dependencies
 [9]: https://github.com/NuGet/Home/wiki/Centrally-managing-NuGet-packages
 [10]: https://github.com/NuGet/Announcements/issues
 [11]: https://github.com/NuGet/NuGetGallery/issues/4124
 [12]: https://github.com/NuGet/Announcements/issues/29
 [13]: https://github.com/NuGet/Home/issues/352
 [14]: https://github.com/NuGet/Home/issues/4628
 [15]: https://github.com/NuGet/Home/issues/6873
 [16]: https://github.com/NuGet/Announcements/issues/32
 [17]: https://github.com/NuGet/Announcements/issues/33
 [18]: https://github.com/NuGet/Home/issues/2867
 [19]: https://github.com/NuGet/Home/issues/7939
 [20]: https://blog.nuget.org/20180522/Introducing-signed-package-submissions.html
 [21]: https://github.com/NuGet/Home/issues/7284
 [22]: https://github.com/NuGet/Home/issues/new
 [23]: https://twitter.com/nuget
 [24]: https://twitter.com/adgrv
 [25]: https://github.com/NuGet/Announcements