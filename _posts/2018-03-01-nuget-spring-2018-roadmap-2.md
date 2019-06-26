---
ID: 226221
post_title: NuGet Spring 2018 Roadmap
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-spring-2018-roadmap-2/
published: true
post_date: 2018-03-01 00:00:00
---
In August 2017, we published the [NuGet Fall 2017 Roadmap][1] where we outlined our backlog for the upcoming quarter. Since then, we’ve published specifications for these experiences on GitHub for the community to review. You have provided a ton of great feedback that has helped us ensure we deliver the right experiences. Thank you for your continued involvement and feedback!

In this post, I would like to briefly summarize our progress on our Fall 2017 roadmap and discuss what we plan to build over the next quarter leading up to early Summer (May 2018).

## Looking back

Here is a quick summary of the various experiences that we have enabled or are enabling soon.

### Package ID Prefix Reservation

*Status: [Implemented][2]*

This feature aims to reduce confusion with NuGet package identity (author, owner, ID). NuGet package owners can apply to reserve a package ID prefix glob pattern so that only they can submit to that pattern. In addition, a visual indicator on NuGet.org and in the Visual Studio Package Manager UI will show when a prefix is reserved so that consumers of the package know that they are getting the package from the author(s) listed. This feature is open to any NuGet package contributor who believes they meet the [criteria for prefix reservations][2]. Since the feature went online, we have received and approved requests from more than **250 NuGet.org authors** with more than **7000 packages** now having the checkmark.

![Package prefix reservation on NuGet.org][3]

### Package Signing

*Status: [In progress][4]*

We are enabling package signing across multiple waves. As the first step, we wanted to enable authors to sign their packages and be able to consume it from the CLI. Hence, two new nuget.exe commands – sign and verify – are now available with [nuget.exe v4.6.0-preview3][5]. [Visual Studio 2017 (version 15.6.0 Preview 3.0 and above)][6] now has the capability to verify the signatures of a signed packages. Note that NuGet.org does not support accepting signed packages yet. You can try the signing functionality with your internal/local NuGet feeds until we allow signed packages on NuGet.org.

The next step is to [enable NuGet.org to accept signed packages][7] and in addition, counter-sign all packages with the [NuGet.org repo signature][8]. Together this will help ensure package integrity from the time package was authored to the time it was consumed. We plan to roll out the E2E signing feature by May 2018. Upcoming updates to Visual Studio will also have the ability to configure environments to enforce [various levels of package signing][9]. Over time, we will add package integrity checks to cross-platform CLIs like dotnet.exe and msbuild.exe.

### Two-factor authentication

*Status: [In progress][10]*

We are [transitioning away from NuGet.org’s home-grown authentication mechanism][11] which will eventually allow us to add support for two-factor authentication (2-FA).

### Organizations

*Status: [In progress][12]*

Organizations allow multiple individuals to manage the same set of packages. Support for Organizations will be available on NuGet.org by early April.

## Roadmap for the next quarter

In addition to the experiences listed above, we will work on the following new experiences.

### Cross-platform credential provider support

*[Feature specification][13] | [Github discussion issue][14]*

If you use an authenticated package feed like [VS Team Services Package Management][15] with Visual Studio or NuGet CLI (including dotnet.exe and msbuild.exe), there is no easy way to restore packages on Linux or Mac. The only solution available today is to specify Personal Access Tokens or the API keys in plain text in your nuget.config file, which is obviously not a good security practice. We would like to address this by having integrated support for credential providers similar to the Windows experience.

### Enable repeatable builds for PackageReference based projects

*[Feature specification][16] | [Github discussion issue][17]*

Projects that use PackageReference to manage NuGet dependencies only specify direct package dependencies. The transitive closure for the dependencies happen during restore resulting in potenitally different build outputs for consecutive builds. We plan to address this issue by enabling locking of package dependency graph for projects.

### Improved debugging and symbols support for packages

*[Feature specification][18] | [Github discussion issue][19]*

With a fast growing .NET ecosystem, we’ve observed a need to streamline the NuGet package debugging experience. Developers should be able to get meaningful debug information when consuming NuGet packages, and for open-source projects, they should even be able to step into the code without the need to clone the repository. In collaboration with the .NET team, we are working to provide an integrated experience for all NuGet package consumers when they want to debug into packages. Some shortcomings of the experience as it exists today are:

*   Discoverability issues in the tooling – it’s difficult to determine whether a package can be debugged
*   Lack of support for portable PDBs
*   Various ways to include source information with no prescribed methodology built into the tooling
*   Since Symbols and .nupkgs are maintained by separate services, providing a first-class experience for both is impossible to do

In addition to building a first party service to address the above issues, we are also partnering with the maintainer of SymbolSource to provide additional value to members of the .NET ecosystem. Expect more information on this soon.

