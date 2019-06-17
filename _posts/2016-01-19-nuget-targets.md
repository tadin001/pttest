---
ID: 100
post_title: >
  NuGet Targets for Project.Json now open
  source
author: Jeffrey Fritz
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-targets/
published: true
post_date: 2016-01-19 00:00:00
---
A question that comes up from time to time is: how does Visual Studio 2015 add references when using project.json files in conjunction with a csproj without modifying the csproj file? This is a good question and demonstrates some of our strategy to help make NuGet easier to integrate into project systems without having to teach NuGet how to work with every project system. We've released this component as open source, and we'll show you where you can learn more about how it was built.

There is a MSBuild task for csproj files that will examine the project.lock.json file and determine what assets should be added. This build task then dynamically adds those resources to the MSBuild tree during design-time and build. This component was closed source and we are now making it available from the NuGet organization on GitHub, with the Roslyn team maintaining the code.

You can get the source code from the new [NuGet.BuildTasks respository on GitHub][1].

 [1]: https://github.com/NuGet/NuGet.BuildTasks