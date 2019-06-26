---
ID: 226207
post_title: NuGet Package Identity and Trust
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-package-identity-and-trust-2/
published: true
post_date: 2017-04-17 00:00:00
---
**Update on 10/16/2017:** Package ID Prefix Reservation is now live. The documentation can be found [here][1].

We want to start this post with a huge thanks to you, the NuGet community. Over the last several months we have been talking to many of you to get feedback on NuGet package identity and trust. We’ve learned so much from you and we hope that what we have planned addresses some of the top concerns you have expressed to us.

One of the recurring messages we heard from you is that it is difficult to determine the origin of a package. More specifically, who produced and published the package that you want to use, and is that person or organization trustworthy?

We want to tackle this problem head on – read on to see what’s in store for our first feature to address NuGet package identity. We are still working on the finer details of the solution, so any feedback you have is greatly appreciated. Please leave a comment on this post or reach out to me (<dajaco@microsoft.com>) directly.

## Summary

Below you can find a quick summary of the first problem we are trying to solve, and our proposed solution. Read past the summary to get into some of the nitty gritty details of both the problem and our first set of solutions.

### Identity Problem

Today, there are multiple properties that identify a NuGet package. Each property serves a purpose, but this sometimes makes it difficult as a NuGet package consumer to fully understand the source of a NuGet package. Ultimately, we’ve observed customers want to know who produced and published the package they are about to consume.

### Initial Solution

To empower NuGet package consumers to quickly identify that the packages they consume originates from a trusted package owner, we are introducing a concept known as [verified package owners][2]. This feature will be exclusive to accounts registered with nuget.org. When an account owner is verified, we will provide a visual indicator for NuGet package consumers to know if the packages they are consuming originate from package owners that have verified their identity with the nuget.org server administrators. This should give NuGet package consumers the ability to quickly identify the true source of their package.

In addition, verified package owners will have an opportunity to apply for a reserved package ID prefix. Only the owner that reserves a package ID prefix will be able to submit new packages that match the ID prefix glob pattern. For example, the .NET Foundation will manage the package prefix \*\*System.\*\** – if you want to submit a package that matches the \*\*System.\*\** naming pattern, you will first need to get permission from the .NET Foundation.

### Next Steps

Based on your feedback, we realize there are several other areas of investment that we need to consider and prioritize over time. The verified package owner and reserved package ID prefix feature set is a tactical first investment to set us up for success in the future, and to empower NuGet package consumers with the information they need. As we continue to work on improving package identity, we want to make sure that we don’t negatively impact existing NuGet scenarios across all NuGet clients.

Some other areas we are considering that are related to package identity are:

1.  [Making package licensing information more obvious to package consumers][3]
2.  [NuGet package signing with digital certificates][4]
3.  [Consistent UI for package ID and package titles in VS and nuget.org so you can quickly uniquely identify a package][5]

## Background on NuGet package owners, authors and package IDs

To understand the source of confusion in package source identity, it’s important to review how NuGet works today. From talking to you, we have identified three properties that package consumers rely on to determine the “source” of the NuGet Package:

*   Package owner
*   Package author
*   Package ID prefix

Each of these serve a separate purpose, but having different identifying properties makes it difficult to answer one extremely important question: **who produced the package I am about to use in my project?**

### Package owner

The package owner is the account on nuget.org that published the package. This value is unique to the nuget.org feed – local feeds or other remote feeds may or may not have any concept of a package **owner**. This is the value that is displayed when searching for packages on nuget.org:

![Package Owner][6]

For the [EntityFramework][7] package, the owners are [aspnet][8], [EntityFramework][9], and [microsoft][10].

### Package author

The package author is defined in the .nuspec file of the .nupkg – it is supposed to represent the person or organization that wrote the code for the package. Let’s look at the [EntityFramework][7] package again:

![Package Author][11]

