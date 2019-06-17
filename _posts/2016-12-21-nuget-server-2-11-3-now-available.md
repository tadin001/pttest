---
ID: 117
post_title: NuGet.Server 2.11.3 now available
author: Jon Chu
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-server-2-11-3-now-available/
published: true
post_date: 2016-12-21 00:00:00
---
Today we are releasing an update to the [NuGet.Server package][1]. The purpose of this update is to address a bug that enabled NuGet.Server to be installed on projects that target .NET Framework versions less than 4.6.

## Issue addressed

Previously, there was a bug in NuGet.Server 2.11.2 ([3257][2], [3406][3]) that incorrectly considered the package to be compatible with projects that target .NET Framework prior to 4.6. Upon installing the package, the build would fail due to a missing NuGet.Server reference. With this update, users will not be able to install NuGet.Server with incompatible projects.

## Try it out!

This update of the NuGet.Server version 2.11.3 is now available for you to [download at nuget.org][1]. It comes with fixes that help new users of the package that might be using the wrong project type. Try it out, and let us know if you have any feedback.

We want NuGet to meet the evolving needs of our community. If you would like to share your pain points, and your current or future needs, hit us up at <feedback@nuget.org>. You can also leave a comment below, and as always, if you run into any issues or have an idea, open an issue on GitHub.

 [1]: https://www.nuget.org/packages/NuGet.Server/2.11.3/
 [2]: https://github.com/NuGet/NuGetGallery/issues/3257
 [3]: https://github.com/NuGet/NuGetGallery/issues/3406