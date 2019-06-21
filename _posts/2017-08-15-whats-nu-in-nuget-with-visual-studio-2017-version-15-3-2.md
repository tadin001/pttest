---
ID: 1002
post_title: 'What&#8217;s Nu in NuGet with Visual Studio 2017 version 15.3?'
author: Serhii Rohatin
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/whats-nu-in-nuget-with-visual-studio-2017-version-15-3-2/
published: true
post_date: 2017-08-15 16:17:19
---
We are happy to announce an update to the NuGet client that comes bundled with Visual Studio 2017 version 15.3 RTW and .NET Core 2.0 SDK. This release introduces support for new scenarios such as .NET Core 2.0/.NET Standard 2.0, some new features, a series of bug fixes and performance improvements. You can see more details, known issues, and workarounds associated with this release in our [release notes][1]. Here are some links to get started with using the latest NuGet functionality:

*   [Visual Studio 2017 version 15.3][2]
*   [.NET Core 2.0][3]
*   [NuGet.exe][4]
*   [Visual Studio 2015 – NuGet Package Manager update (v3.6.0)][4]

## TL;DR

Our major focus for this release was adding support for .NET Core 2.0. In addition, we made some other improvements:

*   [Support for new TFMs][5]
*   [MSBuild integration of NuGet warnings and errors][6]
*   [Support for Semantic Versioning 2.0.0][7]

For Visual Studio 2015, you can now start using .NET Standard 2.0 packages by installing the latest [NuGet client (v3.6.0) for Visual Studio 2015][8].

We are also happy to announce that we have integrated the NuGet.org facelift we had previously previewed back into the NuGet.org site and results in the following improvements available to all NuGet.org users:

*   [An accessible NuGet.org site][9]
*   [Prominent package details page][10]
*   [Streamlined account settings][11]

## Support for new TFMs

We have added support for the following TFMs - `netcoreapp2.0` and `netstandard2.0`. We also enabled `Tizen` as a supported TFM that helps integration of Tizen .NET libraries with Xamarin Forms. The complete list of supported TFMs is reflected in this [documentation][12].

## MSBuild integration of NuGet warnings and errors

We have integrated NuGet warnings and errors with MSBuild so you can use existing MSBuild properties to suppress certain warnings or elevate them to errors. In addition, we have also improved warnings and errors to output relevant information and thereby making them more actionable. All NuGet warnings/errors now have a unique number associated with them as documented [here][13].

**Note**: These improvements are only available to project systems that support expressing NuGet dependencies using [PackageReference][14].

### How does it work?

As an example, when you install a .NET Framework package (say RestSharp) into a .NET Core 2.0 project, you will see the following [NU1701][15] warning:

    Warning NU1701 Package 'RestSharp 105.2.3' was restored using '.NETFramework,Version=v4.6.1' instead of the project target framework '.NETCoreApp,Version=v2.0'. This package may not be fully compatible with your project. 
    

If you are okay with the risk as stated by the warning and do not want to see it in future restores/builds, you can do one of the following:

#### Suppress the warning in the project on a PackageReference basis

To suppress the warning, you can go to the package properties window and add

[NU1701][15] to the NoWarn property: ![Suppress warning for package reference][16] This results in the following entry being created in the project file: NU1701

#### Suppress the warning at a project level

If you do not want to see the warning at all in the project irrespective of the package reference that introduces it, you can suppress it in the project’s build properties:

![Suppress warning at a project level][17] This results in the following entry being created in the project file: NU1701

#### Elevate a warning to an error

In case you want to elevate a specific warning to an error, you can do so by specifying the same in the Build properties:

![Elevate warning to error][18] This results in the following entry being created in the project file: NU1605

#### A word of caution!

If you have set your project’s build property to treat all warnings as errors, you may start to see your builds fail because of the NuGet warnings now being flagged as errors. You can choose to either fix these warnings and/or suppress them using one of the mechanisms specified above.

## Support for Semantic Versioning 2.0.0

[Semantic Versioning 2.0.0][19] specification has been out for a long time and we have gotten multiple requests to support it. We have been listening to your requests and [feedback][20] and are happy to announce SemVer 2.0.0 support for NuGet starting with our latest client releases including NuGet 4.3.0, Visual Studio 2017 version 15.3 and dotnet.exe 2.0.0. We are also making this support available for Visual Studio 2015 as a separate [download][4].

### A word of caution!

If your package version contains one of the following, they will not be visible from the older clients (NuGet clients before 4.3.0 or Visual Studio before 2017 version 15.3):

1.  The pre-release label is dot-separated, e.g. 1.0.0-alpha.1
2.  The version has build-metadata, e.g. 1.0.0+githash We recommend you consider this to be a beta-feature until a significant majority of our users are on these latest clients that understand Semantic Versioning 2.0.0. Read more on Semantic Versioning 2.0.0 NuGet support, [here][21].

## Update to the NuGet client in Visual Studio 2015

We wanted to ensure that developers using Visual Studio 2015 can use packages targeting .NET Standard 2.0, and have created an [update][4] to the NuGet client in Visual Studio 2015 that adds the following improvements:

*   Support for consuming .NET Standard 2.0 packages into the compatible projects

*   Support for Semantic Versioning 2.0.0 packages

