---
ID: 226072
post_title: Accelerate your NuGet.Server
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/accelerate-your-nuget-server/
published: true
post_date: 2016-01-13 00:00:00
---
Since its inception, NuGet supports setting up custom package sources. There's the default [NuGet Gallery][1], there's third-party NuGet hosting (see the [NuGet Ecosystem][2]), we can use local directories on our hard drive or a network UNC share (like `&#092;yourservernuget`) and there's the [NuGet.Server][3] package that we can use to create our own NuGet server.

Developers and teams have been using NuGet.Server to host and share their NuGet packages internally without exposing them publicly on nuget.org. It's easy to set up: [install a NuGet package][4] and we can work with our own NuGet server over HTTP(S).

Some users went above and beyond in hosting their own packages and grew their package repository to several hundreds of packages, a tipping point where NuGet.Server performance would suffer. Today we're releasing an update that increases performance in these cases.

This update also comes with support for new target frameworks, supporting (among others) the new capabilities of ASP.NET 5 and DNX.

## Improving NuGet.Server performance

Up until [version 2.8.6][5] the NuGet.Server package has been using a flat folder structure to store packages. For example, this is what the server's packages folder could look like:

    ~/packages/PackageA.1.0.0.nupkg 
    ~/packages/PackageA.1.0.1.nupkg 
    ~/packages/PackageB.0.0.1-beta.nupkg 
    ~/packages/PackageB.0.0.1.nupkg
    

When accessing NuGet.Server, these packages (which are actually zip archives) would all be unzipped to a temporary folder to extract their metadata so that it could be served up on the exposed NuGet feed. This extraction would happen whenever NuGet.Server's application pool was restarted as well as right after a package was pushed. To make things worse, each package would be read a second time to calculate the package hash which is surfaced in the NuGet feed. Bottom line: high-churn feeds with many packages were doing more unzipping than serving packages.

After we [introduced a new, expanded folder format][6] for directory based NuGet package sources, we started thinking about how using this structure could benefit users of NuGet.Server as well. We decided it would increase performance a lot! The expanded folder format would look like this:

    ~/packages 
            /PackageA 
                /1.0.0 
                    /PackageA.1.0.0.nupkg 
                    /PackageA.nuspec 
                    /PackageA.1.0.0.nupkg.sha512
    

In this structure, every package is analyzed when pushed to the server. Of course, the .nupkg is stored so it can be served to the client. Next to the package, we extract the .nuspec containing the package's metadata and write the package hash to disk as well.

All NuGet.Server has to do on startup is read the .nuspec files for metadata which is much faster than unzipping all packages. Since the hash is also precalculated when a package is added to the feed, we no longer have to recalculate it - resulting in more performance gains.

Why stop there? While we were at it, we decided we could also persist the in-memory package metadata cache that is created and load it again on the next server start. This improves NuGet.Server start time tremendously - only one file has to be read at startup if the persisted cache exists.

### How much faster is it?

Of course we did some tests before and after upgrading NuGet.Server. Here are the results on an i7 with SSD:

table, th, td { border: 1px solid black; border-collapse: collapse; } th, td { padding: 5px; } 

<table>
  <thead>
    <tr>
      <th>
      </th>
      
      <th>
        Before upgrade
      </th>
      
      <th>
        After upgrade
      </th>
    </tr>
  </thead>
  
  <tbody>
    <tr>
      <td>
        # of files
      </td>
      
      <td>
        6,875
      </td>
      
      <td>
        20,625
      </td>
    </tr>
    
    <tr>
      <td>
        # of packages
      </td>
      
      <td>
        6,875
      </td>
      
      <td>
        6,875
      </td>
    </tr>
    
    <tr>
      <td>
        Space consumed on server
      </td>
      
      <td>
        3.94 GB
      </td>
      
      <td>
        3.98 GB
      </td>
    </tr>
    
    <tr>
      <td>
        First request to <code>/nuget</code> (cold start, no metadata cache)
      </td>
      
      <td>
        75.266 sec
      </td>
      
      <td>
        08.482 sec
      </td>
    </tr>
    
    <tr>
      <td>
        First request to <code>/nuget</code>(warm start, with metadata cache)
      </td>
      
      <td>
        n/a
      </td>
      
      <td>
        00.712 sec
      </td>
    </tr>
    
    <tr>
      <td>
        Second request to <code>/nuget</code>
      </td>
      
      <td>
        00.184 sec
      </td>
      
      <td>
        00.158 sec
      </td>
    </tr>
    
    <tr>
      <td>
        Memory usage on server (managed)
      </td>
      
      <td>
        146 MB
      </td>
      
      <td>
        210 MB
      </td>
    </tr>
  </tbody>
