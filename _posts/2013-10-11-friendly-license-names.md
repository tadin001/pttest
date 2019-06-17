---
ID: 172
post_title: Friendly License Names
author: Howard Dierking
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/friendly-license-names/
published: true
post_date: 2013-10-11 00:00:00
---
Yesterday afternoon, we enabled a new feature for both the NuGet gallery and the Visual Studio client (for NuGet >= 2.7) which will display the list of license names for a package in addition to simply providing a link to the license text. The big idea behind this feature is that it will aid in the decision making process over whether or not to use a package.

While displaying text such as "MIT" may seem like a trivial problem, there is a significant amount of complexity involved in determining the name from a license document. In this feature, license names are determined as a result of analyzing the license text itself. 

For this analysis, [NuGet has partnered with Sonatype][1] and leveraged the power of their component lifecycle management (CLM) system. You may remember that in early 2012, Sonatype began enabling polyglot development shops to manage both their Java and .NET components with one repository when they [included NuGet support in their Nexus repository][2]. As such, when Sonatype released CLM, it seemed only natural that we leverage our partnership to bring some of the power of that system to the NuGet community.

While there’s a lot happening behind the scenes, browsing license names shows up in some pretty simple ways in the NuGet clients. 

On the gallery, you can see license names in search results or package listings:

![License names in search results][3]

Or on an individual package details page:

![License names on package details page][4]

In Visual Studio, the license name shows up just beneath the existing license link:

![License names in Visual Studio][5]

In the event that a license URL was not provided in the source package, or if the license could not be determined, no license name will be displayed. Finally, as the owner of a package, the NuGet gallery provides control over whether or not the analyzed license information should be displayed to users.

![Managing license name visibility][6]

We plan to continue exploring ways that we can reduce friction around package selection by incorporating more of these kinds of “package intelligence” capabilities into the NuGet client and server. If you have a specific scenario that you think would be helpful here, let us know!

 [1]: http://www.sonatype.com/news/sonatype-reduces-licensing-risks-with-new-update-to-nuget-and-visual-studio#.Ulgp8NxDsqw
 [2]: http://www.sonatype.com/news/sonatype-brings-industry-leading-repository-manager-to-net-developers#.UlgqG9xDsqw
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/license-names-package-list.png
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/license-names-package-details.png
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/license-names-vs-dialog.png
 [6]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/license-names-disable.png