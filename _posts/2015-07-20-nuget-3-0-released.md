---
ID: 226045
post_title: NuGet 3.0 Released
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-3-0-released/
published: true
post_date: 2015-07-20 00:00:00
---
Its been a long journey to this date. We've investigated and tested and released a number of interim builds for developers and package owners to test with the latest Visual Studio bits. The NuGet team is happy to hear all of your feedback about our add-in for Visual Studio and would like to announce the availability of the NuGet 3.0 client with Visual Studio 2015. This update to the NuGet extension comes with Visual Studio 2015 and is avaialble from the [Visual Studio extension gallery][1] We are also making available an update to the NuGet extension for Visual Studio 2013, and you can download version 2.8.6 from the \[Visual Studio extension gallery\]() as well.

## Changes

There are no significant changes to the user interface since the last release candidate of NuGet, but we did make one change to remove a feature that was no longer necessary. We have removed the 'force' flag from the powershell Install-Package command. This is no longer needed, as the option was intended be used to install packages without installing their dependencies. However, we already have an 'ignore-dependencies' flag, so the ["force" option was declared redundant][2] and removed.

## Known Issues

There were a number of known issues delivered with this release, and all of these items are fixed in our scheduled 3.1 release to coincide with the release of Windows 10 on July 29th. You will be able to update your Visual Studio extension from the gallery on or after that date to fix these known issues.

*   Translation is not provided for the "Do not show this again" label on the preview window and the "Authors" label in the package description window.
*   When you working on a project by using TFS source control, NuGet cannot present the package manager user interface if the nuget.config file is marked as read-only. 
    *   **Workaround** Check out the file from TFS. 
*   Text in the yellow "restart bar" in the NuGet Powershell window is not visible when you use the Visual Studio dark theme. 
    *   **Workaround** Use the Visual Studio light theme. 
*   Powershell execution policy prevents packages from being installed properly - [Issue 974][3] (this fix will not make the Windows 10 update, and will be in a release after that) 
    *   **Workaround** Delete the registry key at: `HKEY_LOCAL_MACHINESOFTWAREPoliciesMicrosoftWindowsPowerShell`
    *   **Workaround** Change the Powershell script execution policy on your Active Directory domain to "Allow all scripts"

## Updates for Visual Studio 2013

We made some focused changes in the extension for Visual Studio 2013 to support the new features of NuGet

*   Introduced the UAP Target Framework Moniker to support Windows 10 Application Development.
*   Support for NuGet protocol version 3 endpoints
*   Support for [nuget.config][4] protocolVersion attribute on repository sources. Default value is "2"

## Summary

There are a number of other lower level fixes in this release, and if you are curious about them you can review the issues list on GitHub. Consult the release notes for [3\.0][5] and [2\.8.6][6] if you are interested in reading more. We're happy with this release, and have another version coming very soon to support the Windows 10 SDK. We'll blog more about that update when it is released. Thank you for your interest, and happy coding!

 [1]: https://visualstudiogallery.msdn.microsoft.com/5d345edc-2e2d-4a9c-b73b-d53956dc458d
 [2]: https://github.com/NuGet/Home/issues/680
 [3]: https://github.com/NuGet/Home/issues/974
 [4]: http://docs.nuget.org/consume/NuGet-Config-Settings
 [5]: http://docs.nuget.org/release-notes/nuget-3.0.0
 [6]: http://docs.nuget.org/release-notes/nuget-2.8.6