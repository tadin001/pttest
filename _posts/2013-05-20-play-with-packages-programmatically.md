---
ID: 225962
post_title: Play with Packages, programmatically!
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/play-with-packages-programmatically/
published: true
post_date: 2013-05-20 00:00:00
---
One of the many often seen requests, from developers using NuGet, is examples on our NuGet core APIs. The requests range from finding meta-data for the packages to installing the package programmatically. In this blog post, I will provide a few examples of using NuGet Core APIs.

### Getting Started

To use NuGet Core APIs, install **NuGet.Core** NuGet package in your project and add **NuGet** namespace to your **using** statements.

### Get me the pre-released versions of a package

Let's say you would like to get the list of all pre-released versions of **EntityFramework** package. Below is the code snippet that would get you the list using NuGet API v2.

    //ID of the package to be looked up 
    string packageID = "EntityFramework"; 
    
    //Connect to the official package repository 
    IPackageRepository repo = PackageRepositoryFactory.Default.CreateRepository("https://packages.nuget.org/api/v2"); 
    
    //Get the list of all NuGet packages with ID 'EntityFramework' 
    List<IPackage> packages = repo.FindPackagesById(packageID).ToList(); 
    
    //Filter the list of packages that are not Release (Stable) versions 
    packages = packages.Where (item => (item.IsReleaseVersion() == false)).ToList(); 
    //Iterate through the list and print the full name of the pre-release packages to console 
    foreach (IPackage p in packages) 
    { 
      Console.WriteLine(p.GetFullName()); 
    }
    

### Help me get a package

Let's say you would like to programmatically download and unzip a package in the path you want. Below is the code snippet that you would use to download and unzip the package in the path specified. The example below downloads and unzips **EntityFramework 5.0.0** to one of my projects.

    //ID of the package to be looked 
    up string packageID = "EntityFramework"; 
    
    //Connect to the official package repository IPackageRepository
    repo = PackageRepositoryFactory.Default.CreateRepository("https://packages.nuget.org/api/v2"); 
    
    //Initialize the package manager string path = <PATH_TO_WHERE_THE_PACKAGES_SHOULD_BE_INSTALLED>
    PackageManager packageManager = new PackageManager(repo, path); 
    
    //Download and unzip the package 
    packageManager.InstallPackage(packageID, SemanticVersion.Parse("5.0.0"));
    

### It's quite easy, have fun!

As illustrated by the above examples, trying to work with NuGet packages programmatically is quite easy. Start exploring them today!

-Ranjini