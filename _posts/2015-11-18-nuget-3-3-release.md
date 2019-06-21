---
ID: 226070
post_title: NuGet 3.3 Release
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-3-3-release/
published: true
post_date: 2015-11-18 00:00:00
---
Big news: a new version of the NuGet clients has been released! This is version 3.3 of the client, with a [new command-line][1] and [Visual Studio 2015 extension][2] available for use. Use the above links to download from the NuGet distribution site, also accessible with the 'Downloads' menu item above. This is a release that the NuGet team worked closely with community influencers to ensure it closed some of the usability gaps and made packages a bit easier to build. Complete details of this version are available in the [v3.3 release notes][3], but lets take a look at the highlights of this release:

## User Interface Updates

Since the July 2015 release of NuGet with Visual Studio 2015, the top comments and requests heard by the NuGet team through the various feedback channels has been to add / modify features of the user interface. With this release, we are deploying a number of changes that were recommended when reviewing with a number of influencers and Microsoft MVPs.

### 'Update All' and 'Update Some'

The top requested feature was the ability to update multiple packages from the user interface. We heard several different stories from developers who wanted to be able to update packages, especially using the 'Update All' button that was in the Visual Studio 2013 interface. We saw this as an opportunity to provide information early in the package management process and give a little more control.

<img style="width: 100%" title="Update Panel Features" src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/update.png" />

First, you'll notice that the filter have been broken out as tabs. The Updates tab now has a convenient badge on it to indicate the number of packages that have updates awaiting. When you click the 'Updates' tab, the list of packages is on the right with some additional information about the packages including Author, currently installed version indicated with a check mark, and the latest available version beneath that.

Each row has a checkbox, allowing you to choose which packages you want to update to the latest available version OR you can click the 'Select all packages' box at the top to select all packages. The 'Update' button to the right will apply updates to all packages that are checked in this view. If you still want to update a package with more control, you can use the right panel to select the exact version you would like to update to and click the Update button next to the package version.

The Uninstall button for each package is always present on the right side of the panel so that you can uninstall specific packages. We separated the Uninstall and Update buttons so that it was clear that you never need to uninstall in order to update a package.

We also cleaned up the language of 'Update' from 'Upgrade'. We heard from developers that the 'Upgrade' wording made it sound like a fee would be charged by NuGet to install a new version. This is NEVER the case, so we changed the caption to 'Update'

### Action buttons on package list

We wanted to make the installation process for packages a bit easier than the previous drop-down action selector button process. It was confusing, and we wanted something simple.

![Action Buttons on Package List][4]

We are introducing action buttons on the package list. When your mouse hovers over a package, you will see one or two action buttons for the package appear to the right of the version numbers. The top version number with the check is the currently installed version, and the red X next to it will immediately uninstall that package. The blue arrow next to the lower 'Latest Available' version number will update that package to the latest available version number displayed. In the case where only one version number is present, this is the latest available version and you have not yet installed a version of that package. A different arrow button will appear next to this number that you can click to immediately install that version number of the package in your project.

We introduced a state badge overlay on the package icons on the right side to help give a visual indication if the package was current or if there was an update available.

Finally, you still have full control over which version of a package is installed, with the package version combobox on the right-side as we saw in the previous screenshot.

### Options, optionally visible

We know that the 'Options' section on the right side of the screen felt out of place to some people and was distracting that it would change content when you changed the action you wanted to perform. Without an action combobox, we decided to list all options in this area.

![Options Settings][5]

This is a large section and it only really needs attention in a minimum number of use cases. For those that don't really need to look at and work with these options, you can click the arrow next to the Options label to hide the section.

### Consolidate

The final new feature that we want to review is the Solution 'Package Consolidate' view. When you choose to manage packages for the solution, an additional panel appears labeled 'Consolidate' with a badge to indicate how many packages in your solution are used in multiple projects with multiple distinct version numbers.

<img style="width: 100%" title="Solution View" src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/consolidate.png" />

We know that many developers consider multiple package versions across a solution to be a bad practice, and are introducing this indicator and panel to help manage this scenario. In the screenshot you can see that there are two packages used in multiple projects with different version numbers. In the case of EntityFramework, you can see the two version numbers and two projects on the right side. The table of projects is sortable by Project or Version number, and you can choose which projects you would like to act on with the checkboxes to the left of the project name. After choosing one or more projects, you can select the version you want to consolidate your solution to. Clicking the Install button will install or update the selected projects with the chosen package version. You could also click the Uninstall button to remove the package from the selected projects.

### Incompatible - Try Bower!

In cooperation with the web tools teams at Microsoft, we are introducing a feature to help developers of ASP.NET 5 projects find appropriate Bower packages that deliver the same content as popular web-focused NuGet packages. If you are building an ASP.NET 5 project and search for a package like jQuery or Bootstrap that deliver CSS and JavaScript content for a web project, you will receive a recommendation in the package list to install the package with Bower instead of NuGet.

![Install with Bower][6]

