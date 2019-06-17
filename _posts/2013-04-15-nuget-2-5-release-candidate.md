---
ID: 154
post_title: NuGet 2.5 Release Candidate
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-2-5-release-candidate/
published: true
post_date: 2013-04-15 00:00:00
---
*Updated:* The NuGet 2.5 Release Candidate was available from April 15 to April 25, 2013. For the several hundred of you that downloaded the release candidate, we truly appreciate your verification of the RC build that became the final release.

## What's Included in NuGet 2.5

This release was so big, we felt compelled to skip versions 2.3 and 2.4! To date, this is the largest release we've had for NuGet, with over [160 work items][1] in the release.

For details about what's in the release, the [tentative release notes][2] have been published to [docs.nuget.org][3].

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

## NuGet's description and slogan

We think we've outgrown NuGet's current description. According to [nuget.org][4], we have the following description and slogan:

> **Jump Start Your Projects with NuGet**
> 
> NuGet is a Visual Studio extension that makes it easy to install and update third-party libraries and tools in Visual Studio.
> 
> *So install NuGet and get a jump on your next project!*

We have come up with a new description for NuGet:

> NuGet is the open-source package manager for the Microsoft platform and .NET/Mono. The NuGet client tools provide the ability to produce and consume packages. The NuGet Gallery (nuget.org) is the central package repository used by all package authors and consumers.

We'd like your feedback on this new description. And for a new slogan to replace "Jump Start Your Projects with NuGet," we're still undecided. We want something fun and maybe a little edgy. If you'd like to submit your idea, tweet your idea using the [#nugetslogan][5] hashtag. Once we have a good list to choose from, we'll open up a poll. Follow [@nuget][6] for updates.

<a class="twitter-timeline" data-dnt="true" href="https://twitter.com/search?q=%23nugetslogan" data-widget-id="323970527691685888">Tweets about "#nugetslogan"</a>

<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+"://platform.twitter.com/widgets.js";fjs.parentNode.insertBefore(js,fjs);}}(document,"script","twitter-wjs");</script>

 [1]: https://nuget.codeplex.com/workitem/list/advanced?release=NuGet%202.5&status=all
 [2]: http://docs.nuget.org/docs/release-notes/nuget-2.5
 [3]: http://docs.nuget.org
 [4]: http://nuget.org
 [5]: https://twitter.com/search/realtime?q=%23nugetslogan
 [6]: http://twitter.com/nuget