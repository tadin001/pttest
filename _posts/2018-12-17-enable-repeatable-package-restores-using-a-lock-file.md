---
ID: 141
post_title: >
  Enable repeatable package restores using
  a lock file
author: Anand Gaurav
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/enable-repeatable-package-restores-using-a-lock-file/
published: true
post_date: 2018-12-17 00:00:00
---
With `PackageReference`, NuGet always tries to produce the same closure of package dependencies if the input package reference list has not changed. However, there are a few scenarios where it may not be able to do so. While these cases are limited, we received multiple requests to completely lock down the full package dependency graph for projects to produce repeatable restores (builds). We have been working on your feedback, iterating quite a few times, on the approach and design over the past few months. We are happy to announce that we have now introduced the ability to lock your project's package dependency graph via a lock file - `packages.lock.json`. With this feature, NuGet uses the lock file to restore packages for **`PackageReference`** based projects. This file should be checked into your source code repository. If present during `restore`, and if the input package references does not change for a given project, NuGet now uses the lock file to determine the dependencies instead of re-evaluating the package graph. Depending on your scenario, you can configure NuGet to either overwrite the lock file (default) or to fail the restore operation whenever the package graph changes.

## Which versions of NuGet support `restore` with lock file? The lock file functionality is available with the following NuGet distributions:

*   [NuGet.exe][1] version **4\.9** and above.
*   [Visual Studio 2017][1] version **15\.9** and above.
*   [.NET SDK][1] version **2\.1.500** and above.

## Why use a lock file? There are a few cases where NuGet may not be able to get the same package graph with every

`restore` across machines. Most of these cases happen when consumers or repositories do not follow NuGet best practices. For example: 1. **`nuget.config` mismatch**: This may lead to an inconsistent set of package repositories (or sources) across `restore`s. Based on the packages' version availability on these repositories, NuGet may end up resolving to different versions of the packages upon `restore`. 2. **Intermediate versions**: A missing version of the package, matching PackageReference version requirements, is published: * Day 1: If you specified `<PackageReference Include="My.Sample.Lib" Version="4.0.0"/>` but the versions available on the NuGet repositories were `4.1.0`, `4.2.0` and `4.3.0`, NuGet resolves to **`4.1.0`** as it the nearest minimum version. * Day 2: Version `4.0.0` gets published. NuGet now restores version **4\.0.0** as it is an exact match. 3. **Package deletion**: Though nuget.org does not allow package deletions, not all package repositories have this constraint. Deletion of a package version results in NuGet finding the best match when it cannot resolve to the deleted version. 4. **Floating versions**: When you use floating versions like `<PackageReference Include="My.Sample.Lib" Version="4.*"/>`, you might get different versions after new versions are available. While the intention here is to float to the latest version on every `restore` of packages, there are scenarios where users require the graph to be locked to a certain latest version and float to a later version, if available, only upon an explicit gesture. 5. **Package content mismatch**: If the same package (id and version) is present with different content across repositories, then NuGet cannot ensure the same package (with the same content hash) gets resolved every time. It also does not warn/error out in such cases. Using the lock file will help you in resolving to the same versions always. External factors would not affect your package graph. In addition, if the lock file changes anytime, you have the ability to find what exactly changed by doing a diff with the previous version. This is particularly helpful when a transitive package version, buried deep into the full package graph, changes because you brought in a new direct dependency.

## How to enable the lock file? To enable the use of lock file with NuGet, set the MSBuild property

`RestorePackagesWithLockFile` in your project file:

<pre class="lang:default decode:true">&lt;PropertyGroup&gt;
    &lt;RestorePackagesWithLockFile&gt;true&lt;/RestorePackagesWithLockFile&gt;
&lt;/PropertyGroup&gt;</pre>

If this property is set, NuGet

`restore` will generate a lock file - `packages.lock.json` file at the **project root directory**. ![image][2] **Note**: * You must **commit/check-in** this file in the source code repository so that it is always available for `restore`. * The lock file is a tool (NuGet) generated file and should **never be manually edited**. * Lock file should not be put inside a package. It does not have any significance within a package and will never be used by NuGet.

