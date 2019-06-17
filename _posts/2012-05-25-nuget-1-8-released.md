---
ID: 50
post_title: NuGet 1.8 Released
author: The NuGet Team
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-1-8-released/
published: true
post_date: 2012-05-25 00:00:00
---
We're happy to announce that we released NuGet 1.8 on 5/23/2012. This release includes support for localized satellite assemblies, nuget.exe performance improvements, and 34 bug fixes. Details are in the [NuGet 1.8 Release Notes][1].

## Known issue on Windows XP

We would like to call out that shortly after NuGet 1.8 was released, we learned that a cryptography change in 1.8 broke users on Windows XP. Do not install NuGet 1.8 onto a Windows XP machine; if you have already installed NuGet 1.8, you can uninstall it and reinstall NuGet 1.7 using [this download link][2].

We are working on a NuGet 1.8.1 release to address the regression. There is a [CodePlex discussion thread][3] for this issue, which resulted in a [work item][4] to fix the regression.

 [1]: http://docs.nuget.org/docs/release-notes/nuget-1.8
 [2]: http://nuget.codeplex.com/downloads/get/382255
 [3]: http://nuget.codeplex.com/discussions/356928
 [4]: http://nuget.codeplex.com/workitem/2240