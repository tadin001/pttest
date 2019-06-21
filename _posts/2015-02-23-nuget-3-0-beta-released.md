---
ID: 226036
post_title: NuGet 3.0 Beta Released
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-3-0-beta-released/
published: true
post_date: 2015-02-23 00:00:00
---
Its time for the 3.0 beta release for NuGet, and this time we have some much sought-after enhancements in the works for you. You can get access to the update NuGet binaries on [Codeplex][1] and through the Visual Studio extension manager. We are only releasing these preview bits for Visual Studio 2013 (on CodePlex only) and for Visual Studio 2015. We are releasing the Visual Studio 2013 version on Codeplex so that we don't accidentally deliver a preview extension version into a production development environment. We will be delivering a new version of the extension for Visual Studio 2012 in the near future.

We strongly recommend that you uninstall any prior version of the NuGet Visual Studio extension before installing this new version. If you have any problems with this version of the extension, we recommend you revert to the [prior version][2] for use with Visual Studio 2015 preview.

Let's take a look at some of the updates:

## Visual Studio Extension Improvements

![Visual Studio Extension UI][3]

The user interface is no longer modal, and is a dockable window in Visual Studio with wrapping enabled for smaller screens. That's cool because now we can see a lot more of the package titles and descriptions that our package authors have worked so hard on.

Across the top of the window is the name of the project you are currently working in. Below that is the package source - a combobox of the various NuGet repositories available on your workstation. The Filter combobox is next and it replaces the old accordion display that was on the left side of the screen. There are three options in this box: All, Installed, and Upgrade Available. This should allow you to quickly review the packages you have installed or review just those packages where updates are waiting to be installed. Otherwise, the All option will show you packages available from your selected repository.

The 'Include Prerelease' checkbox is fairly self-explanatory, showing those packages and their versions that are in prerelease state. Next is the search text box. We've gone to great lengths to improve the speed of the search on NuGet. It was our number one requested feature, and we put a lot of work into making this a very fast search operation. Check out the [attached video][4] for a side-by-side demonstration of Visual Studio 2013 with the old add-in and Visual Studio 2015 with the new add-in. We achieved this performance improvement through the implementation of new search algorithms and a smaller payload of data being delivered from the NuGet.org servers in the JSON-LD format. Our [previous blog post][5] discusses this change further.

We've also cleaned up the action pane on the right side of this dialog. You can now select an action you'd like to take with your package and click a button to enact that action. Any necessary configuration options for that action will be provided in the Options segment below.

We heard your concerns about the preview UI that we provided, and the experience when you install, upgrade, or uninstall packages. We've cleaned up that experience significantly. We will (optionally) show you a preview of what changes NuGet will make to your project, and then shift focus to the Output Window's Package Manager pane where you can observe and review the detailed log messages reported by NuGet. This should feel very similar to completing a standard build for your project, something that we are sure all Visual Studio developers are familiar with.

![Package Manager Output Window][6]

## New Powershell Cmdlets

We are beginning to introduce and migrate functionality in our Powershell commands in this version of NuGet. We are starting with moving the package search functionality to the Find-Package cmdlet. This allows us to keep package searching mechanisms separate from package fetching commands.

We have also introduced the Sync-Package cmdlet that provides the same 'consolidate' functionality that the Visual Studio UI introduced. We will be continuing to deliver parallel functionality in the UI and in powershell. No more flipping back and forth between these two clients because one has more functionality that the other.

We've also introduced some new switches to old favorite commands:

*   Install-Package now has a -Force switch that will force an uninstall and re-install of the same package
*   Install-Package can now accept a local or remote nupkg address or config file listing packages. For example: install-package <https://raw.githubusercontent.com/NuGet/json-ld.net/master/src/JsonLD/packages.config>
*   Update-Package now has a -Version switch that accepts an enum value of Highest, HighestMinor, and HighestPatch to indicate which package version NuGet should install

## Re-architecting for a purpose

We have spent some time segmenting the NuGet source code to make re-use and modernization of the code much easier. This allows us to engage and deliver functionality better, as that code is now more clearly segmented into client, configuration, and communications. We are completing a move to a [GitHub source code repository][7], and we think you will be very happy to see what we have prepared there for you to interact with. We'll have a more formal announcement when that move is completed.

## Building NuGet with NuGet

That sounds confusing, but this is an important step for us. As the .NET framework starts to deliver the base class library with NuGet, we need to be able to support and deliver development practices that rely on NuGet as part of the build process for every reference in your project. Our build process now compiles and delivers each of the segments of the NuGet application as a package for the NuGet client applications to consume. This gives us maximum re-use of code and demonstrates to ourselves some of the issues that developers will have as they move into this style of coding. This practice has given rise to some enhancement considerations for the NuGet client experience to improve this workflow.

## Summary

These are just a few of the changes in store for you when you install the new NuGet 3.0 beta that is now available. We encourage you to download a copy and give it a try. The performance improvements are significant, the UI updates should make things easier, and we're listening to your feedback. Please comment below about what you like, don't like, or your suggestions for improvement. We'll publish a follow-up post with some of our ideas for next steps in the weeks ahead.

 [1]: http://nuget.codeplex.com/releases
 [2]: http://nuget.codeplex.com/downloads/get/909582
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/ui.png
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/nuget-perf.mp4
 [5]: http://blog.nuget.org/20140424/building-nuget-3.x.html
 [6]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/output.png
 [7]: http://github.com/nuget