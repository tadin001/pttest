---
ID: 55
post_title: Upgrade Successful
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-expected-downtime-completed/
published: true
post_date: 2012-08-22 00:00:00
---
Earlier this evening, [we announced][1] that the [NuGet Gallery][2] would be offline for about an hour during a scheduled upgrade. That upgrade is now complete.

We expect this deployment to help address [performance issues][3] we've been experiencing for the past week. We will continue to monitor the service and measure the results of this deployment.

## CDN Re-Enabled

As a bonus, this deployment has re-enabled [Windows Azure CDN][4] for NuGet packages. We had initially enabled the CDN last week but some users around the globe [reported][5] the DNS name of the CDN host could not be resolved. After a week, we trust that the DNS name has propagated and we can now all benefit from faster package downloads.

Thank you once again for your patience. If you encounter any issues with NuGet, feel free to report issues on [CodePlex][6].

To stay in touch with us, follow [@nuget][7], or the [#nuget tag][8] on twitter for live updates. You can also check in on JabbR's [#nuget room][9].

 [1]: http://blog.nuget.org/20120822/nuget-expected-downtime.html
 [2]: http://nuget.org
 [3]: http://blog.nuget.org/20120822/nuget-performance-issues.html
 [4]: http://msdn.microsoft.com/en-us/library/windowsazure/ee795176.aspx
 [5]: http://nuget.codeplex.com/workitem/2503
 [6]: http://nuget.codeplex.com/WorkItem/Create
 [7]: http://twitter.com/nuget
 [8]: https://twitter.com/#!/search/%23nuget
 [9]: http://jabbr.net/#/rooms/nuget