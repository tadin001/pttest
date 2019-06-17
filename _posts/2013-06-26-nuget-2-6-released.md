---
ID: 163
post_title: >
  NuGet 2.6 Released and Included in
  Visual Studio 2013 Preview
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-2-6-released/
published: true
post_date: 2013-06-26 00:00:00
---
We are excited to announce that NuGet 2.6 has been released and it is included in the box with Visual Studio 2013 Preview. For details about what's in the release, the [release notes][1] are published on [docs.nuget.org][2].

## NuGet 2.6 Features

1.  Support for Visual Studio 2013
2.  XDT Support
3.  Machine-Wide Configuration
4.  Contextualizing Search
5.  Tracking Direct Installs vs. Dependency Installs

### Support for Visual Studio 2013

NuGet 2.6 is the first release that provides support for Visual Studio 2013. And like Visual Studio 2012, the NuGet Package Manager extension is included in every edition of Visual Studio.

In order to provide the best possible support for Visual Studio 2013 while still supporting both Visual Studio 2010 and Visual Studio 2012, and keeping the extension sizes as small as possible, we are producing a separate extension for Visual Studio 2013 while the original extension continues to target both Visual Studio 2010 and 2012.

Starting with NuGet 2.6, we will publish two extensions as below:

1.  [NuGet Package Manager][3] (applies to Visual Studio 2010 and 2012)
2.  [NuGet Package Manager for Visual Studio 2013][4]

With this split, the [nuget.org][5] home page's "Install NuGet" button will now take you to the [installing NuGet][4] page, where you can find more information about installing the different NuGet clients.

### XDT Support

One of the most highly-requested features for the NuGet client has been to support more powerful XML transformations using the XDT transformation engine which is used in Visual Studio [build configuration transformations][6]. In April 2013, we made two big announcements regarding NuGet support for XDT. The first was that the XDT library itself was being itself [released as a NuGet package][7] and [open sourced on CodePlex][8]. This step enabled the XDT engine to be used freely by other open-source software, including the NuGet client. The second announcement was the plan to support use of the XDT engine for transformations in the NuGet client. NuGet 2.6 includes this integration.

### Machine-Wide Configuration

As more companies are using NuGet internally, with private package sources, it has become important to apply machine-wide configuration for NuGet that can make it easy for developers to get access to these private package sources in addition to the official NuGet package source at <http://nuget.org>. Additionally, some development tools need to expose additional package sources for developers for easy access to packages related to those tools.

With NuGet 2.6, the configuration model has been extended to allow machine-wide package sources to be registered for developers to easily discover those package sources. This is a feature that Visual Studio 2013 itself utilizes to register a ".NET Framework Packages" feed by default.

### Contextualizing Search

As the number of packages served by the NuGet gallery continues to grow at an exponential pace, improving search remains ever at the top of the NuGet priority list. One of the planned features for NuGet is contextual search, meaning that NuGet will use information about the version and SKU of Visual Studio that you are using and the type of project that you are building as criteria for determining the relevance of potential search results.

Starting with NuGet 2.6, each time a package is installed, the context for the installation is recorded as part of the installation operation data. Searches also send the same context information, which will allow the NuGet Gallery to boost search results by contextual installation trends. A future update to the NuGet Gallery will enable this context-sensitive relevance boosting.

### Tracking Direct Installs vs. Dependency Installs

Package authors are relying more and more on the [Package Statistics][9] provided on the NuGet Gallery. One significant missing data point that authors have asked for is a differentiation between direct package installs and dependency installs. Until now, the NuGet client did not send any context around the installation operation for whether the developer directly installed the package or if it was installed to satisfy a dependency. Starting with NuGet 2.6, that data will now be sent for the installation operation. Package Statistics on the NuGet Gallery will expose that data as separate install operations, with a "-Dependency" suffix.

*   Install
*   Install-Dependency
*   Update
*   Update-Dependency
*   Reinstall
*   Reinstall-Dependency

In addition to the different operation name, the dependent package id is also recorded for the installation. A future update to the NuGet Gallery will expose that data within reports, allowing package authors to fully understand how developers are installing their packages.

## Bug Fixes

NuGet 2.6 also includes several bug fixes. For a full list of work items fixed in NuGet 2.6, please view the [NuGet Issue Tracker for this release][10].

 [1]: http://docs.nuget.org/docs/release-notes/nuget-2.6
 [2]: http://docs.nuget.org
 [3]: http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c/file/37502/30/NuGet.Tools.vsix
 [4]: http://docs.nuget.org/docs/start-here/installing-nuget
 [5]: https://nuget.org
 [6]: http://msdn.microsoft.com/en-us/library/dd465318(v=vs.100).aspx
 [7]: https://nuget.org/packages/Microsoft.Web.Xdt
 [8]: http://xdt.codeplex.com/
 [9]: http://blog.nuget.org/20130226/Introducing-Package-Statistics.html
 [10]: https://nuget.codeplex.com/workitem/list/advanced?keyword=&status=Closed&type=All&priority=All&release=NuGet%202.6&assignedTo=All&component=All&sortField=LastUpdatedDate&sortDirection=Descending&page=0&reasonClosed=All