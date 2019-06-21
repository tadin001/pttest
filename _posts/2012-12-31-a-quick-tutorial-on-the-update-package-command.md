---
ID: 225937
post_title: >
  A quick tutorial on the Update-Package
  command
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/a-quick-tutorial-on-the-update-package-command/
published: true
post_date: 2012-12-31 00:00:00
---
Among the supported commands in NuGet's Package Manager Console, **Update-Package** is arguably the most powerful in terms of its supported parameters and switches. The primary purpose of the command is to update package(s) in your projects to a higher (possibly latest) version. However, the provided parameters allow you to twist this behavior in many different ways. In this post, I'm going to give a quick tutorial on what possible ways you can invoke the command.

In total, the **Update-Package** command offers eight parameters (in addition to the built-in PowerShell parameters):

<table cellspacing="0" cellpadding="6" border="1">
  <tr>
    <th>
      Parameter
    </th>
    
    <th>
      Type
    </th>
    
    <th>
      Description
    </th>
  </tr>
  
  <tr>
    <td>
      -Id
    </td>
    
    <td>
      string
    </td>
    
    <td>
      Specifies the Id of the package to be updated.
    </td>
  </tr>
  
  <tr>
    <td>
      -ProjectName
    </td>
    
    <td>
      string
    </td>
    
    <td>
      Specifies the name of the project in which packages should be updated.
    </td>
  </tr>
  
  <tr>
    <td>
      -Version
    </td>
    
    <td>
      SemanticVersion*
    </td>
    
    <td>
      Specifies the new target version of the package as a result of the update.
    </td>
  </tr>
  
  <tr>
    <td>
      -Source
    </td>
    
    <td>
      string
    </td>
    
    <td>
      Specifies where to look for package updates, overriding the package sources that are specified in the Options dialog. This value can be either a url to a remote repository or a path to a local repository or the name of a package source specified in the Options dialog.
    </td>
  </tr>
  
  <tr>
    <td>
      -IgnoreDependencies
    </td>
    
    <td>
      Switch parameter
    </td>
    
    <td>
      If set, NuGet will ignore dependency packages and only update the main package.
    </td>
  </tr>
  
  <tr>
    <td>
      -Safe
    </td>
    
    <td>
      Switch parameter
    </td>
    
    <td>
      If set, NuGet will only update to a new version that has the same major and minor versions as the previous package. For example, if the old version is 1.2.0, NuGet will accept the update package with version of 1.2.1 or 1.2.9999 but it will <em>not</em> accept 1.3.0.
    </td>
  </tr>
  
  <tr>
    <td>
      -IncludePrerelease
    </td>
    
    <td>
      Switch parameter
    </td>
    
    <td>
      If set, NuGet will consider prerelease packages as candidates for updates.
    </td>
  </tr>
  
  <tr>
    <td>
      -Reinstall
    </td>
    
    <td>
      Switch parameter
    </td>
    
    <td>
      If set, instead of updating the package to the latest version, NuGet will uninstall the package and reinstall the same version. This is useful when, for example, you've updated the target framework of your project, e.g. from .NET 4.0 to .NET 4.5, and you want to reference .NET 4.5-specific assemblies in the package. You can't set this parameter together with the -Version parameter.
    </td>
  </tr>
</table>

`*`Although the type of the -Version parameter is **SemanticVersion**, which is a type declared by NuGet, you can just use string when you set it. PowerShell will automatically convert it for you, as long as the value can be parsed by the **SemanticVersion** class.

With that, here are the common use cases for the Update-Package command:

*   Update a particular package in a project to the latest version:  
    `<strong>Update-Package</strong> jQuery <strong>-ProjectName</strong> MyProject`

*   Update a particular package in a project to the latest version, using safe update rule:  
    `<strong>Update-Package</strong> jQuery <strong>-ProjectName</strong> MyProject <strong>-Safe</strong>`

*   Update a particular package in a project to a particular version:  
    `<strong>Update-Package</strong> jQuery <strong>-ProjectName</strong> MyProject <strong>-Version</strong> 1.8`

*   Update a particular package in *all* projects of the current solution to the latest version:  
    `<strong>Update-Package</strong> jQuery`

*   Update a particular package in *all* projects of the current solution to a particular version:  
    `<strong>Update-Package</strong> jQuery <strong>-version</strong> 1.8`

*   Reinstall a particular package in *all* projects of the current solution:  
    `<strong>Update-Package</strong> jQuery <strong>-reinstall</strong>`

*   Update *all* packages in a project to the latest versions:  
    `<strong>Update-Package</strong> <strong>-ProjectName</strong> MyProject`

*   Reinstall *all* packages in a project:  
    `<strong>Update-Package</strong> <strong>-ProjectName</strong> MyProject <strong>-reinstall</strong>`

*   Update *all* packages in *all* projects of the current solution to the latest versions:  
    `<strong>Update-Package</strong>`

*   Reinstall *all* packages in *all* projects of the current solution:  
    `<strong>Update-Package</strong> <strong>-Reinstall</strong>`

Phew, that's a lot of different combinations; but they are quite intuitive and easy to remember. To keep the list small, I left out the -IgnoreDependencies and -IncludePrelease parameters but you can add either (or both) of them to any of the example above (except that you can't use -Version and -Reinstall together, remember?) to adjust the behavior of the command.

Hope that helps. Happy New Year.