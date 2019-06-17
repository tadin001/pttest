---
ID: 91
post_title: Meet the new NuGet status page
author: Maarten Balliauw
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/meet-the-new-nuget-status-page/
published: true
post_date: 2015-09-28 00:00:00
---
On the server side we are working on boosting performance, scalability and reliability. We were also in need of a new status page to better communicate the status of the service. Without further ado: meet the new [status.nuget.org][1]!

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/new-status-page.png" alt="The new NuGet status page" style="width: 100%" />

We're happy with this new incarnation of our status page. It not only shows whether individual services are up or not, it also adds some insights in our back-end jobs like search indexing. And when we want to communicate written context about potential issues, we can now do so as well.

## Services status

The question we get when we have a production service issue is the one which is displayed in a prominent position on the status page: is NuGet up or not? All our status checks are aggregated into one status which immediately tells users of our status page what is going on.

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/service-status-summary.png" alt="Status summary - is NuGet up or not" style="width: 100%" />

One of the key elements that we kept from the previous status page is the services status. Where our old status page just showed indivivual checks, we decided to show the current status of individual NuGet features. By clicking a particular feature we can see more details of the individual services that contribute to that feature, including their datacenter region when applicable.

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/service-status-drill-down.png" alt="Status drill down shows status per feature" style="width: 100%" />

## Insight into back-end jobs

NuGet is not only front-end. Package uploads are a separate service. We also have several jobs running in the background - for example our indexing job. The index lag chart shows the time it takes for a new package to show up in the index. The lower the time, the faster new packages are available for consumption.

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/chart-index-lag.png" alt="Charts for various back-end jobs" style="width: 100%" />

## Additional context through status messages

When we detect a potential issue with the NuGet services, we are now able to comminicate about it through the status page. Whenever there's a "hot" issue, we will show additional context about the issue at the top of the status page.

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/communication-via-status-posts.png" alt="Issue status messages" style="width: 100%" />

Older messages are shown near the bottom of the page. Messages are visible for one week.

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/historic-messages.png" alt="Older status messages" style="width: 100%" />

With this new status page we now can provide more insight into the status of the various services that serve NuGet features and provide more accurate details on potential issues through status messages.

 [1]: http://status.nuget.org