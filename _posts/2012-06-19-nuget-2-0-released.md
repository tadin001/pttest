---
ID: 52
post_title: NuGet 2.0 Released
author: Howard Dierking
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-2-0-released/
published: true
post_date: 2012-06-19 00:00:00
---
We're happy to announce that we released NuGet 2.0 on 6/19/2012. This release includes support for grouping dependencies, tools and content by the target framework of the project. Additionally, we've dramatically improved the performance of tab completion in the package management console.

## Package restore consent is now active

As described in an [earlier post on package restore consent][1], NuGet 2.0 will now require that consent be given to enable package restore to go online to download packages. Please ensure that you have provided consent via either the package manager configuration dialog or the EnableNuGetPackageRestore environment variable.

More details on NuGet 2.0 can be found on the [release notes][2]. Finally, NuGet 2.0 fixed several bugs. For a full list of work items fixed in NuGet 2.0, please view the [NuGet Issue Tracker][3] for this release.

 [1]: http://blog.nuget.org/20120518/package-restore-and-consent.html
 [2]: http://docs.nuget.org/docs/release-notes/nuget-2.0
 [3]: http://nuget.codeplex.com/workitem/list/advanced?keyword=&status=Closed&type=All&priority=All&release=NuGet%202.0&assignedTo=All&component=All&sortField=Votes&sortDirection=Descending&page=0