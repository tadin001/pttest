---
ID: 105
post_title: Announcing NuGet 3.4.2
author: Harikrishna Menon
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/anouncing-nuget-3-4-2/
published: true
post_date: 2016-04-09 00:00:00
---
We have listened to the feedback from the community, and we have tried our very best to turn around quickly and address this feedback. Our primary focus for 3.4.2 have been around improving the quality and performance of the product.

The complete list of issues fixed in this version is [available on GitHub][1]. In this post, we'll review the important changes in 3.4.2 release.

You can download the latest 3.4.2 VS extension and Windows x86 Commandline (RC) from [here][2].

## Performance Improvements

*   We continue to invest heavily in performance improvements. For this release, we have significantly improved the performance of updates in a specific scenario, where updates on packages with deep dependency graphs took a really long time and hung Visual Studio.
*   In addition to this change, we have fixed an issue where we were hitting the network twice when fetching the update count in the VS UI. This was partially responsible for some timeout issues customers experienced in 3.4/3.4.1.
*   Finally, nuget restore without network traffic is 2.5x – 3x faster within Visual Studio.

## Changes

We have fixed some issues that broke compatibility in some cases with Artifactory, Klondike and Nexus. We have further improved the quality of the ‘All Sources’ feature by fixing issues with merging versions and entries from multiple sources.

Our [release notes for 3.4.2][3] and the [issues list in GitHub][1], goes into more details around the changes in this release.

## Breaking Changes

If you have not specified a [DefaultPushSource in nuget.config][4], and try to push a package without specifying the Source parameter, you will get the following error message in 3.4.2

"Source parameter was not specified"

We have heard feedback from package authors and seen it in the support requests that we receive on nuget.org, that having a default behavior like this causes accidental publish of proprietary software that was never meant to be consumed publically. NuGet has a huge ecosystem with multiple private and public hosts and this change enables authors to be prescriptive about their intentions around package publications.

## Known Issues in 3.4.2 Windows x86 Commandline (RC)

These issues will be fixed early next week before we hit RTM.

*   Running nuget restore on a solution will fail if the solution file is placed in a lower folder hierarchy than the project files.
*   Running nuget delete command on a package using the V2 feed will fail. Use V3 feed instead.

## What's Next

The team is cleaning up any remaining issues from the cross-platform support in conjunction with the .NET Command-Line-Interface release. There are new features we want to implement around project.json capabilities, and improvements to NuGet.org service that enable a smoother publication process for package authors.

Download the latest version of the NuGet Visual Studio extension from [dist.nuget.org][2]

## Help us build our roadmap

We want NuGet to meet the evolving needs for community and provide an amazing package management and authoring experience. In order for us to do that, we need your help!!!

We want to talk to you to understand your pain points and needs. This is the feedback we want to use as the foundation of our future roadmap. All we need is 15-30 minutes of your time. If you are interested in providing us with feedback, here is our calendly link - <https://calendly.com/harikm/nuget-feedback>. Book any available time that is convenient for you to talk to us. If you would like to send us an email instead, hit me up at harikm@microsoft.com.

Our team is super excited about engaging with our customers more deeply and we would be happy to answer any questions you might have as well. As always, if you run into any issues or have an idea, open an issue on [GitHub][5].

 [1]: https://github.com/Nuget/Home/issues?q=is%3Aissue+milestone%3A3.4.2+is%3Aclosed
 [2]: https://dist.nuget.org/index.html
 [3]: http://docs.nuget.org/release-notes/nuget-3.4.2
 [4]: https://docs.nuget.org/consume/nuget-config-file
 [5]: https://github.com/Nuget/Home/issues