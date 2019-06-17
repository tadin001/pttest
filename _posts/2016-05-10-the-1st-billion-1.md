---
ID: 108
post_title: The 1st Billion
author: Harikrishna Menon
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/the-1st-billion-1/
published: true
post_date: 2016-05-10 00:00:00
---
Today, [NuGet.org][1] reached one billion downloads. This is a momentous achievement for our users and the community of package authors who continue to use and build new libraries that is the cornerstone of .NET adoption. We want to take this opportunity to give a huge thank you to the millions of our users who made this milestone possible. With the advent of .NET Core, ASP.NET Core, Universal Windows Apps, open sourcing of the Xamarin Platform, and the accelerated use of NuGet, we believe we will hit the next 1 billion in a year or so.

## Journey to 1 Billion

NuGet started out as the NuPack project which was then [renamed][2] to NuGet and accepted into the ASP.NET Open Source Gallery by the [OuterCurve foundation][3]. This was one of the first forays by Microsoft into Open Source development and we are proud of that heritage. For a taste of nostalgia, here are some of the very first posts around the NuGet project: [Scott Guthrie][4], [Phil Haack][5], [Scott Hanselman][6] and [Mary J Foley][7]

Here are some fun stats around our growth over the last 6 years. Data from the past 6 weeks or so across some of these dimensions are available in our [statistics][8] page in NuGet.org.

## Package Downloads

Downloads in NuGet.org grew almost 80% this year. From an all time download count of 50 million in 2013, we now average around **72 million downloads per month** in 2016. NuGet from a day 1 was built with caching in mind and we have seen significant usage of NuGet via [NuGet Server][9], [VSTS Package Management][10], [myget][11], [ProGet][12], [Nexus][13], [Artifactory][14] and other third party services. These download numbers purely represent downloads from NuGet.org. We suspect the actual downloads of NuGet packages to be a much higher number.

If you were curious which was the billion'th package that was downloaded. The magic number was taken by the [dnx-clr-win-x86][15] package, which was downloaded at around 3:15 a.m (PST) on 5/10/2016. 

## Package Uploads

We have over 54K unique packages in NuGet.org and over 600K packages in total. Year over year we are seeing around 31% growth in the number of packages being uploaded to NuGet.org.

![Package Uploads][16]

## Tools Usage

This visualization shows the average count of requests from NuGet v3 client, the Windows x86 Command line and the .NET CLI NuGet Client over the last 2 weeks to NuGet.org endpoints. As you can see, there is huge interest in .NET Cross platform tools and we believe this is going to be a significant part of our tools traffic going forward.

![Tools Usage][17]

## NuGet.org

We now have around 600K users visiting NuGet.org on a monthly basis to search and peruse our packages. We are making investments in improving search and package information in the near future to make user engagement much more meaningful on the website.

![Site Usage][18]

## Community

An Open Source project thrives and survives on the patronage of its users. Even from the very early days, we have had significant contributions from the community. [3 out of the top ten contributors][19] in the initial days were individuals from the community. Fast forwarding to today, we have over 130 contributors on GitHub across [NuGet.Client][20] and [NuGetGallery][21]. While a number of these folks are MSFT’s, there is a large number of contributions from the community as well. We are always on the lookout for folks from the community to suggest ideas, and [contribute][22] features to the project. 

Among non-Microsoft packages. [Json.net][23] authored by our friend [James Newton-King][24] reigns supreme with over 28 million downloads. Our most prolific author/owner with the largest number of packages at this time is [Definitely Typed][25] which curates high quality [TypeScript][26] definitions with over 1,779 packages.

We cannot reiterate enough how much our community means to us and we want to continue to partner with you all to innovate and push the boundaries of package management with NuGet.

## Why you should be using NuGet

There is a lot of innovation happening in the community around .NET which enables you to develop software faster than ever. NuGet is the most widely adopted system across the community for consuming shared components and is the primarily channel for showcasing this innovation. One of the big goals for us is to optimize developer productivity, reduce redundancy and promote code-reuse. Using NuGet Packages is one the most optimal ways to do this. We are here to help you succeed in adopting NuGet and if you would like to talk to us about how NuGet can help you achieve these goals, do not hesitate to reach out to us at <feedback@nuget.org> or holler at us on Twitter [@nuget][27].

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. If you would like share your pain points and your current and future needs, use the [calendly][28] link to set up a quick 15-30 min call with us. If you would like to send us an email instead, hit us up at <feedback@nuget.org>. You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][29].

 [1]: https://nuget.org
 [2]: http://haacked.com/archive/2010/10/29/nupack-is-now-nuget.aspx/
 [3]: http://www.outercurve.org/
 [4]: http://weblogs.asp.net/scottgu/announcing-nupack-asp-net-mvc-3-beta-and-webmatrix-beta-2
 [5]: http://haacked.com/archive/2010/10/06/introducing-nupack-package-manager.aspx/
 [6]: http://www.hanselman.com/blog/IntroducingNuGetPackageManagementForNETAnotherPieceOfTheWebStack.aspx
 [7]: http://www.zdnet.com/article/microsoft-contributes-new-open-source-package-management-system-to-outercurve-foundation/
 [8]: https://www.nuget.org/stats
 [9]: https://docs.nuget.org/create/hosting-your-own-nuget-feeds
 [10]: https://www.visualstudio.com/en-us/get-started/package/what-is-packaging
 [11]: https://www.myget.org/
 [12]: http://inedo.com/proget
 [13]: http://www.sonatype.org/nexus/
 [14]: https://www.jfrog.com/artifactory/
 [15]: https://www.nuget.org/packages/dnx-clr-win-x86/1.0.0-rc1-update2
 [16]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/PackageUploads.png
 [17]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/ClientUsage.png
 [18]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/SiteUsage.png
 [19]: http://haacked.com/archive/2011/04/29/nuget-contributors.aspx/
 [20]: https://github.com/NuGet/NuGet.Client
 [21]: https://github.com/NuGet/NuGetGallery
 [22]: https://docs.nuget.org/contribute/contributing-to-nuget
 [23]: https://www.nuget.org/packages/Newtonsoft.Json/
 [24]: https://twitter.com/JamesNK
 [25]: https://www.nuget.org/profiles/DefinitelyTyped
 [26]: http://www.typescriptlang.org/
 [27]: https://twitter.com/nuget
 [28]: https://calendly.com/harikm/nuget-feedback
 [29]: https://github.com/Nuget/Home