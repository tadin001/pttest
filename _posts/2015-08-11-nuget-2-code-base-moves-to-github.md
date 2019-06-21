---
ID: 226049
post_title: NuGet 2 code base moves to GitHub
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-2-code-base-moves-to-github/
published: true
post_date: 2015-08-11 00:00:00
---
In the last few months we have constantly moved the issues and all new code base to GitHub. Today we moved the last bits of code from [CodePlex][1] to [GitHub][2]. We have stopped monitoring the CodePlex site completely, and will not be taking further looks at reports there or pull requests. As we keep working on NuGet 3.1.2 release for Visual Studio 2015 we will also work on 2.8.7 release with bug fixes for Visual Studio 2013.

The tip branch we compile nuget.core from is the dev branch, this is a change from master we used in CodePlex. The change it to align this repo with the rest of the repos in nuget.org.

 [1]: https://nuget.codeplex.com
 [2]: https://github.com/NuGet/NuGet.V2