*   Fix for UI hang bug as described in NuGet [#4976][22]

## The facelift to NuGet.org site is live!

A month ago, we had announced a significant redesign of the NuGet.org site. We got a ton of great feedback, and for that, we are always grateful. We were able to address a majority of that feedback as listed below (See full list of issues [here][23]):

### Condensed list for ‘Manage Packages’ [#4431][24]

We have improved the author’s view of packages they own to make it more manageable – sorted and compact:

![Condensed Manage packages list][25]

### Search results consistency with Visual Studio [#3791][26], [#4493][27]

We have made the package ID to be the consistent identifier (header) for the packages that show up on NuGet.org or Visual Studio:

![Condensed Manage packages list][28]

The NuGet.org package search has a new filter for prelease packages so that search results would be consistent with this existing functionality on Visual Studio:

![Include pre release filter][29]

In addition, we have made some improvements to package search results to fit more items by reducing the package ID font size and by removing the unnecessary separator line. We will continue to explore more ways to improve this page. In case you missed the improvements we made to NuGet.org, here is a quick summary of our previous [blog post][30]:

### An accessible NuGet.org site

We are committed to making sure that NuGet.org is fully accessible to all users. We addressed many accessibility issues for this preview in accordance with [Section 508 Laws][31] and [Web Content Accessibility Guidelines (WCAG 2.0 AA)][32]. If you find any accessibility issues, please let us know by [filing an issue on GitHub][33]. Some of the changes we’ve made include:

*   Updating tab order on all pages to ensure proper keyboard navigation

*   Setting ARIA attributes on elements to make sure screen-reader technologies work on the website

*   Ensuring all interactive elements have visual feedback for when they are focused by the keyboard

*   Setting text colors to properly contrast with backgrounds with a minimum ratio of 4.5:1
    
    ![Accessibility Demo][34]

### Prominent package details

During our discussions, we heard from you what information is important when determining the viability of a package. The importance of various aspects like freshness, download count, license type, access to a project site, and many more influenced the design of a more streamlined package details page.

![Package Details Page][35]

We created sections to divide package details to make it easier to parse through all the information to get a better understanding of a package. All the vital information is placed at the top of the page to be seen first when navigating to the page. Additionally, the project site now displays as the full URL (if it is under a certain number of characters), publish date is more readable, and license type is made more prominent. Now that NuGet is supported by a variety of consoles, we wanted to support the many ways users can add packages to their projects. We’ve made it easier to copy the command to install a package and added a new tab for the command to install using the .NET CLI.

![CLI Support Demo][36]

### Streamlined account settings

We’ve made it easier for users to access their account settings, moving away from a centralized account page that combined unrelated settings together. Now, API keys and package management controls can be accessed from any page with the account dropdown. It’s much easier for authors to manage their packages.

![New Account Settings Menu][37]

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. In case you missed it, we recently published a [post][38] that describes some of the experiences we are working in the near future. Share your thoughts with us at <feedback@nuget.org>. You can also leave a comment below, and as always, if you run into any issues or have an idea, open an issue on GitHub: [NuGet client issues][39], [NuGet server issues][40]

 [1]: https://docs.microsoft.com/en-us/nuget/release-notes/nuget-4.3-rtm
 [2]: https://www.visualstudio.com/downloads
 [3]: https://www.microsoft.com/net/download/core
 [4]: https://nuget.org/downloads
 [5]: #support-for-new-tfms
 [6]: #msbuild-integration-of-nuget-warnings-and-errors
 [7]: #support-for-semantic-versioning-200
 [8]: #update-to-the-nuget-client-in-visual-studio-2015
 [9]: #an-accessible-nugetorg-site
 [10]: #prominent-package-details
 [11]: #streamlined-account-settings
 [12]: https://docs.microsoft.com/en-us/nuget/schema/target-frameworks#supported-frameworks
 [13]: https://docs.microsoft.com/en-us/nuget/reference/errors-and-warnings
 [14]: https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files
 [15]: https://docs.microsoft.com/en-us/nuget/reference/errors-and-warnings#nu1701
 [16]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/SuppressWarningPerPackage.png
 [17]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/SuppressWarningPerProject.png
 [18]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/ElevateWarningAsError.png
 [19]: http://semver.org/#semantic-versioning-200
 [20]: https://github.com/NuGet/Home/issues/3610
 [21]: https://docs.microsoft.com/en-us/nuget/reference/package-versioning#semantic-versioning-200
 [22]: https://github.com/NuGet/Home/issues/4976
 [23]: https://github.com/NuGet/NuGetGallery/issues?q=label%3A"Redesign%20Feedback"%20
 [24]: https://github.com/NuGet/NuGetGallery/issues/4431
 [25]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/CondensedManagePackagesList.png
 [26]: https://github.com/NuGet/NuGetGallery/issues/3791
 [27]: https://github.com/nuget/nugetgallery/issues/4493
 [28]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/PackageTitleAndIDConsistency.png
 [29]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/IncludePrereleaseFilter.png
 [30]: http://blog.nuget.org/20170718/NuGet-Gallery-Gets-A-Facelift.html
 [31]: https://www.section508.gov/content/learn/laws-and-policies
 [32]: https://www.w3.org/TR/WCAG20/
 [33]: https://github.com/NuGet/NuGetGallery
 [34]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-Accessibility-Focus.gif
 [35]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-PackageInfo-Overview.png
 [36]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-PackageInfo-CLI.gif
 [37]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-AccountSettings.gif
 [38]: https://blog.nuget.org/20170809/NuGet-Fall-2017-Roadmap.html
 [39]: https://github.com/NuGet/Home/issues
 [40]: https://github.com/NuGet/NuGetGallery/issues