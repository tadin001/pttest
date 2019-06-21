---
ID: 225996
post_title: Update All Experience Explained
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/update-all-experience-explained/
published: true
post_date: 2013-12-16 00:00:00
---
While working on NuGet 2.8, we addressed some bugs dealing with the "Update All" behavior, where a single gesture will update all packages in the project. During the testing of those fixes, another bug was filed stating, [After using Update All, packages remain in the Updates list][1]. We have decided to mark that bug as "By Design" for now and get your feedback on it. In order to explain why we consider it by design, let us first explore how install-package or update-package works in the following scenario.

## Scenario

The source repository contains package B versions 1.0.0, 1.0.5, 1.5.0 and 2.0.0. It also contains package A version 2.0.0 which has a dependency on B (>= 1.0.0) and version 1.0.0 which has no dependency on B.

Now, a clean `Install-Package A -version 2.0.0` would install A version 2.0.0 and install ***B version 1.0.0*** since that is the ***lowest version of the dependency*** that meets the dependency version requirements.

Similarly, a clean `Install-Package A -version 1.0.0` followed by `Update-Package A` would first install A version 1.0.0 and then update to 2.0.0 and install ***B version 1.0.0***.

## DependencyVersion

Note that during Install-Package, you can use the -DependencyVersion switch to determine which version of the dependency is pulled in: Lowest/HighestPatch/HighestMinor/Highest. This switch is being introduced in NuGet 2.8.

For the example above,

*   DependencyVersion Lowest would bring in B version 1.0.0
*   DependencyVersion HighestPatch would bring in B version 1.0.5
*   DependencyVersion HighestMinor would bring in B version 1.5.0
*   DependencyVersion Highest would bring in B version 2.0.0

As you may have guessed, *Lowest* is the default value when the switch is not specified. This is true for both Update-Package PowerShell cmdlet and the 'Update' and 'Update All' buttons on the dialog. Before the -DependencyVersion switch was introduced in NuGet 2.8, NuGet always used the *HighestPatch* behavior when resolving dependencies.

## Update All

Now, let us extend the same example to have another package C versions 1.0.0 and 2.0.0. Imagine a project with the following packages installed on it:

*   Package A version 1.0.0
*   Package C version 1.0.0

If we hit 'Update All' on this project, as you could guess, the end result would be the following:

*   Package A version 2.0.0
*   Package B version 1.0.0
*   Package C version 2.0.0

This means that, after 'Update All', there is still one update available for B version 2.0.0. In fact, there are 3 versions of B that are newer than the one installed as part of 'Update All'.

Hence, [Bug-3885][1], which states that "After using Update All, packages remain in the Updates list."

Some argue that for the 'Update All' button and the 'Update-Package' PowerShell cmdlet, DependencyVersion should have been *Highest*. That is incorrect for 3 reasons:

1.  Packages that are not already installed are being updated to the latest version. In the example, B version 2.0.0 gets installed while B version 1.0.0 would have sufficed. This is not preferable.
2.  Package author did not imply it. In the example, author of 'A' wanted 'B' version 1.0.0 to be used with 'A' version 2.0.0. But, 'B' version 2.0.0 gets installed instead.
3.  'Update' and 'Update All' will behave differently from one another making it confusing.

With the current behaviour, you can simply hit 'Update All' again to get the updates for the newly installed packages.

## By Design

In the case of new packages being installed while updating other packages, we've concluded that getting the lowest compatible version of those packages is the right design, for two reasons. First, the user hadn't specifically asked for those packages to be updated. Second, it's a simple extra step to update them once their installed. This seems better than NuGet eagerly updating the packages, making the code a bit more complicated but more importantly making the behavior less predictable.

We will be watching comments on the [issue][2] if you would like to add your thoughts. If there's overwhelming support for making NuGet eagerly get the new updates, we'll reconsider the behavior in NuGet 2.9.

 [1]: http://nuget.codeplex.com/workitem/3885 "Bug-3885"
 [2]: https://nuget.codeplex.com/workitem/3885