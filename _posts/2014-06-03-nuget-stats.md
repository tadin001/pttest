---
ID: 65
post_title: NuGet Package Download Stats
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-stats/
published: true
post_date: 2014-06-03 00:00:00
---
## Lies, Damned Lies, and Package Statistics

We've been getting reports about package download statistics being inconsistent with packages' overall download counts and people have asked us which numbers to believe. We've investigated the inconsistencies and found that due to a very subtle bug, ***you can't trust the statistics right now, but you'll be able to trust them again starting June 26th.***.

You can however trust the all-time download counts shown in search results and on the package details pages. Only the "Statistics" tab on the gallery is affected.

## Background

In order to fully explain the issue, we need to provide some background on how we record, aggregate, and report download statistics for packages.

![Data Flow for Download Statistics][1]

### Step 1: Record Downloads

Every time a NuGet package is downloaded from the gallery, we record the download statistics into our SQL database. We don't record the download directly against the package table, but instead into an table used as a queue. This allows us to avoid writing to the package table directly with every download--instead we aggregate the downloads in batches.

This raw data is never shown anywhere on the gallery.

### Step 2: Update All-Time Package Download Counts

Every 5 minutes, we take all of the downloads that have been recorded in our queue table and aggregate them. This aggregation tells us how many downloads each package had during the 5 minutes, and we update the package table in the database to increase the all-time download counts.

These aggregated, all-time download counts are displayed on the Package Details page for any package. For example, the [Json.NET][2] page shows the all-time downloads of all versions of the package as well as the all-time downloads for that specific version.

### Step 3: Update Search Index

Every minute, we update our search index using the already-aggregated all-time download counts. That means that it can be roughly 6 minutes from the time a package is downloaded to when the search index reflects that download. And for that minute in between aggregating package downloads and updating the search index, the search results might show a number slightly lower than the package details page.

This search index is used for showing the packages list, with or without a search term. If you search for ["json.net"][3], you'll see the search index's value for the all-time download count for the Json.NET package.

### Step 4: Replicate Downloads to Statistics Warehouse

We don't want to keep all detailed package download records in our primary database--it would grow far too much. Instead, we replicate statistics over to a warehouse database, and we purge old download records from our primary database. The replication happens every 10 minutes, and we copy all details about the downloads into the warehouse. We keep the following information:

1.  Package/Version
2.  Operation (install, install-dependency, update, update-dependency, restore)
3.  Date and Time (with precision of 1 hour)
4.  Dependent Package/Version where applicable
5.  NuGet Client (Visual Studio, WebMatrix, NuGet.exe, etc.) and Version

The warehouse uses a star schema, allowing us to produce reports from several angles.

### Step 5: Produce Statistics Reports

Every 12 hours, we produce package download statistics reports and store them. Any page on the gallery under the [Statistics][4] tab is rendering a report that was produced up to 12 hours earlier. These reports only reflect data from a rolling 6-week window of time and *not* the all-time download counts.

Because the statistics reports are always a 6-week window, the numbers should always be lower than a package's all-time download counts. But the reports we have been getting have shown that these statistics reports had *higher* values than the all-time numbers--that was obviously wrong.

## How Far Off are the Statistics?

It really varies from package to package. Generally, the more a package is getting downloaded, the more incorrect the statistics are. There's no consistency across the packages though, so we can't say exactly how much packages were affected. The only categorical statement we can make is: ***You can't trust the statistics right now, but you'll be able to trust them again starting June 26th.***

For example, just looking at a window of 1 hour, Microsoft.AspNet.Mvc version 5.1.2 had 649 actual downloads that hour but the package statistics recorded 3035 downloads (a difference of 2386, or more than 365%). On the other hand, knockoutjs 2.2.0 had an hour with 5 actual downloads and 11 recorded downloads (a difference of 6, or 120%).

## Timeline and Anatomy of the Bug

The bug was introduced back in January, but we didn't start getting reports of inconsistent data until April. We think the rollout of the [new search service][5] brought more attention to our data and led people to start filing issues. The fix took effect on May 15th. This means that package download statistics are accurate after May 15th. Since our statistics reports are based on a rolling 6-week window, the statistics will be trustworthy again on June 26th.

The bug was down in the process where we replicate data from the primary database to the warehouse (step 4 above). When pulling out the data not yet replicated, our SQL SELECT statement was missing an ORDER BY clause. This led to unpredictable results and a situation where we'd replicate the same data more than once.

If you're curious, it was a [simple one-line fix][6] to correct this problem.

## All-Time Download Counts are Accurate

Because the bug was in the process of replicating statistics over to the warehouse, only the warehouse and reports generated from it are affected. The all-time download counts are accurate on the package details pages and search results.

## Thank You

Thank you for reaching out to us through [GitHub][7] and [Twitter][8] to let us know the data looked bad.

 [1]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/dataflow.png
 [2]: http://www.nuget.org/packages/Newtonsoft.Json/
 [3]: http://www.nuget.org/packages?q=json.net
 [4]: http://www.nuget.org/stats
 [5]: /20140411/new-search-on-the-gallery.html
 [6]: https://github.com/NuGet/NuGet.Services.Work/commit/98eda2c433c9b44922dee12ef6f373fde87de864#diff-e23a546b77f6a24c4929bbe8513888aeR95
 [7]: http://github.com/NuGet/NuGetGallery/issues
 [8]: http://twitter.com/nuget