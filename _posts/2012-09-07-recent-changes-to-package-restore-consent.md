---
ID: 225931
post_title: >
  Recent Changes to Package Restore
  Consent
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/recent-changes-to-package-restore-consent/
published: true
post_date: 2012-09-07 00:00:00
---
When we released NuGet 2.0, one of the changes included the addition of a [privacy-related constraint][1] to the package restore feature. More specifically, NuGet 2.0 required that you provide explicit consent, via either a checkbox in the package manager configuration dialog or an environment variable, before it would successfully download required packages as a part of the package restore workflow.

The constraint was added specifically with build scenarios in mind. However, the implementation extended to other scenarios and as a result, added some unhappiness to the overall NuGet experience. Therefore, we’ve revisited the implementation and limited its impact to only the scenario that it was designed to address and none other.

The following changes have been made to the package restore workflow.

*   Added a ‘-RequireConsent’ command line flag to nuget.exe that, when present, causes NuGet to check for consent (this is the default behavior in NuGet 2.0)
*   Modified the NuGet targets file which is created by enabling package restore such that it passes the flag to nuget.exe when restoring packages during build

The impact of this change is twofold.

*   The default package restore workflow will continue to check for consent as it does today
*   Calling nuget.exe will no longer check for consent unless you explicitly pass the flag to the executable

The hope here is that while we need to continue checking for consent in the build scenario (since downloading is not a known or obvious build step), this change will eliminate any additional frustrations related to requiring consent when simply using nuget.exe by itself.

Additionally, because of the [mechanics behind package restore][2], you won’t have to wait until the next NuGet release to take advantage of these changes. They are already available today as a part of the [nuget.exe CodePlex download][3] and the [NuGet.Bootstrapper package][4]. The act of enabling package restore for a solution will cause the latest version of nuget.exe to be downloaded automatically, so no further action is needed in this scenario. If you have an existing solution where package restore is already enabled, you can update it by doing the following:

*   Remove the .nuget folder from the solution in Visual Studio
*   Delete the .nuget folder from disk (keep Visual Studio open)
*   Right-click on the solution in the Visual Studio solution explorer and re-enable NuGet package restore

The process of enabling package restore will then run again, which will in turn download the latest version of nuget.exe and nuget.targets. Also, if you happened to close Visual Studio before deleting the .nuget folder from disk, you may notice that your projects fail to load when re-opening the solution. In this case, simply right-click on the solution and re-enable NuGet package explorer, then right-click on each of the failed projects and reload them.

 [1]: http://blog.nuget.org/20120518/package-restore-and-consent.html
 [2]: http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages#Details
 [3]: http://nuget.codeplex.com/releases/view/58939
 [4]: http://nuget.org/packages/nuget.bootstrapper