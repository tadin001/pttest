---
ID: 225969
post_title: >
  Invoke the Manage NuGet Packages dialog
  programmatically
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/invoke-the-manage-nuget-packages-dialog-programmatically/
published: true
post_date: 2013-07-25 00:00:00
---
Recently I've been asked several times for how to invoke the Manage NuGet Packages dialog from VS components programmatically, and optionally auto-invoke the search when the dialog opens. Today I'd like to share the solution in this post.

It's actually very straightforward. The **Manage NuGet Packages** menu command is registered as a standard VS command, thus you can call `DTE.ExecuteCommand()` to invoke it. The trick is to know the command name, which is **Project.ManageNuGetPackages**. Here's a code sample:

    DTE dte = (DTE)GetService(typeof(SDTE)); 
    dte.ExecuteCommand("Project.ManageNuGetPackages");
    

Note that it will open the *project-level* dialog. To open the *solution-level* dialog instead, you replace the command name with **Tools.ManageNuGetPackagesforSolution**.

The `ExecuteCommand()` method also accepts a second optional parameter of string type. If you pass a string value to it, the NuGet command handler will intepret it as a search query and will automatically issue a search request after it has opened the dialog.

    DTE dte = (DTE)GetService(typeof(SDTE)); 
    dte.ExecuteCommand("Project.ManageNuGetPackages", "jQuery");
    

With the above code, assuming you have the Online tab selected the last time you closed it, you will see this:

![Dialog opened with search query ][1]

If, for some reason, you don't like to call through DTE, you can also invoke the command directly through the `OleMenuCommandService` service.

    var nugetCommandGuid = Guid.Parse("25fd982b-8cae-4cbd-a440-e03ffccde106"); 
    var nugetCommand = new CommandID(nugetCommandGuid, 0x100); 
    
    // 0x100 is the command id for project-level dialog 
    OleMenuCommandService mcs = GetService(typeof(IMenuCommandService)) as OleMenuCommandService; 
    mcs.GlobalInvoke(nugetCommand, "jQuery");
    

Again, to open the solution-level dialog, you set the command id to 0x200. These constants are set in the [NuGet source code][2].

 [1]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05//open-dialog-with-search.png
 [2]: https://nuget.codeplex.com/SourceControl/latest#src/VsExtension/PkgCmdID.cs "PkgCmdID.cs"