---
ID: 106
post_title: Announcing NuGet 3.4.3
author: Harikrishna Menon
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/anouncing-nuget-3-4-3/
published: true
post_date: 2016-04-22 00:00:00
---
Today, we are releasing the 3.4.3 RTM version of the NuGet Visual Studio 2015 extension and the Windows x86 commandline executable. We continue to heavily invest in improving the quality of NuGet scenarios in Visual Studio and commandline. The complete list of issues fixed in this version is [available on GitHub][1]. In this post, we will review the important changes in 3.4.3 release.

You can download the latest 3.4.3 RTM VS extension and Windows x86 Commandline from [here][2].

## Changes

*   Fixed some authorization issues with password protected private nuget feeds.
*   Fixed an issue around being unable to restore PCL's from project.json with runtimes specified.
*   Some customers were running into intermittent failures when installing packages. This has now been fixed in this release.
*   Fixed an issue that caused restore failures in C++/CLI projects with project.json.
*   Some packages (E.g ModernHttpClient) where not being unzipped correctly when you use nuget in mono. This has now been fixed in this release.

Our [release notes for 3.4.3][3] and the [issues list in GitHub][1], goes into more details around the changes in this release.

## What's Next

Along with improving the overall quality and performance of the product, the team is cleaning up any remaining issues from the cross-platform support in conjunction with the .NET Command-Line-Interface release. There are new features we want to implement around project.json capabilities, and improvements to NuGet.org service that enable a smoother publication process for package authors. Stay tuned for a more detailed update of our roadmap in the next month or so.

## We want to hear your feedback!

We want NuGet to meet the evolving needs for community and provide an amazing package management and authoring experience. In order for us to do that, we need your help!!!

We want to talk to you to understand your pain points and needs. This is the feedback we want to use as the foundation of our future roadmap. All we need is 15-30 minutes of your time. If you are interested in providing us with feedback, here is our calendly link - <https://calendly.com/harikm/nuget-feedback>. Book any available time that is convenient for you to talk to us. If you would like to send us an email instead, hit me up at harikm@microsoft.com.

Our team is super excited about engaging with our customers more deeply and we would be happy to answer any questions you might have as well. As always, if you run into any issues or have an idea, open an issue on [GitHub][4].

 [1]: https://github.com/NuGet/Home/issues?q=is%3Aissue+milestone%3A3.4.3+is%3Aclosed
 [2]: https://dist.nuget.org/index.html
 [3]: http://docs.nuget.org/release-notes/nuget-3.4.3
 [4]: https://github.com/Nuget/Home/issues