## How does NuGet use the lock file? NuGet does a quick check to see if there were any changes in the package dependencies as mentioned in the project file (or dependent projects' files) and if there were no changes, it just restores the packages mentioned in the lock file without re-evaluating the full package graph. If NuGet detects a change in the defined dependencies as enumerated in the project file(s), it re-evaluates the package graph and updates the lock file to reflect the new package graph applicable for the project. This is the default mode and is useful when you are in development environment actively modifying your package and project dependencies. You can however, at any point, diff against the previous version of the lock file, if modified, to know the exact dependency changes happening to the package graph. For

**Continuous Integration (CI/CD) and other build scenarios**, where you would **always** want the same repeatable `restore`, you should use the **locked mode**. You can get into the locked mode by setting the MSBuild property `RestoreLockedMode` to `true`:

<pre class="highlight"><code>&lt;span class="nt">&lt;PropertyGroup&gt;&lt;/span>
       &lt;span class="nt">&lt;RestoreLockedMode&lt;/span> &lt;span class="na">Condition=&lt;/span>&lt;span class="s">"'$(ContinuousIntegrationBuild)' == 'true'"&lt;/span>&lt;span class="nt">&gt;&lt;/span>true&lt;span class="nt">&lt;/RestoreLockedMode&gt;&lt;/span>
&lt;span class="nt">&lt;/PropertyGroup&gt;&lt;/span> </code></pre>

**Note:** The above example assumes you have a MSBuild property `ContinuousIntegrationBuild` that is set to `true` for CI/CD builds. Alternatively, for CI/CD builds, you can use the following command line options to enable locked mode: For dotnet.exe, run:

> dotnet.exe restore --locked-mode For msbuild.exe, run:

    > msbuild.exe /t:restore /p:RestoreLockedMode=true If 
    

**locked mode** is set, `restore` will either get the exact packages as listed in the lock file or fail if it cannot. For example, if you updated the defined package dependencies for the project after lock file was created or due to any of the [reasons mentioned in this post][3].

### SHA validation In additional to locking the package dependencies graph, NuGet also persists the content hash of all the packages in the lock file. During

`restore`, it verifies that the content hashes match between the lock file and actual restored packages. In case of discrepancy, NuGet `restore` throws an error. This prevents any inadvertent use of packages with same ID and version but with different content ([package content mismatch][3]).

## What next?

### `packages.config` Currently, the lock file feature is only limited to

[`PackageReference`][4] based projects. However, the full package closure is already listed in the `packages.config` file and the only piece missing is content hash of the packages restored. We understand this is an important validation and hence we are working on a similar feature for `packages.config`. The information in the lock file will have a lot of repetitive contents as in the `packages.config` file but we plan to keep a consistence lock file experience, as with `PackageReference`. You can track the progress of this feature, [here][5].

### Solution or repo lock file We believe this feature goes a long way towards addressing the repeatable

`restore` concern that many of you had. However, we are aware that managing multiple project lock files for a huge solution or a repo can be daunting. We have started to think about providing better and easier way to manage package dependencies centrally i.e. at a solution or even a repo level - and thereby providing a central lock file. You can find our early thoughts in the spec: [Centrally managing NuGet package versions][6]. We would love to get your early feedback so that we can steer it towards the best experience possible.

## We want to hear your feedback! Please provide any feedback on this feature by commenting on

[this GitHub issue][7]. If you have any other question, comment or feedback you can reach out to me at <anangaur@microsoft.com> or tag [@nuget][8] in your tweets. You can always create a new issue on our [Github repository][9] to start a new discussion or report a bug. To keep yourself updated on all planned NuGet features/experiences, do subscribe to [NuGet/Announcements][10] repo. We actively seek participation for all new features and we announce these features on [this repo][10] as they get ready for the community review.

 [1]: https://blog.nuget.org/20181217/Enable-repeatable-package-restores-using-a-lock-file.html
 [2]: https://user-images.githubusercontent.com/14800916/49912003-6d98f100-fe3d-11e8-8be1-372d36d46c98.png
 [3]: #why-use-a-lock-file
 [4]: https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files
 [5]: https://github.com/NuGet/Home/issues/7281
 [6]: https://github.com/NuGet/Home/wiki/Centrally-managing-NuGet-package-versions
 [7]: https://github.com/NuGet/Home/issues/5602
 [8]: https://twitter.com/nuget
 [9]: https://github.com/NuGet/Home/issues/new
 [10]: https://github.com/NuGet/Announcements