---
ID: 226055
post_title: >
  Accelerate your network share hosted
  Package Source
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/accelerate-your-network-share-hosted-package-source/
published: true
post_date: 2015-09-22 00:00:00
---
This is a guest blog, by Andrew Arnott from the Visual Studio team. Andrew was having performance issues with NuGet updating or installing packages from a UNC share. These issues where well known, and our solution in the past was to point people to http servers. We discussed this issue with Andrew and suggested that the new supported folder structure introduced in NuGet 3 and in 2.8.7 should resolve the issue.

This blog is the result of Andrew's experiments with the structure in his project.

(The NuGet team)

## Accelerate your network share hosted Package Source

NuGet is fairly flexible in where you set up your package sources. Besides the default nuget.org web service, you can set up alternate package sources at other HTTP URLs such as myget.org or your own NuGet server and even local directories on your hard disk or network UNC shares like yourservernuget. When using a network share as your package store, there is a really fast and a really slow way to do it. In this post, we’ll review the two options and help you accelerate your network share based package store if you’re doing it the slow way.

Why would you want to set up a custom package source? There are a variety of reasons. Here are just a couple:

1.  You want to use NuGet packages to share code internally without exposing them publicly on nuget.org
2.  You have a continuous build server that produces nuget packages that you want to consume before releasing to nuget.org

### Custom package sources

First, let’s review how you set up a custom package source. You can do this using the Visual Studio NuGet extension UI, but if you’re using source control among a team or multiple machines, you’ll want to check in a nuget.config file that adds your custom package source so that everyone who checks out your source code, including any automated builds you have set up, will be able to restore your packages.

Create a nuget.config file with this content, and check it in at the root of your source tree:

<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt; &lt;configuration&gt; &lt;packageSources&gt; &lt;add key="YourServer" value="\yourserverNuGet" /&gt; &lt;/packageSources&gt; &lt;/configuration&gt;</pre>

[Learn more about setting up a nuget.config file.][1]

### Set up your package source: the simple way

Now that we have our source tree prepared to restore packages from your network share, we need to get that network share up and going. After creating the directory on the network server and setting up permissions, it’s time to fill it with packages.

The simplest way to set up a package store, whether on your local hard disk or on a network share, is to just drop all your .nupkg files into the same directory, as a flat list of files. For example, when using a network share a file listing may look like this:

    \yourservernugetPackageA.1.0.0.nupkg 
     \yourservernugetPackageA.1.0.1.nupkg 
     \yourservernugetPackageA.1.0.2.nupkg 
     \yourservernugetPackageB.0.0.1-beta.nupkg 
     \yourservernugetPackageB.0.0.1.nupkg 
     \yourservernugetPackageB.0.0.2-rc.nupkg
    

"Pushing" to this package source is as simple as copying a .nupkg you’ve built to that network directory.

That’s it. Now the NuGet client can install and restore packages in your projects from this location.

But it turns out that this doesn’t scale well. Package Restore tends to perform well in this configuration, but as the number of your packages grow, installing and updating packages in projects that use this package source can get very slow. The NuGet client has to download huge numbers of .nupkg files as it evaluates which package(s) need to be installed to find those that are version compatible with each other. This can make a simple package install or upgrade take several minutes (or longer), and download gigabytes of packages in the process.

### Set up your package source: the fast and scalable way

The most performant upgrade you can make to your network share to boost performance is setting up [a full scale NuGet package server][2] of your own. But suppose you can’t or don’t want to go to that trouble at this point, and just want to give your network share a big performance boost in just a few minutes. Here’s how you can do it.

It turns out that when the NuGet client downloads those gigabytes of .nupkg files during package install or update, it only needs to look at a few small files in the package. Setting up your network share so that these files can be accessed by themselves without downloading the whole package makes a world of difference. This requires that we stop directly copying the .nupkg package to the network share and instead use the nuget add command available in [nuget 3.3][3].

Before NuGet 3.3 was released you could have used these PowerShell [Publish-Package][4] cmdlet to push the packages to the network share:

<pre><code class="language-powershell">Publish-Package "c:pathtoyourpackage.0.0.1-beta.nupkg" -PackageSource "\yourservernuget"</code></pre>

This creates a different layout on your network share than a simple copy:

    \yourservernuget 
                    yourpackage 
                         0.0.1-beta 
                               yourpackage.0.0.1-beta.nupkg 
                               yourpackage.nuspec 
                               yourpackage.0.0.1-beta.nupkg.sha512
    

Notice the .nupkg archive itself is still there, but it is also has the .nuspec file pre-expanded and package hash readily accessible. Also notice that all the files are within a folder structure named after the package ID and version. As you push more packages, you'll end up with multiple version folders within your package ID folder.

Also note that the version portion of the path has to be normalized for nuget to find it, the nuget init and add commands will normalize it for you. For example yourpackage.1.0.0.0 will create a normalized version folder of 1.0.0 (and not 1.0.0.0)

#### Migrate an existing flat .nupkg file list to the new layout

This can be done with the nuget.exe init command in [NuGet 3.3][3]

Pre NuGet 3.3 you can use the following [Upgrade-PackageSource][4] PowerShell cmdlet:

<pre><code class="language-powershell">Upgrade-PackageSource \yourservernugetpackages \yourservernugetpackages3</code></pre>

#### How much faster is this new file layout?

In my tests on a network share before and after migration, here are the results:

table, th, td { border: 1px solid black; border-collapse: collapse; } th, td { padding: 5px; } 

<table>
  <thead>
    <tr>
      <th>
      </th>
      
      <th>
        Before
      </th>
      
      <th>
        After
      </th>
    </tr>
  </thead>
  
  <tbody>
    <tr>
      <td>
        # of files
      </td>
      
      <td>
        5,236
      </td>
      
      <td>
        15,708
      </td>
    </tr>
    
    <tr>
      <td>
        # of packages
      </td>
      
      <td>
        5,236
      </td>
      
      <td>
        5,236
      </td>
    </tr>
    
    <tr>
      <td>
        Space consumed on server
      </td>
      
      <td>
        1.425 GB
      </td>
      
      <td>
        1.432 GB
      </td>
    </tr>
    
    <tr>
      <td>
        Update-Package x -Version y: time to execute
      </td>
      
      <td>
        4:33
      </td>
      
      <td>
        0:23
      </td>
    </tr>
    
    <tr>
      <td>
        Update-Package x -Version y: MB downloaded
      </td>
      
      <td>
        963 MB
      </td>
      
      <td>
        5.6 MB
      </td>
    </tr>
  </tbody>
</table>

Your results of course will vary, based on the number of package IDs, versions per package, the size of your packages, and their inter-dependencies.

 [1]: http://docs.nuget.org/consume/nuget-config-file
 [2]: http://docs.nuget.org/create/hosting-your-own-nuget-feeds#creating-remote-feeds
 [3]: https://dist.nuget.com/index.html
 [4]: https://gist.github.com/AArnott/45d884642684046c81cb