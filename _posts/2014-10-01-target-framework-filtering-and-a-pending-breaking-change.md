---
ID: 226019
post_title: >
  Target Framework Filtering and a Pending
  Breaking Change
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/target-framework-filtering-and-a-pending-breaking-change/
published: true
post_date: 2014-10-01 00:00:00
---
Have you ever seen this error before?

![Could not install package because it does not contain any files that are compatible with your framework][1]

Doesn't it make you want to yell and scream at NuGet, "Why did you just show it to me if I couldn't install it!?‽"

Yeah, it makes us want to yell and scream too, so we decided to set about fixing it! Along with that, we've also discovered a bug we want to correct but unfortunately, it's a breaking change so we wanted to get your feedback and do it in NuGet 3.0.

## Target Framework Filtering in Search

We are deploying a change to make the nuget.org Search API filter out packages (and versions of packages) that are incompatible with the framework your project targets. This means that if you are targetting .NET 4.0, you will see MVC 4 but you won't see MVC 5 in the list, because MVC 4 is the latest version that supports .NET 4.0. This also means that if you're building a Windows Phone App, MVC won't show up at all because no versions of it are compatible with Windows Phone.

This feature should work in version 2.1 of NuGet and up. Since adding the new Search API in that version we've always sent the target framework of the project to the server, now we've just turned on the code on the server to do something with that data.

*We expect this to take effect around 6:00pm Pacific time on October 1st*

### Some Caveats

There are a few caveats and quirks with this change, but they should be minor and affect very few users. The biggest one is that if you open the dialog from a package that targets a Xamarin framework, a portable profile or a framework we don't understand, the filtering will be disabled. This is due to some limitations in our client-server interaction in NuGet 2.x. The good news is, we plan to fix those in 3.0! For the existing 2.x clients, we felt it would be better to just disable filtering when we don't recognize a framework rather than accidentally filter in or out packages that shouldn't be filtered.

We also may occasionally filter out a package that NuGet will *technically* allow you to install. In those cases, you can still use Install-Package from PowerShell to force NuGet to try and install the package. However, NuGet only allows you to install these packages due to what we believe is a bug and something that we will be correcting in 3.0.

## Breaking Change to Target Framework Handling

In NuGet 3.0, we will be introducing a small breaking change in our Target Framework handling. We strongly believe this will have a very minimal impact on users, but we want to tell you about it and get your feedback.

Consider a package with the following files:

    content/
             web.config.transform 
    lib/ 
         net40/ 
                MyThingy.dll 
         net45/ 
                MyThingy.dll
    

In NuGet 2.0, if a package contains a file in `content/` or `lib/` that is **not** within a target framework folder, the package is allowed to install in to **any project** targeting **any framework** no matter what folder structure is used in the other folders. However, we believe that this is not generally what the author intends. In the above example, the author likely intended for the package to be installable into `net40` and `net45` projects **only**, but for the same config transform to be applied **regardless of the target framework**. In fact, this is exactly how the EntityFramework package is structured (with a few extra files), meaning EntityFramework can technically be installed into a Windows Store JavaScript project. When you do that, all you end up with is a fairly useless app.config and an entry in packages.config:

![EntityFramework installation a JavaScript project][2]

So, in NuGet 3.0, this behavior will be **removed**. We think this makes the behavior much clearer and easier to understand. The NuGet 3.0 behavior will be the following:

1.  A Package has a set of zero or more *framework constraints*.
2.  If a Package has no *framework constraints*, it can be installed into any project, regardless of target framework.
3.  If a Package has *framework constraints*, it can be installed into any project that targets a framework **compatible** with any framework in the set.
4.  If a Project targets a Portable profile, a package can only be installed if it has no *framework constraints*, or contains a portable profile *framework constraint* that targets a **superset** (i.e. the same, or more, but never fewer) of the frameworks supported by the project.
5.  Package *framework constraints* are defined by the following algorithm: 
    1.  Start with an empty set of *constraints*
    2.  For each [Framework Assembly Reference][3] with a non-null `targetFramework` value, add that framework to the set of constraints
    3.  For each file in `content/`, `lib/`, `build/` and `tools/`: If file is in a subfolder of one of those top-level folders, AND the subfolder is a valid short framework name (i.e. net40, win81, etc.), add that framework to the set of constraints.

The net effect is this: If you never limit anything to a specific framework, your package can be installed into a project targetting **any framework**. However (here's the new bit): as soon as you start limiting package contents to a specific framework, you will have to specify all the frameworks you want to support *somewhere* in the package. We still use the same logic we always used when selecting the files **within** the package, so if a file is not in a framework subfolder we will copy it (or reference it, or import it, or whatever we normally do with it) regardless of your project's target framework. The only difference here is that adding a file that is not framework-specific does not lift the framework constraints placed by other files, whereas in NuGet 2.0 it would.

There's still plenty of time to discuss and adjust this policy so if you have comments, please let us know!

 [1]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/01-error.png
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/02-what-the-ef.png
 [3]: http://docs.nuget.org/docs/reference/nuspec-reference#Specifying_Framework_Assembly_References_(GAC)