The listed author for the EntityFramework package is “Microsoft”. The data presented to you on nuget.org is different than what you see in Visual Studio, but in this case (as in most cases), the information is consistent. Because one of the owners on nuget.org is [microsoft][12], and the author is also listed as Microsoft, you can be confident this package indeed comes from Microsoft.

### Package ID prefix

Another property that package consumers use to identify the source of the package is the package ID prefix. When installing a package on the [Package Manager Console][13], this is the beginning of the package ID – e.g. for the popular [ASP.NET MVC package][14], the package ID prefix is **Microsoft.AspNet.MVC**. Because it starts with **Microsoft**, many users trust that this package comes from Microsoft as the owner and author of the package.

### Examples of package identity causing potential confusion

Below are just three examples of packages that may be confusing to package consumers. In each of these cases, one of the three properties don’t provide consistent information about the package. For example, the package author may be **Microsoft**, but the nuget.org account [microsoft][12] may not actually be an owner on the package.

*   [System.Linq.Dynamic][15] 
    *   Author is listed as **Microsoft**, but [microsoft][12] is not a package owner for this package
*   [System.Ben][16] 
    *   \*\*System.\*\** should be reserved for foundational .NET packages
*   [Microsoft.Office.Interop.Excel][17] 
    *   Author is listed as **Microsoft**, but [microsoft][12] is not a package owner for this package

**Now, I want to stress that these packages are not necessarily bad packages!** We are trying to empower NuGet package consumers with information about the package identity. Even though [System.Linq.Dynamic][15] has **Microsoft** as the listed author, but is not owned by [microsoft][12], the package is still a good package – in fact, it has close to 1 million downloads from .NET community members.

## Solving the package identity crisis

After talking to many NuGet community members, we have several ideas on how to fix this NuGet package identity problem. Our number one goal is to provide NuGet package consumers with meaningful information about who their package is coming from. In other words, when you are consuming a **Microsoft.*** package with a **Microsoft** author, you should quickly be able to determine if it *actually* comes from **Microsoft**, or if it comes from a different person or organization.

To do this, we are going to introduce two features: **verified package owners** and **package ID prefix reservation**.

### Verified package owners

A verified package owner in its simplest form is just an account on [nuget.org][18] who has proven to [nuget.org][18] administrators that they are who they say they are. They have gone through an application and verification process to prove their identity. In addition, verified owners will be held to a stricter set of terms. Most notably, the author property for their packages must properly identify the source of the package. When you see **Microsoft** in Visual Studio next to a verified owner indicator, you can be confident that it comes from **Microsoft**. If you see **Microsoft** as an author for a package without the visual indicator, then you may want to do some additional research to determine who owns and publishes the package.

### Criteria for package owner verification

Verified package owners will **not** be available to all accounts on [nuget.org][18]. Verified package owners must meet certain crtiera:

*   There must be significant risk of your packages or identity being confusing to nuget.org package consumers. This is often but not exclusive to the following: 
    *   Impact your packages have on the .NET community
    *   Popularity of your NuGet packages (number of downloads from nuget.org is just one indicator of popularity)
*   Being a co-owner of a package with a verified owner 
    *   This is to drive confidence in the contents of a package that is owned by multiple owners

To start, this will be a limited program that we will expand over time. Verified package owners will get several benefits. The most important one is that top-level packages that are owned by verified owners will have a visual indicator on both nuget.org and in Visual Studio that the package originated from verified owners. We will not evaluate if all transitive dependencies are also verified. If you can trust the author/publisher of the top-level package, you should be able to trust their ability to determine their package dependencies.

### Verified package owner mockups

You can see several possible examples of the visual indicator in the mockups below:

![nuget.org Verified Owners][19]

![Visual Studio Verified Owners][20]

### Workflow improvement with verified package owners

We’ve learned from our customers about typical package consumption workflows – especially when package identity and trust is important. An example of a typical workflow before verified package owners are available is:

*   Discover package in the Visual Studio Package Manager UI or on the internet
*   Look at package author in Visual Studio
*   Refer to [nuget.org][18] to look at package owners and number of downloads (and sometimes source code)
*   If the package owners somewhat match the author in Visual Studio, and the package seems generally trusted in the community, then add the package reference