When you click the 'Bower' link in the NuGet package list, you will be routed to the new Bower Package Manager that comes with Visual Studio 2015 Update 1. That interface will be pre-populated with the appropriate matching package from Bower that we recommend you use instead of the NuGet package.

## Content Files

The next feature to discuss is a change to better support content files in packages. Historically, you could include content in a `content` folder and the NuGet client would copy that folders data into your project. This had several side-effects that were difficult to manage:

*   What should the NuGet client do if the delivered content is modified on disk and the package is updated? Should the content on disk in a project be overwritten by the new content delivered in the package update? NuGet cannot reliably migrate modified content based on new content delivered in a package
*   How should the project be configured to act on the files delivered from a package? Are the files supposed to be compiled, embedded, or just left on disk to be referenced?

To better handle these scenarios and offer a significantly better experience for package authors and package consumers, we are introducing the `contentFiles` folder structure and features. In particular, this feature initially targets three key scenarios:

*   Delivering content that should be referenced and included in the project without modification for reference and reuse elsewhere in the project.
*   Providing a template to generate some code that should be referenced in a project using the .PP file extension and replacing tokens in those files based on MSBuild property values.
*   How should this content be managed when packages are included indirectly?

The ContentFiles feature is only available to projects that are managed with a project.json file. The dnu utility for ASP.NET 5 does not yet support this feature.

### ContentFiles folder layout

The new "contentFiles" folder is at the root of the package and has a strictly defined layout of folders within. The first layer of folders defines the project language to which the content will be applied. Valid names of folders here are `vb`, `cs`, and `any` The NuGet client will inspect the project into which the package is being installed and grab the content from the most specific folder (vb or cs) based on the language used in the project. If a specific folder is not located in the project, the `any` folder will be used if it is present. If the NuGet client cannot locate an appropriate folder no content will be copied into the project.

The second folder in the hierarchy is a target framework moniker (TxM) that defines the project framework into which the content will be copied. All valid NuGet target frameworks are eligible to be referenced as the names of a folder at this level, in addition to the keyword `any`. A similar folder resolution strategy as the language folder is used here - the most specific folder name will be chosen by NuGet first, and the last folder to be chosen to copy content from is the `any` folder.

Content will only be copied from one folder location.

There is an override folder name of `_._` that can be used at the third level of folders to prevent delivering content to that combination of language and project framework type.

Any folders at the third level and lower will be referenced by the project the package is being installed into. Valid content folders in a package include:

    /contentFiles/any/any/config.xml 
     /contentFiles/any/net45/config.xml 
     /contentFiles/cs/net45/config.xml 
     /contentFiles/vb/any/_._
    

### Project Content Configuration

In previous version of NuGet, the client would inspect the file extension and determine what project build action to configure. With this feature, package authors can specify how the NuGet client will configure the project for this content. Inside of the NuSpec document for a package, the author should include the new `contentFiles` element with child `files` items that define the project configuration for the content included in the package.

    <contentFiles> 
        <!-- Embed image resources --> 
        <files include="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/dnf.png" buildAction="EmbeddedResource" /> 
        <files include="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/ui.png" buildAction="EmbeddedResource" /> 
    
        <!-- Embed all image resources under contentFiles/cs/ using a wild card --> 
        <files include="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/*.png" buildAction="EmbeddedResource" /> 
    
       <!-- Copy config.xml to the root of the output folder --> 
       <files include="cs/uap10.0/config/config.xml" buildAction="None" copyToOutput="true" flatten="true" /> 
    
    
       <!-- Include everything in the scripts folder except exe files --> 
       <files include="cs/uap10.0/scripts/*" exclude="**/*.exe" buildAction="None" copyToOutput="true" /> 
    
       <!-- All other files in shared are compiled and use the default options --> 
    </contentFiles> 
    

Each files item has a set of attributes available to be configured:

