---
ID: 225979
post_title: NuGet 2.7 Package Restore Consent Errors
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-2-7-package-restore-consent-errors/
published: true
post_date: 2013-08-29 00:00:00
---
After NuGet 2.7 was released with Automatic Package Restore and implicit consent, some users reported that they were still seeing build errors indicating that package restore consent had not been given. When we made the package restore changes in NuGet 2.7, we identified one scenario where this would happen but determined we couldn't implement a fix and would rather have to document the cause and solution.

## Scenario and Cause

In order to hit this problem, there's a specific scenario you must be in. The scenario isn't extremely common, but we have already heard reports of it.

1.  Using a freshly built machine or a machine where Visual Studio was freshly installed
2.  NuGet has been upgraded to NuGet 2.7
3.  An existing solution is opened where the MSBuild-integrated package restore has already been enabled
4.  The MSBuild-integrated package restore was enabled before NuGet 2.7 was released

In this scenario, the solution contains a .nuget folder that has a nuget.exe in it versioned 2.6 or earlier. In those versions of nuget.exe, package restore consent was OFF by default (hard-coded to false when not present in nuget.config). When building this solution in Visual Studio, NuGet identifies that the MSBuild-integrated package restore is enabled and therefore automatic package restore is skipped. Then the old nuget.exe kicks in for the package restore and it does not find package restore consent to be given, yielding the following build error:

Package restore is disabled by default. To give consent, open the Visual Studio Options dialog, click on Package Manager node and check 'Allow NuGet to download missing packages during build.' You can also give consent by setting the environment variable 'EnableNuGetPackageRestore' to 'true'.

## Solution

As explained on the [Package Restore][1] documentation, there are three ways to address this situation.

1.  **Force save your NuGet settings with consent given.** To do this, open Visual Studio's options and under Package Manager, choose General. Uncheck and then re-check the boxes for consent and click OK. This forces your %AppData%NuGetNuGet.config file to be saved with consent explicitly given, allowing NuGet 2.6 and earlier to see that you've given consent.
2.  **Update the version of NuGet.exe in your .nuget folder.** To do this, run `nuget.exe update -self` from your .nuget folder, which will download the latest version of NuGet.exe and replace the version in the .nuget folder. The latest version of NuGet.exe will infer consent to be ON even when not explicitly saved in the NuGet.config file.
3.  **Migrate to Automatic Package Restore.** For this approach, you would migrate from the MSBuild-integrated package restore to the Automatic Package Restore approach, following the [documented walkthrough][2].

 [1]: http://docs.nuget.org/docs/reference/package-restore
 [2]: http://docs.nuget.org/docs/workflows/migrating-to-automatic-package-restore