</table>

Your results of course will vary, based on the number of package IDs, versions per package, the size of your packages, and their dependencies.

## Getting started

It all starts with [creating a remote NuGet feed with NuGet.Server][4]. In short, all we have to do is create an empty web application and install a NuGet package.

> **Important:** The accelerated [NuGet.Server package][7] is still in preview. Make sure to tick the "Include prereleases" box in Visual Studio's NuGet dialog or use `Install-Package NuGet.Server -Pre` to install / `Update-Package NuGet.Server -Pre` to update an existing NuGet.Server.

### Upgrading

To upgrade an existing NuGet.Server we can update the NuGet.Server package in our project.

After the upgrade, the first start of the application will convert the flat packages folder structure into the expanded folder structure. If there are many packages on the server, this first launch may take a while (we've seen 6 minutes for 6,000 packages for this first launch). To overcome this performance hit, stop the server, [download the NuGet.exe command line 3.3+][8] and run the following commands:

    REM Navigate to NuGet.Server web root 
    cd c:pathtonuget.server 
    
    REM Copy the existing packages folder to a backup folder 
    xcopy packages packages-backup /E /I /Q /H /Y 
    
    REM Clean the original packages folder 
    del /f /s /q packages 
    
    REM Let NuGet.exe initialize the expanded folder structure 
    nuget init packages-backup packages
    

Once finished we can launch NuGet.Server using the new package folder structure. Note the `packages-backup` folder can be removed after succesfully launching the NuGet.Server.

### Uploading packages

There are two ways of uploading packages to NuGet.Server:

*   Using the NuGet.exe command line
*   Copying the NuGet package to the packages folder

To upload packages using the NuGet.exe command line, make sure NuGet.Server has an API key set. For example add the following in Web.config:

    <add key="apiKey" value="SooperS3cr3t"/>
    

Once added, we can use NuGet.exe to upload packages to our NuGet server:

    nuget push MyPackage.nupkg SooperS3cr3t -Source http://url.to.server
    

Another way of uploading packages is by copying the NuGet package to the root of the packages folder. The root acts as a "drop folder": NuGet.Server will automatically pick up the package and store it in the expanded folder format.

### Consuming packages

To consume packages from our NuGet.Server, we can [use the Visual Studio NuGet extension UI][9] or create/edit a NuGet.config file. When using source control among a team or multiple machines, adding a NuGet.config file registers our NuGet server with everyone who checks out our source code, including automated builds.

Create a NuGet.config file with this content, and check it in at the root of the source tree:

<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt; 
 &lt;configuration&gt; 
   &lt;packageSources&gt; 
     &lt;add key="CustomNuGetServer" value="http://url.to.server/nuget" /&gt; 
   &lt;/packageSources&gt; 
 &lt;/configuration&gt;</pre>

[Read more about setting up a nuget.config file.][10]

## Give it a try!

We've released this improved [NuGet.Server package][7] as a prerelease. It comes with performance improvements and support for new .NET target frameworks. Please give it a try and let us know how it performs in your setup!

 [1]: https://www.nuget.org
 [2]: http://docs.nuget.org/Contribute/Ecosystem
 [3]: https://www.nuget.org/packages/NuGet.Server/
 [4]: http://docs.nuget.org/create/hosting-your-own-nuget-feeds#creating-remote-feeds
 [5]: https://www.nuget.org/packages/NuGet.Server/2.8.6
 [6]: http://blog.nuget.org/20150922/Accelerate-Package-Source.html
 [7]: https://www.nuget.org/packages/NuGet.Server/2.10.0-master-0002
 [8]: https://dist.nuget.org/index.html
 [9]: http://docs.nuget.org/consume/package-manager-dialog#package-sources
 [10]: http://docs.nuget.org/consume/nuget-config-file