---
ID: 226097
post_title: The path towards better documentation
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/the-path-towards-better-documentation/
published: true
post_date: 2016-08-22 00:00:00
---
**Update 9/20 (11:00 A.M PST): The revamped NuGet Docs experience is now live on [docs.nuget.org][1]. Read more about it here - [New experience for NuGet Documentation][2]**

[docs.nuget.org][1] is the authoritative guide on everything NuGet. It is used as reference by nearly 180k developers with over 500k page views a month. Summary of the most consistent feedback we heard is given below:

1.  The grouping is not intuitive and makes it difficult to find the right document.
2.  Figuring out the end to end steps to build packages for new platforms is hard.
3.  Lack of indexing and cross referencing makes it difficult navigate between samples, reference docs and release notes.
4.  Some items only appear in release notes and blogs, but do not appear in the final docs.

In this post, we are going to talk about the changes we recently made based on your feedback, and plans going forward.

Before diving into details, we would like to thank a number of people who have helped us review and give feedback on these changes or inspired us to write new guides. Some of them have written a number of blog posts and tools that has significantly helped the community in understanding various package authoring techniques, NuGet concepts and APIs. There are many more who provided feedback through issues, tweets and other channels.

*   [Oren Novotny][3] (@onovotny)
*   [Dave Glick][4] (@daveaglick)
*   [Paul Betts][5] (@xpaulbettsx)
*   [Ryan Davis][6] (@rdavis_au)
*   [Nicholas Blumhardt][7] (@nblumhardt)
*   [Stephen Cleary][8] (@aSteveCleary)

## What did we change?

We have revamped the NuGet docs experience and we have a preview for you to try out. [NuGet Docs Preview][9] addresses the majority of feedback from the community. Just to be clear, we are not replacing [docs.nuget.org][1] just yet.

We have made several improvements including:

1.  New quick-starts for creating and consuming packages.
2.  End to end guides for new platforms such as .NET Standard and UWP.
3.  A simpler and more intuitive organization of topics.
4.  Left pane of contents serves as an index and allows you to glance at all topics.
5.  Cross-references with links to recommended reading, related topics and reference docs.
6.  And finally, a modern look and feel to the docs site.

![new nuget docs landing page][10]

## How are the new docs organized?

1.  Quickstarts - These are very basic guides for both authors and consumers to get started 
2.  Guides - The idea here is to give you everything you need to know to execute a scenario end to end. Some of the key guides we have built are:

*   [Installing NuGet][11]
*   [Creating a UWPpackage][12]
*   [Creating a .NET Standard package][13]
*   [Creating a Bait and switch package][14]

<ol start="3">
  <li>
    Creating, Consuming and Hosting packages
  </li>
  <li>
    Tools, Schema, API and Visual Studio Extensibility - Reference documentation
  </li>
  <li>
    Policies - FAQs, Governance, and the policy around Dispute resolution and Deleting packages
  </li>
  <li>
    Contribute guidance has been moved to the <a href="https://github.com/NuGet/Home/wiki/Contribute-to-NuGet">Nuget/Home wiki on Github</a>
  </li>
</ol>

## Will my existing references break?

No, existing references will not break. When we update [docs.nuget.org][1], we'll have permanent redirects in place that will redirect you to the new page.

## How can I Contribute?

We made the contribution link a lot more obvious. Every page that allows contribution has a “Edit in Github” link right at the top. This link will you take you to the md file. Feel free to make changes and create a PR from your branch. The NuGet team will review the change and work with you to get it in.

![edit in github][15]

If you would like to raise a request for new docs or changes to existing docs, feel free to [open a new issue][16].

## What are we going to do moving forward?

With the advent of .NET Core, the NuGet community is growing by leaps and bounds. We understand that we have to lower the barrier to entry to make it easy for authors to create packages and for users to consume them. The first step there is to have fantastic documentation in place that enables this.

Over the next two weeks, we'll closely look at any feedback on [NuGet Docs Preview][9] and iron out any wrinkles before updating the old site [(docs.nuget.org)][1].

In the coming quarter, we are looking into starting our docs migration into docs.microsoft.com similar to [.NET Core documentation][17]. Since NuGet and .NET are tied at the hip for the majority of its use cases, it’ll make it easier for our users to find and reference docs. In addition, we’ll have a consistent way to get feedback and enable contributions to docs from our users.

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. If you would like share your pain points and your current and future needs, use the [calendly link][18] to set up a quick 15-30 min call with us. If you would like to send us an email instead, hit us up at <feedback@nuget.org>.

You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][16].

 [1]: https://docs.nuget.org
 [2]: http://blog.nuget.org/20160920/NuGet-Docs-GoLive.html
 [3]: https://oren.codes/
 [4]: http://daveaglick.com/
 [5]: http://paulbetts.org/
 [6]: http://ryandavis.io/
 [7]: https://nblumhardt.com/
 [8]: http://blog.stephencleary.com/
 [9]: https://docspreview.nuget.org
 [10]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/nuget_docs_website.png
 [11]: https://docspreview.nuget.org/ndocs/guides/install-nuget
 [12]: https://docspreview.nuget.org/ndocs/guides/build-for-uwp
 [13]: https://docspreview.nuget.org/ndocs/guides/build-for-net%20standard
 [14]: https://docspreview.nuget.org/ndocs/guides/create-cross-platform-packages
 [15]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/edit_in_github.png
 [16]: https://github.com/NuGet/Home/issues
 [17]: https://docs.microsoft.com/en-us/dotnet/articles/core/index
 [18]: https://calendly.com/harikm/nuget-feedback