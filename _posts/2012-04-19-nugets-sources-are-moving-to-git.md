---
ID: 143
post_title: 'NuGet&#8217;s Sources Are Moving to Git'
author: Drew Miller
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nugets-sources-are-moving-to-git/
published: true
post_date: 2012-04-19 16:11:48
---
At the end of April, we'll be moving the NuGet sources hosted on [CodePlex][1] from a Mercurial repository to a Git repository. This won't impact or affect NuGet users, but NuGet contributors and those that follow NuGet's development might need to act before this move.

## Forks

All of the [current NuGet forks on CodePlex][2] will be deleted as part of this move. If you have forked NuGet, and you need to keep that fork, you can clone the fork and keep it locally or you can find somewhere else to push it. Aren't DVCS great?

## Pull Requests

The NuGet team will review all the [currently active pull requests][3] before we move to Git. We will not be accepting any new pull requests until this move is complete. Please wait to initiate new pull requests until that time.

## Commit History

[NuGet's commit history][4] will be preserved. If you've contributed to NuGet, your contributions will still be included in the commit history.

## CodePlex Project

All other areas of the [NuGet CodePlex project][5] (the URL, issues, discussions, etc.) will remain as they are.

*If you have questions or concerns, please present them in the comments or [start a discussion on CodePlex][6].*

 [1]: http://codeplex.com
 [2]: http://nuget.codeplex.com/SourceControl/network
 [3]: http://nuget.codeplex.com/sourcecontrol/list/contributions
 [4]: http://nuget.codeplex.com/SourceControl/list/changesets
 [5]: http://nuget.codeplex.com
 [6]: http://nuget.codeplex.com/discussions/create?ProjectName=nuget