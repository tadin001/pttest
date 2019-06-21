---
ID: 225929
post_title: Feed Performance Update
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/feed-performance-update/
published: true
post_date: 2012-08-24 00:00:00
---
As you might know, NuGet has been having some performance (and timeout) related issues recently. Earlier this week, we [completed a deployment][1] that helped, but it didn't address everything.

Many users are still seeing slow responses or even timeouts when trying to use the 'Manage NuGet Packages' dialog in Visual Studio.

## Ongoing Investigation

The deployment earlier this week greatly improved the [packages page][2] on the gallery, but it didn't address the Visual Studio dialog performance as much as we had hoped. Since that deployment, we've been focusing on the queries behind the Visual Studio dialog.

We have found that the SQL queries that get executed from the 'Manage NuGet Package' dialog are not great. While the execution plans look okay, the memory grants for the queries are HUGE--MUCH bigger than they need to be. Because of the huge memory grants, the queries are stacking up behind each other waiting for memory to be granted. This is leading to poor performance and also timeouts.

We are working to get a different approach for these queries in place. We will let you know when a fix is ready. If you're curious about what the queries look like, you can see the current and tentative rewritten queries [here][3]. The rewritten queries are using about 1/100th of the memory of the current queries.

To stay in touch with us, follow [@nuget][4], or the [#nuget tag][5] on twitter for live updates. You can also check in on JabbR's [#nuget room][6].

 [1]: http://blog.nuget.org/20120822/nuget-expected-downtime-completed.html
 [2]: http://nuget.org/packages
 [3]: https://gist.github.com/3442185
 [4]: http://twitter.com/nuget
 [5]: https://twitter.com/#!/search/%23nuget
 [6]: http://jabbr.net/#/rooms/nuget