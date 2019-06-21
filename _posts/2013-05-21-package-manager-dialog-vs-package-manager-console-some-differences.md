---
ID: 225966
post_title: >
  Package Manager Dialog vs. Package
  Manager Console, some differences!
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/package-manager-dialog-vs-package-manager-console-some-differences/
published: true
post_date: 2013-05-21 00:00:00
---
One common misconception we see among NuGet users is that NuGet could only install/ update to the latest version of a package in VS. This is due to the fact that through 'NuGet Package Manager Dialog' we only show that latest (stable or prerelease) version of any package. But, NuGet provides the ability to install/ update to **any specific version** of a package through Package Manager Console. Let's see an example below on how to do this.

## Install a specific version of a package

To bring up Package Manager Console in VS, go to View->Other Windows->Package Manager Console.

![Package Manager Console][1]

You could use the highlighted dropdowns above to set the Package Source and the Project in which the package should be installed.

To install EntityFramework 4.3.1, you could type the following in the console.

***Install-Package EntityFramework -Version 4.3.1***

In addition to being able to install a specific version of a package, there are a few other things that could be done through Package Manager Console, that are not available through UI. Let's see those features below.

## Reinstall packages that have already been installed

There may be instances when you would like to install a package that is already installed. For example, in some build or CI scenarios, the package version may not be incremented but you may want to install the latest produced package. This cannot be done through the Package Manager Dialog as NuGet will notify that the package is already installed. However, through Package Manager Console you could reinstall an existing package. Let's say you have SamplePackage 1.8 already installed and you want to reinstall it. You can use the following command to reinstall SamplePackage. Reinstall will always install the same version that was previously installed.

***Update-Package SamplePackage -reinstall***

**Please note that -reinstall flag works only on NuGet Clients 2.1 and above**

## Ignore dependencies when installing a package

Another thing that you could do through the Package Manager Console but not through the Package Manager Dialog is installing a package without it's dependencies. To do this, you could use the -ignoreDependencies switch with Install-Package command. For example, jQuery.Validation package has a dependency on jQuery. When you install through the Package Manager Dialog the dependencies will always get installed. However, executing the following command in Package Manager Console will only install jQuery.Validation and not jQuery.

***Install-Package jQuery.Validation -ignoreDependencies***

## Force

The next thing that you could do with Package Manager Console easily is to force uninstall of a package that is being dependent upon by other packages. Let's take the example of jQuery and jQuery.Validation in a project. When you try to uninstall jQuery from the project through the Package Manager dialog, you will see the following UI that tells you that it can't be done.

![Failure on uninstall of a parent package][2]

In case of Package Manager Dialog, you have to first uninstall the packages that have jQuery dependency before you can uninstall jQuery itself. However, with Package Manager Console, you can use the -force switch to uninstall the jQuery package.

***UnInstall-Package jQuery -force***

## Source

When you install NuGet, it automatically comes with one package source - the NuGet official package source. If you would like to install packages from other sources you need to go to **Tools->Options->Package Manager->Package Sources** to add other custom sources. But, if you would like to install a package from a custom source and not have it as one of your sources in your package sources, you can quickly do it through Package Manager Console using the -Source switch. An example is given below.

***Install-Package MyPackage -Source PATH_TO_YOUR_CUSTOM_PACKAGE_SOURCE***

The above are some of the differences between Package Manager Dialog and Package Manager Console and highlight how you could use the Package Manager Console to do some additional things that are not possible through the dialog. Have fun exploring the Package Manager Console!

-Ranjini

 [1]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/PMC.png
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/UninstallFailure.png