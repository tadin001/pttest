---
ID: 104
post_title: Introducing NuGet 3.4
author: Jeffrey Fritz
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/introducing-nuget-3-4/
published: true
post_date: 2016-03-30 00:00:00
---
The NuGet team is happy to introduce version 3.4 and 3.4.1 of the NuGet clients for Visual Studio 2015. We shipped v3.4 with Visual Studio 2015 Update 2 and immediately followed that with the 3.4.1 release to address a small set of issues that were identified after Visual Studio bundled the NuGet 3.4 release. There were three main areas that the team focused on:

*   Cross-Platform support
*   Improved Performance in Visual Studio
*   Minor user-interface improvements

The cross-platform support is being validated, and we plan to release command-line versions of nuget.exe in the coming weeks.

We think you'll find these updates a welcome change that improves your day-to-day interactions with NuGet packages. The complete list of issues fixed in this version is [available on GitHub][1]. In this post, we'll review the important updates in the 3.4 and 3.4.1 versions.

## Visual Studio Performance and All Sources

In the 3.4RC we previously discussed the performance improvements in the UI. We've taken those performance updates and added a few more things that we know you'll be happy to see. Check this video for a quick tour of the new enhancements:

<div style="text-align: center; width: 100%">
  <iframe width="420" height="315" src="https://www.youtube.com/embed/W6quyUV-cxY" frameborder="0" allowfullscreen></iframe>
</div>

That video is not edited or enhanced for speed, that is real-time search and updates performance from outside of the pristine Microsoft networks. The package lists on the Updates tab are now sorted alphabetically and the 'All Sources' aggregate NuGet source are available for you to search across in the user-interface.

## Changes

The clients have been updated to remove some of the extra interactions that felt unnecessary. For example:

*   Packages in project.json can have a floating version number and were updating on every build regardless of whether the package has an update available. The client has been optimized to only check for an update and apply it when forced to restore, clean, rebuild, or after a project.json modification.

*   The Visual Studio extension no longer are automatically adds the nuget.org repository source to your nuget.config files when you use the NuGet configuration user interface.

*   Shared projects no longer have packages restored to them, nor is a lock file written for them

*   The latest project.json schema for DNX is supported in the Visual Studio editor and by the NuGet extension

## Breaking Change

We are announcing a breaking change in the way that NuGet clients handle package version numbers. The clients will now normalize the numbers to a format of *major*.*minor*.*patch*-*prerelease* where major, minor, and patch are all integers. The prerelease is treated as a string and will not be modified. This means that if you search for version 1 of a package, the NuGet client will search for version 1.0.0 Similaryly, if you search for package version 1.01.0.0 the client will search for version 1.1.0

Full details of this change are available in the documents under [normalized version numbers][2].

## What's Next

The team is cleaning up any remaining issues from the cross-platform migration in conjunction with the .NET Command-Line-Interface release. There are new features we want to implement for project.json capabilities and there are NuGet.org service improvements that we want to implement to better enable the publication of a team's collection of packages.

Download the latest version of the NuGet Visual Studio extension from [dist.nuget.org][3]

 [1]: https://github.com/NuGet/Home/issues?q=milestone%3A%223.4+RTM%22+is%3Aclosed
 [2]: http://docs.nuget.org/Create/Versioning#Normalized_Version_Numbers
 [3]: https://dist.nuget.org/index.html