### Improve validation times on package submissions

*[Github discussion issue][20]*

In December 2017, we changed the NuGet.org backend publishing pipeline to introduce a set of validation steps for packages. Our goal is to maintain the same level of experience in terms of the time and effort it would take to publish a package and have it available for download. However, these new validation steps caused a few incidents that resulted in significant delays in the publishing workflow. As mentioned in our recent [blog post][21], we plan to reduce the overall time it takes to validate packages as well as improve our communication and reporting for related service degradations.

### PackageReference migration tool

*[Feature specification][22] | [Github discussion issue][23]*

With Visual Studio 2017 and .NET Core, we improved NuGet package management by introducing the PackageReference feature in MSBuild. PackageReference enables capabilities such as simplified package dependency management, deep MSBuild integration, improved performance for everyday tasks such as install and restore, multi-targeting etc. The next logical steps include providing better guidance and help to users trying to migrate their projects to use PackageReference to express NuGet dependencies, and partnering with package authors to test various package consumption scenarios.

## We want to hear your feedback!

While the experiences above capture our immediate plans, we are also aware of a few other that we simply could not get to in the current quarter and will be our top candidates in the rare event that we run out of work :). Otherwise they will be picked up for consideration in the next quarter.

*   Deprecate external icon URLs for packages [NuGet#352][24]
*   Allow users to determine version resolution strategy [NuGet#5553][25]
*   Manage allowed packages for a solution/repo (or globally) [NuGet#5934][26]
*   Package discovery: Show and search by TFM/package-compatibility [NuGet#3098][27]
*   Easy licensing embedded in package [NuGet#4628][28]
*   Package discovery: Show dependent packages [NuGet#4718][29]
*   Markdown support for documentation on NuGet.org [NuGet#2280][30]
*   Improve basic search [NuGet#4124][31]

We would love to hear your feedback on any additional items we should prioritize. You can reach out to us by commenting on the blog, emailing me at <anangaur@microsoft.com> or by tagging [@NuGet][32] in your tweets. You can also create an issue in our [Github repository][33]. We will be sure to announce any changes or updates on our [NuGet/Announcements][34] repo.

 [1]: https://blog.nuget.org/20170809/NuGet-Fall-2017-Roadmap.html
 [2]: https://docs.microsoft.com/en-us/nuget/reference/id-prefix-reservation
 [3]: https://docs.microsoft.com/en-us/nuget/reference/media/nuget-gallery-reserved-prefix.png
 [4]: https://github.com/NuGet/Announcements/issues/6
 [5]: https://www.nuget.org/downloads
 [6]: https://www.visualstudio.com/downloads/
 [7]: https://github.com/NuGet/Announcements/issues/18
 [8]: https://github.com/NuGet/Announcements/issues/15
 [9]: https://github.com/NuGet/Home/wiki/%5BSpec%5D-NuGet-Package-Signing-Client-Policy
 [10]: https://github.com/NuGet/Announcements/issues/3
 [11]: https://blog.nuget.org/20180227/Deprecating-NuGet-org-authentication.html
 [12]: https://github.com/NuGet/Announcements/issues/4
 [13]: https://aka.ms/nuget-xplat-auth
 [14]: https://github.com/NuGet/Home/issues/6486
 [15]: https://www.visualstudio.com/team-services/package-management
 [16]: https://github.com/NuGet/Home/wiki/Enable-repeatable-builds-for-PackageReference-based-projects
 [17]: https://github.com/nuget/home/issues/5602
 [18]: https://github.com/NuGet/Home/wiki/NuGet-Package-Debugging-&-Symbols-Improvements
 [19]: https://github.com/NuGet/Home/issues/6104
 [20]: https://github.com/NuGet/NuGetGallery/issues/5560
 [21]: https://blog.nuget.org/20180201/NuGet-package-publishing-workflow-behind-the-scenes.html
 [22]: https://github.com/NuGet/Home/wiki/packages.config-%28PC%29-to-PackageReference-%28PR%29-Upgrader
 [23]: https://github.com/NuGet/Home/issues/5877
 [24]: https://github.com/NuGet/Home/issues/352
 [25]: https://github.com/NuGet/Home/issues/5553
 [26]: https://github.com/NuGet/Home/issues/5934
 [27]: https://github.com/NuGet/NuGetGallery/issues/3098
 [28]: https://github.com/NuGet/Home/issues/4628
 [29]: https://github.com/NuGet/NuGetGallery/issues/4718
 [30]: https://github.com/NuGet/NuGetGallery/issues/2280
 [31]: https://github.com/NuGet/NuGetGallery/issues/4124
 [32]: https://twitter.com/nuget
 [33]: https://github.com/NuGet/Home/issues/new
 [34]: https://github.com/NuGet/Announcements