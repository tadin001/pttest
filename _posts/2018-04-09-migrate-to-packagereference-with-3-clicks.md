---
ID: 1016
post_title: >
  Migrate to PackageReference with 3
  clicks
author: Serhii Rohatin
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/migrate-to-packagereference-with-3-clicks/
published: true
post_date: 2018-04-09 00:00:00
---
Last year, we introduced the option to [make PackageReference the default package management format][1] for managing NuGet dependencies when installing the first NuGet package for a newly created projects. With Visual Studio Version 15.7 Preview 3, we have introduced the capability to migrate existing projects that use the `packages.config` format to use PackageReference instead.

Benefits of using PackageReference include:

*   Ability to manage all project dependencies from one place (the project file).
*   An uncluttered view of top-level dependencies: the project file shows only those NuGet packages you directly installed in the project.
*   Faster package install/update times.
*   Better cache management with a global-packages folder instead of a solution-local packages folder.
*   Fine control over dependencies and content flow such as conditionally referencing a NuGet package per target framework, configuration, platform, or other pivots.
*   New features as we continue to [invest in PackageReference][2].

## Migrate your projects to PackageReference today!

To try out the new migration experience, [download Visual Studio 2017 Preview][3], open a `packages.config` based project, right-click the **References** node in **Solution Explorer**, and select the **Migrate packages.config to PackageReference....** command.

*Note that the migrator does not presently support C++, JavaScript, and ASP.NET (.NET Framework) projects.*

![tryprmigrator][4]

The migrator analyzes the project's existing references, calculates the dependency graph, and categorizes them into top-level and transitive dependencies.

![migratordialog][5]

By using the **Top-level** option, you can change any package that's categorized as a transitive dependency to be treated instead as a top-level dependency.

The migrator also displays warnings for any potential [package incompatibilities][6].

### Backup and roll back

Before the process begins, the migrator creates a backup of the project file and `packages.config` to `<solution_root>MigrationBackup<unique_guid><project_name>`. The backup allows you to [roll back to `packages.config`][7] if necessary.

### Set PackageReference as the default

In the NuGet options in Visual Studio (opened using the **Tools > NuGet Package Manager > Package Manager Settings** menu command), change the **Default package management format**" to PackageReference. When you then install a NuGet package into a project for the first time, NuGet uses the PackageReference format. Newly created projects that come with existing NuGet references using `packages.config` (such as WPF) must be migrated to PackageReference after project creation.

![trypackageref][8]

### Limitations

Although we're working to bring the PackageReference goodness to all project types and to make all packages compatible with PackageReference, migration is not presently supported for C++, JavaScript, and ASP.NET (.NET Framework) projects.

Also, some packages are [not fully compatible with PackageReference][6].

### Enhancements in the pipeline

We are actively working to [enhance PackageReference][2] with features such as locking the transitive dependencies, ability to consolidate package versions across projects in a solution, and more.

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. For feedback specific to the migration experience, use [NuGet/Home#5877][9]. For anything else, hit us up at [feedback@NuGet.org][10], and as always, if you run into any issues or have an idea, [open an issue on GitHub][11].

 [1]: https://blog.nuget.org/20170316/NuGet-now-fully-integrated-into-MSBuild.html#what-about-other-project-types-that-are-not-net-core
 [2]: https://github.com/NuGet/Home/issues/6763
 [3]: https://www.visualstudio.com/vs/preview/
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/2018.04.04.15.7Prev3.nuget_migrator.png
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/2018.04.04.15.7Prev3.nuget_migrator_dialog.png
 [6]: https://docs.microsoft.com/en-us/nuget/reference/migrate-packages-config-to-package-reference#package-compatibility-issues
 [7]: https://docs.microsoft.com/en-us/nuget/reference/migrate-packages-config-to-package-reference#how-to-roll-back-to-packagesconfig
 [8]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/trypackageref.gif
 [9]: https://github.com/NuGet/Home/issues/5877
 [10]: mailto:feedback@nuget.org
 [11]: https://github.com/Nuget/Home/issues