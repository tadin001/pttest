---
ID: 179
post_title: NuGet 2.6.1 for WebMatrix
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-2-6-1-for-webmatrix/
published: true
post_date: 2014-03-28 00:00:00
---
The NuGet team released an updated NuGet Package Manager extension for WebMatrix on March 26, 2014. This update can be installed from the [WebMatrix Extension Gallery][1] using the following steps:

1.  Open WebMatrix 3
2.  Click the Extensions icon in the Home ribbon
3.  Select the Updates tab
4.  Click to update NuGet Package Manager to 2.6.1
5.  Close and restart WebMatrix 3

Here are the salient points from the [release notes][2].

## Notable Changes

This extension update addresses two of the biggest issues users have faced consuming NuGet packages within WebMatrix. The first was a [NuGet schema version error][3] and the second was a bug leading to [zero-byte DLLs in the bin folder][4].

This latest release provides compatibility with the newest NuGet packages, preventing the schema error from occurring. New versions of packages including Microsoft.AspNet.WebPages can now be installed in WebMatrix. Some of these packages were using NuGet features such as [XDT config transforms][5], which wasn't supported in WebMatrix until now.

## Other Recent Improvements

When NuGet Package Manager 2.8 was released for Visual Studio, we also released NuGet Package Manager 2.5.0 for WebMatrix. While this was mentioned in the [NuGet 2.8 Release Notes][6], we didn't mention the specific new features that update introduced.

Those improvements include the ability to [update all NuGet packages][7] in your web site together, as well as getting a prompt to [overwrite existing files][8] when installing NuGet packages.

## Continued Updates

Because we extracted the NuGet functionality out of WebMatrix 3 into an extension contributed to the NuGet open-source project, we've been able to make these important updates without requiring an update to WebMatrix itself. If you have other issues using NuGet within WebMatrix, [let us know by filing issues on CodePlex][9] so that we can address them.

 [1]: http://extensions.webmatrix.com/packages/NuGetPackageManager/
 [2]: http://docs.nuget.org/docs/release-notes/nuget-2.6.1-for-webmatrix
 [3]: http://docs.nuget.org/docs/release-notes/nuget-2.6.1-for-webmatrix#NuGet_Schema_Version_Error
 [4]: http://docs.nuget.org/docs/release-notes/nuget-2.6.1-for-webmatrix#Zero-Byte_DLLs_in_bin_Folder
 [5]: http://docs.nuget.org/docs/release-notes/nuget-2.6#XDT_Web.config_transformation_support
 [6]: http://docs.nuget.org/docs/release-notes/nuget-2.8#WebMatrix_NuGet_Client_Updates
 [7]: http://docs.nuget.org/docs/release-notes/nuget-2.6.1-for-webmatrix#Update_All
 [8]: http://docs.nuget.org/docs/release-notes/nuget-2.6.1-for-webmatrix#Overwrite_Existing_Files
 [9]: https://nuget.codeplex.com/WorkItem/Create