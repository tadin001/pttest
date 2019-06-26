---
ID: 226241
post_title: >
  Improved package debugging experience
  with the NuGet.org symbol server
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/improved-package-debugging-experience-with-the-nuget-org-symbol-server-2/
published: true
post_date: 2018-11-16 00:00:00
---
Starting today, you can publish symbol packages to the NuGet.org symbol server. With NuGet.org as a single service provider for libraries and symbols, package authors and consumers will have a streamlined publishing and consumption experience. With a single place for managing authentication and identity, you can be sure that both the package and its symbols are coming from the same author when you consume a NuGet package from NuGet.org.

## Publish a snupkg to the NuGet.org symbol server today!

We have introduced a new symbol package format - `.snupkg`. You will need [Visual Studio 2017 15.9][1] to generate [portable PDBs][2] that can be packaged as `.snupkg` for publishing to the NuGet.org symbol server.

### Create and publish snupkg to NuGet.org

1.  Compile your code using [Visual Studio 2017 15.9][1] to generate the portable PDBs.

2.  Get [nuget.exe 4.9.0][3] / [dotnet.exe 2.1.500][4] or higher (Visual Studio 2017 15.9 ships with dotnet.exe 2.1.500 and msbuild).

3.  Create the .snupkg using pack with your tool of choice. We have added a new property `SymbolPackageFormat`, which must be set to `snupkg`
    
    **using dotnet**
    
    `> dotnet pack MyAwesomeLib.csproj --include-symbols -p:SymbolPackageFormat=snupkg`
    
    **using nuget**
    
    `> nuget pack MyAwesomeLib.nuspec -Symbols -SymbolPackageFormat snupkg`
    
    **using msbuild**
    
    `> msbuild /t:pack MyAwesomeLib.csproj /p:IncludeSymbols=true /p:SymbolPackageFormat=snupkg`

4.  Push the .nupkg to NuGet.org (we will detect the .snupkg and push it for you)
    
    **using dotnet**
    
    `> dotnet nuget push MyAwesomeLib.1.0.0.nupkg -s https://api.nuget.org/v3/index.json -k ~~your API key here~~`
    
    **using nuget**
    
    `> nuget push MyAwesomeLib.1.0.1.nupkg -Source https://api.nuget.org/v3/index.json -apikey ~~your API key here~~`
    
    **using nuget.org upload**
    
    ![nuget.org upload][5]

5.  Once validated and indexed, the `.snupkg` will be available for consumption and will also be downloadable from the NuGet.org gallery.
    
    ![snupkg published][6]

> NuGet.org symbol server only accepts the new symbol package format `.snupkg`. Take a look at [NuGet.org symbol package constraints][7] for more info.

### Consume snupkg from NuGet.org in Visual Studio

You will need [Visual Studio 2017 15.9][1] to consume `.snupkg` from NuGet.org symbol server.

1.  In Visual Studio, open **Tools** > **Options** > **Debugging** > **Symbols** (or **Debug** > **Options** > **Symbols**).

2.  Under **Symbol file (.pdb) locations**, add a new symbol server location by selecting the **+** symbol in the toolbar. Use the following URL `https://symbols.nuget.org/download/symbols`.
    
    ![VS debug options][8]

## FAQs

*   **Q. Can I submit a snupkg for an existing package on NuGet.org?**  
    To upload any .snupkg to NuGet.org, the hash of the .pdb must be embedded in the .dll that is included in the matching .nupkg. If the existing .nupkg on nuget.org does not include the .pdb hash, you will need to recompile and package your .nupkg with a compiler that supports this feature (such as the one that shipped with Visual Studio 2017 15.9), and push a new version before you can submit the corresponding .snupkg.

*   **Q. Which PDB formats are supported?**  
    Currently, NuGet.org symbol server supports only the portable PDB format. The portable PDB format can be created and consumed for any .NET application provided that the consuming client is on .NET 4.7.1 or later. More info about Portable PDBs is available on the [.NET GitHub][2].

*   **Q. Where are the docs?**  
    Here are the docs for [creating symbol packages (.snupkg)][9] and the corresponding [API docs][10].

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. Use the [GitHub issue tracking this experience][11] to provide feedback, or ask questions. You can also reach out me on twitter - mention [@karann9][12] and [@nuget][13] in your tweets.

 [1]: https://docs.microsoft.com/en-us/visualstudio/releasenotes/vs2017-relnotes
 [2]: https://github.com/dotnet/core/blob/master/Documentation/diagnostics/portable_pdb.md
 [3]: https://www.nuget.org/downloads
 [4]: https://www.microsoft.com/net/download/dotnet-core/2.2
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/nuget_upload.png
 [6]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/snupkg_download.png
 [7]: https://docs.microsoft.com/en-us/nuget/create-packages/symbol-packages-snupkg#nugetorg-symbol-package-constraints
 [8]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/vs-nuget-symbol-server.png
 [9]: https://docs.microsoft.com/en-us/nuget/create-packages/symbol-packages-snupkg
 [10]: https://docs.microsoft.com/en-us/nuget/api/symbol-package-publish-resource
 [11]: https://github.com/NuGet/Home/issues/6104
 [12]: https://twitter.com/karann9
 [13]: https://twitter.com/nuget