---
ID: 226038
post_title: NuGet 3.0 Beta 2 Released
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-3-0-beta-2-released/
published: true
post_date: 2015-02-26 00:00:00
---
That was a quick turn-around... we issued our 3.0 beta just a few days ago and our community of developers and supporters quickly found a number of issues with it. Thank you to the early reporters on Twitter and on this blog for helping us to identify those issues. We would like to issue an update to our [Visual Studio extension][1] to correct some of the stickier problems that were identified. You can also get a copy of the extension from Codeplex at <http://nuget.codeplex.com/downloads/get/1432773>.

## Fixes Addressed

Our new protocol delivers NuGet package data in JSON format from the nuget.org server. One issue we ran into was a mishandling of the date formatting for non-US machines. We addressed this issue and re-tested with a German workstation to verify that the problem is corrected.

We had a backwards compatibility issue with Visual Studio due to a previously included reference to the Visual Studio v12 shell. That type is not being loaded by Visual Studio 2015 (version 14 internally) and we fixed our references to use the Visual Studio v14 shell.

The final issue we had reported to us was an interesting uninstall of the NuGet extension issue. This is a particularly strange one because it does not happen on all machines, and we have not been able to isolate the trigger scenario. It does appear to be an issue in Visual Studio when uninstalling an extension, and we are working with their team to address this. We suspect that it could be an issue for other extension developers as well.

We do not have a fix for this uninstall issue, but we do have a workaround if you encounter it. We recommend that you follow these steps if you encounter it:

1.  Uninstall the existing NuGet extension that you retrieved from the extension gallery.
2.  With Visual Studio closed, download a new copy of the [NuGet extension from Codeplex][2] instead of through the extension gallery.
3.  Double-click the VSIX you downloaded to install a fresh copy of the NuGet extension.
4.  When you next start Visual Studio, if you are prompted with an error about needing Admin privileges again, take the following steps: 
    1.  Close Visual Studio
    2.  Start the Visual Studio Command Prompt, called "Developer Command Prompt for VS2015" as an Admin
    3.  Manually install the NuGet VSIX using VsixInstaller with the /admin switch. eg: `VSIXInstaller.exe c:installnuget.tools.2015.vsix /admin`

## kpm incompatibility

A note about using NuGet 3 with kpm: a new kpm client has not been issued that is compatible with the NuGet 3 API. This means that if you are in an ASP.NET 5 project, you cannot use the NuGet v3 endpoint. For these projects, we recommend that you disable the api.nuget.org service in the package sources dialog as follows:

![][3]

## Legacy package restore incompatibility

If you have a project with in the 'legacy package restore mode', with the .nuget folder at the root of your solution folder, you will run into a similar API incompatibility issue. In this scenario, the nuget.exe client program that you have copied locally does not understand the v3 API and will not be able to restore packages for you using the api.nuget.org endpoint. You have two options:

1.  Disable the api.nuget.org endpoint as described above, enabling the v2 endpoint.
2.  [Migrate from legacy package restore mode to automatic package restore][4]. 

## Summary

We responded quickly to these issues because we heard your feedback, and we value our developers and customers who are using our NuGet extension for Visual Studio. It is important to us that you have a good experience while using our product. We are committed to improving your package management experience, and we will deliver on that promise.

 [1]: https://visualstudiogallery.msdn.microsoft.com/5d345edc-2e2d-4a9c-b73b-d53956dc458d,
 [2]: http://nuget.codeplex.com/downloads/get/1432773
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/config.png
 [4]: http://docs.nuget.org/consume/package-restore/migrating-to-automatic-package-restore