---
ID: 103
post_title: Announcing NuGet 3.4 RC
author: Jeffrey Fritz
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/announcing-nuget-3-4-rc/
published: true
post_date: 2016-03-08 00:00:00
---
After several months of measuring, tuning, and testing, the NuGet team would like to announce version 3.4 RC of the NuGet clients for Visual Studio 2015 and the command-line. There were three main areas that the team focused on for this version:

*   Cross-Platform support
*   Improved Performance in Visual Studio
*   Minor user-interface improvements

We think you'll find these updates a welcome change that improves your day-to-day interactions with NuGet packages. The complete list of issues fixed in this version is [available on GitHub][1]. In this post, we'll review the important updates in the 3.4-RC version.

## Visual Studio Performance

The first thing that NuGet Visual Studio users should notice when using the NuGet UI is that it loads immediately and defaults to the 'Installed' view of packages. Check out the un-edited animated GIF below to see just how fast this performs on an ASP.NET MVC 5.2 project:

![Speedy Start][2]

You'll see similar speed improvements in the Updates and Consolidate tabs, where we've optimized the offline storage and retrieval of package information. One other bit of tuning that we did to this screen was to enable gzip-compressed data from the NuGet repositories. This should be a small change that we can already see has made a big improvement in search and download performance from our services.

Another feature that we cleaned up on the user-interface is that the Installed, Consolidate and Updates tabs now show the list of packages sorted alphabetically. We know this was an important feature to the community and we updated the interface as requested.

## Changes

The clients have been updated to remove some of the extra interactions that felt unnecessary. For example:

*   Packages in project.json can have a floating version number and were updating on every build regardless of whether the package has an update available. The client has been optimized to only check for an update and apply it when forced to restore, clean, rebuild, or after a project.json modification.

*   The Visual Studio extension no longer are automatically adds the nuget.org repository source to your nuget.config files when you use the NuGet configuration user interface.

*   Shared projects no longer have packages restored to them, nor is a lock file written for them

*   The latest project.json schema for DNX is supported in the Visual Studio editor and by the NuGet extension

## What's Next

The team is cleaning up any remaining issues from the cross-platform migration and is looking at some further performance tuning for the clients. There are new features we need to implement for project.json capabilities and there are NuGet.org service improvements that we want to implement to better enable the publication of a team's collection of packages.

Download the latest version of the NuGet command-line and Visual Studio extension from [dist.nuget.org][3]

 [1]: https://github.com/NuGet/Home/issues?q=milestone%3A%223.4+Beta%22+is%3Aclosed
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/speedy-start.gif
 [3]: https://dist.nuget.org/index.html