<table>
  <tbody>
    <tr>
      <th>
        Attribute
      </th>
      
      <th>
        Description
      </th>
    </tr>
    
    <tr>
      <td>
        include
      </td>
      
      <td>
        <b>[Required attribute]</b> Include provides either a file path or a wild card path. All matching files from the contentFiles folder will have the attributes for that files node applied. Examples: **/*, **/*.cs, any/any/myfile.txt, **/net*/*
      </td>
    </tr>
    
    <tr>
      <td>
        exclude
      </td>
      
      <td>
        Exclude provides either a file path or a wild card path. All matching files will be excluded from the include.
      </td>
    </tr>
    
    <tr>
      <td>
        buildAction
      </td>
      
      <td>
        Build action taken by msbuild for the content items. Examples: None, Compile. <b>Default Value: Compile</b>
      </td>
    </tr>
    
    <tr>
      <td>
        copyToOutput
      </td>
      
      <td>
        If True the content items will be copied to the build output folder <b>Default Value: False</b>
      </td>
    </tr>
    
    <tr>
      <td>
        flatten
      </td>
      
      <td>
        If False the content items will be copied to the build output folder using the full folder structure from the nupkg. This path will be relative to the TxM folder. Example: cs/net45/config/data.xml -> config/data.xml <b>Default Value: False</b>
      </td>
    </tr>
  </tbody>
</table>

### Source Code Transformations

The content of *.pp files will be transformed and renamed to remove the .pp extension when a package is installed that contains this type of content. The transformation of these files is the same process as with prior versions of NuGet. [Documentation][7] about how these files are transformed and the options available are available online. All output of files that are transformed are added to a project with the build action of "Compile".

## Visual Studio Team Services and Credential Providers

In Fall 2015, [Visual Studio Team Services][8] introduced NuGet package hosting. The VSTS hosted solution provides:

*   Private package hosting for your team or entire organization,
*   Enterprise-grade security and authentication,
*   Seamless integration with other VSTS components such as Team Build, and
*   Easy integration with third-party dev tools like Jenkins.

More information about [package management support for VSTS][9] is available online. In this release, we are introducing support for credential providers that allow NuGet to work seamlessly with an authenticated feed like VSTS.

[Instructions on how to install the Visual Studio Team Services credential provider ][10] and configure the NuGet clients to use it are available on NuGet Docs.

## Folder Based Repository Commands

Over the summer, we published a [blog post][11] that demonstrated an improved method to organize packages in a shared folder for significant package installation performance gains. In that blog post's sample scenario, the install or restore time was reduced by 92% and the bandwidth use was reduced by 99.4%.

We would like to help encourage more developers to realize this type of performance improvement in working with their local packages. To help with that, we are introducing two commands to the `nuget.exe` command-line tool to help configure and maintain this prescribed folder structure:

Init will initialize the folder location based on the contents of another flat folder of packages. This preparation will enable the folder to be read and consumed by a NuGet 3.x client at a higher speed.

    nuget init c:myTestNugetRepository c:sharednuget_repo
    

This will initialize the folder at `c:sharednuget_repo` with the contents of `c:myTestNugetRepository`

Add will take a package and place it in an appropriate folder location for use in the new folder structure.

    nuget add my.nupkg -Source c:sharednuget_repo
    

This command will add the package called `my.nupkg` to the proper package folder location within the `c:sharednuget_repo` folder.

More information about this feature is available in the [NuGet docs][12].

## Local cache management

NuGet uses a local cache, several actually. If you have ever run into an issue with your network or a corrupted package that was downloaded incompletely from a package repository, you've run into this scenario where you need to clear out your NuGet caches in order for NuGet to fetch a new version of the package. We've made this operation easy to do with an additional `locals` command on the NuGet command-line tool. Syntax looks like the following:

    nuget locals <all | http-cache | packages-cache | global-packages> [-clear | -list]
    

You can list the locations of these caches on disk, or you can instruct NuGet to clear them out for you which will force a new copy of the packages to be downloaded from any remote repositories that you have configured.

More about the locals command can be found in the [NuGet Command Line Reference][13].

## Support for the .NET Platform Standard

The .NET Platform Standard announcement is covered in full detail on the [.NET Blog][14] NuGet provides support for this new definition of compatibility across .NET Framework versions and verticals in the target frameworks specified in nuspec documents.

More details about the various target frameworks and supported capabilities of the .NET Platform Standard are still evolving but can be [found on GitHub][15].

## Summary

These are the top level features that were brought into the NuGet package manager and clients with the 3.3 version. A complete list of updates can be found in the [v3.3 release notes][16]. The team is continuing to work on improving the user interface and the package management clients, looking forward to a 3.4 release early in Q1 2016. A list of proposed fixes and updates can be found in the [3\.4 milestone][17] on the NuGet GitHub repository. Keep an eye on the NuGet blog in the days and weeks ahead as we are planning more posts about using the .NET Platform Standard and how to better work with building and managing packages.

 [1]: https://dist.nuget.org/win-x86-commandline/v3.3.0/nuget.exe
 [2]: https://dist.nuget.org/visualstudio-2015-vsix/v3.3.0/NuGet.Tools.vsix
 [3]: http://docs.nuget.org/release-notes/nuget-3.3
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/packageList.png
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/options.png
 [6]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/bower.png
 [7]: http://docs.nuget.org/Create/Configuration-File-and-Source-Code-Transformations#specifying-source-code-transformations
 [8]: https://www.visualstudio.com/products/what-is-visual-studio-online-vs
 [9]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/08/27/announcing-package-management-for-vso-tfs.aspx
 [10]: http://docs.nuget.org/Consume/Credential-Providers
 [11]: http://blog.nuget.org/20150922/Accelerate-Package-Source.html
 [12]: http://docs.nuget.org/Consume/Command-Line-Reference#folder-repository-commands
 [13]: http://docs.nuget.org/Consume/Command-Line-Reference
 [14]: http://blogs.msdn.com
 [15]: https://github.com/dotnet/corefx/blob/master/Documentation/project-docs/standard-platform.md
 [16]: http://docs.nuget.org/release-notes/nuget-3.3.0
 [17]: https://github.com/NuGet/Home/milestones/3.4