With verified package owners, we hope to reduce the workflow for many of the most critical .NET packages to the following:

*   Discover the package in Visual Studio Package Manager UI
*   See the verified indicator on a package with an author that you trust, and reference the package

Only if the package is not coming from verified owners that you trust should you have to resort to additional research on nuget.org or elsewhere.

## Package ID prefix reservation

While we think package owner verification is a good start at improving identity related confidence for NuGet package consumers, we also want to address some concerns many package authors have expressed to us. There are cases where developers clone an open source repository, create a NuGet package, and publish it to [nuget.org][18] with an appropriate package ID. While this is fine in practice, we’ve had several complaints that the package was intentionally not yet on nuget.org by the author of the code. Many times, this is because the code is not yet ready for broad distribution. It could still be pre-release code or not fully supported.

As the code evolves and is ready to be submitted as a NuGet package, the original author of the code cannot publish the package with the package ID they want (since it had previously been published).

With verified package owners being able to reserve package ID prefixes, this problem should be mitigated for key .NET partners. The most influential .NET contributors will be able to reserve a package ID prefix so that future package submissions that match the ID prefix pattern are reserved for them. This allows them to develop their code in the open, and only submit to nuget.org when they are confident the package is ready for broad consumption. If other community members want to publish the package earlier, they are welcome to use a different package ID prefix.

We will not retrofit this to all previously submitted packages (so we don’t destroy existing dependencies), but we will evaluate existing packages and may delist or remove packages that appear to be intentionally abusing a package ID prefix.

## Criteria for package ID prefix reservation

Applying for a reserved package ID prefix will require package owners to be verified. Once the package ID prefix application is submitted, the [nuget.org][18] team will review the following criteria with the .NET Foundation:

*   Does the ID prefix properly identify the package owner?
*   Are a significant number of the packages that have already been submitted by the owner under that package ID prefix?
*   Is the package ID prefix something common that should not belong to any individual owner or organization?
*   Would *not* reserving the package prefix cause ambiguity and confusion? 

What we are striving for is clear messaging on identity of the package and the source of the package. All issued reserved prefixes will be reviewed with .NET Foundation with all criteria heavily considered.

## Feedback!

There are a lot of complexities with NuGet package identity and trust. For us to address your needs properly, we desire your input. Please let us know what you think about this in the comments below or by emailing <dajaco@microsoft.com>. Alternatively, you can post your comments on the [NuGet issue][2] tracking this work.

We sincerely thank you for your feedback as we continue to build a better NuGet for everyone!

 [1]: https://docs.microsoft.com/en-us/nuget/reference/id-prefix-reservation
 [2]: https://github.com/NuGet/Home/issues/1882
 [3]: https://github.com/NuGet/Home/issues/4628
 [4]: https://github.com/NuGet/Home/issues/2577
 [5]: https://github.com/NuGet/NuGetGallery/issues/3791
 [6]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/entity-framework-owner.png
 [7]: http://www.nuget.org/packages/EntityFramework
 [8]: http://www.nuget.org/profiles/aspnet
 [9]: http://www.nuget.org/profiles/EntityFramework
 [10]: http://www.nuget.org/profiles/microsoft
 [11]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/entity-framework-author.png
 [12]: https://www.nuget.org/profiles/microsoft
 [13]: https://docs.microsoft.com/en-us/nuget/tools/package-manager-console
 [14]: https://www.nuget.org/packages/Microsoft.AspNet.Mvc
 [15]: http://www.nuget.org/packages/System.Linq.Dynamic/
 [16]: http://www.nuget.org/packages/System.Ben/
 [17]: http://www.nuget.org/packages/Microsoft.Office.Interop.Excel/
 [18]: https://www.nuget.org/
 [19]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/nuget-org-verified-owners.png
 [20]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/vs-verified-owners.png