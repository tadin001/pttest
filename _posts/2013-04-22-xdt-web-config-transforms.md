---
ID: 225954
post_title: XDT web.config transforms
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/xdt-web-config-transforms/
published: true
post_date: 2013-04-22 00:00:00
---
Sitting there since October 12, 2010, with a total of 55 votes accumulated since then, there's a NuGet feature that has had more than its fair share of attention: [Support Visual Studio (XDT) Web.config Transforms][1]. It's the 17th highest voted work item of all time for NuGet, and the 5th highest voted *open* work item. Today, we are announcing that we are finally unblocked on getting this feature integrated into NuGet!

Last November, we made the decision to act on this feature request, and Luan Nguyen ([dotnetjunky][2]) posted a [discussion thread][3] that NuGet would be taking a dependency on the XDT assembly. Within a few hours, Andreas Håkansson ([thecodejunkie][4]) pointed out a really good glitch in our plan: we needed to make sure the feature would work on Mono!

Fast forward 4 months, and we have resolution for the requirement of XDT support working on Mono--Microsoft has open-sourced the XML Document Transform (XDT) library at <http://xdt.codeplex.com>, and NuGet will be taking a source dependency on XDT rather than a binary dependency.

Here's the net result of the work we've done with the team that develops the XDT library:

1.  A [Microsoft.Web.Xdt][5] NuGet package is published to nuget.org.
2.  The NuGet team used that NuGet package to prototype integration of NuGet and XDT.
3.  The XDT library is open-sourced (not accepting contributions) at [xdt.codeplex.com][6].
4.  The NuGet team will now use the XDT source to provide the integration.
5.  This allows all NuGet users on all platforms to use the feature. NuGet can be built on Mono and have the XDT source built there too.

NuGet package authors will be able to create two new files in their packages to perform XDT transforms:

1.  web.config.install.xdt (and yes, files other than web.config are supported)
2.  web.config.uninstall.xdt

These files can use XDT functionality to transform XML files during install, and then to reverse the transformations upon uninstall. It's a concept very similar to install.ps1/uninstall.ps1, but using the XDT library for the XML transforms.

We are working on the source-based integration of this feature now, and we plan to include it in the NuGet 2.6 release in early summer 2013.

For more information about the XDT library and its open source project, check out the [.NET Web Development and Tools blog post][7].

 [1]: https://nuget.codeplex.com/workitem/232
 [2]: https://www.codeplex.com/site/users/view/dotnetjunky
 [3]: https://nuget.codeplex.com/discussions/405195
 [4]: https://www.codeplex.com/site/users/view/thecodejunkie
 [5]: http://nuget.org/packages/Microsoft.Web.Xdt/
 [6]: http://xdt.codeplex.com
 [7]: http://blogs.msdn.com/b/webdev/archive/2013/04/23/xdt-xml-document-transform-released-on-codeplex-com.aspx