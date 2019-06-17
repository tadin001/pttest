---
ID: 69
post_title: A New Package Statistics Warehouse
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/new-statistics-warehouse/
published: true
post_date: 2014-08-20 00:00:00
---
## The Warehouse is Dead, Long Live the Warehouse!

Back in June, we blogged that our [package statistics were full of lies][1]. We made a fix and expected reports to become correct on June 26th. When June 26th came and went though, we discovered that the numbers were still questionable. Since then, we've identified a few other issues leading to invalid statistics, and we've made notable improvements to our statistics system. We are now confident that the statistics are trustworthy.

As part of the work, we decided to discard our existing package statistics warehouse database and create a new one. This new warehouse would only be populated with clean data and none of the invalid, historic data would survive.

## How We Create Statistics Reports

If you don't already have the background on how we create our package statistics reports, here's an overview:

![Data Flow for Download Statistics][2]

You can read more about [this data flow][3] on our previous post about package statistics.

## What Went Wrong

The bug we fixed a couple months ago was related to how we pull data out of the primary gallery SQL database, where we weren't properly ordering the results and it led to cases where we would get the same results out multiple times over multiple runs. After fixing that and actively monitoring the data flowing through the system, we observed statistics still getting multiplied in our statistics warehouse.

Moving data from our primary gallery SQL database to our warehouse was done with a record-by-record replicate process. The logic was something like this:

1.  Begin loop
    1.  [Warehouse] Read our replication marker that indicates the most recent download statistic that has been replicated
    2.  [Gallery] Read a batch of download events to be replicated
    3.  Begin inner loop over those statistics
        1.  [Warehouse] Replicate each download event into the warehouse's [star schema][4]
        2.  [Warehouse] Update the replication marker to the key of the download event that was just replicated
    4.  Continue through all of the events in the batch that was just read
2.  Continue until a batch comes back empty because all events are older than the replication marker

There were a few issues with this process:

1.  It relied heavily on never having multiple instances of itself running
2.  It was a record-by-record replication that wasn't scaling very well
3.  It was basically an infinite loop since download events always come in faster than a batch of them can be replicated

After recognizing these three issues, we studied each of them closely to determine how impactful each issue was.

### Multiple Instances

We found circumstances under which we had 2 instances of the replication running in our backend system. And with the recognition that our process relied heavily on a single instance running, we studied the effects of 2 concurrent instances. We observed that our replication marker would increase (as expected) and then momentarily *decrease*. In our outer loop above, our two instances had each read a batch of events and were iterating through them at different speeds--this lead to duplication of the replicated stats.

As we saw with our previous replication bug, this was leading to random duplication of download statistics in the warehouse and proved to us that none of the data in our warehouse could be trusted.

### Scaling

Once we concluded our warehouse data was invalid, we began plans for discarding the warehouse and populating a new warehouse with only clean data. We began calculating how long it would take to reprocess the download events from the gallery database, replicating them into the new warehouse. For the 6 weeks of download events present in the gallery database, we found that it would take 2-3 weeks to process them. That means we were only able to replicate events 2-3 times as fast as they are coming in. That's not good!

We knew this meant we needed to build a more efficient replication process that could A) keep up with increasing download events, and B) catch us up in a reasonable timeframe.

### Infinite Loop

As the loop was constructed above, it becomes a virtually infinite loop. That is okay on the surface--since it will always have work to do, let it keep running. But for our backend work services, we like to monitor when processes start and how long they take--this allows us to know how long a job should take and then trigger alarms when jobs are running longer than expected. We needed to restructure the process so that we could monitor its progress more effectively and know when the process was truly hung or failing.

## Cleaning it Up

With the problems all understood, we set out to clean all of this up and resume our package statistics reporting as soon as possible.

### A New Warehouse

After some deliberation, we concluded that the data in our existing warehouse was pretty much worthless. Sure, it was still relatively representative of package downloads, but it certainly wasn't accurate. Besides, all of the reports we show on [nuget.org/stats][5] are limited to the last 6 weeks, and we had that data in our gallery database. So we could discard our existing warehouse; create a new, empty one; and populate it with cleanly replicated statistics.

### A New Replication Process

We replaced the existing record-by-record replication process with a new batch-based process. It takes the following approach:

1.  [Gallery] Read the most recent download event that we want to jump to
2.  Begin loop
    1.  [Warehouse] Read our replication marker that indicates the most recent download statistic that has been replicated
    2.  [Gallery] Read a batch of download events to be replicated
    3.  [Warehouse] Replicate the batch of events into the warehouse in one transaction, updating the replication marker at the same time
3.  Continue until we've replicated all the way through the initially-read download event from the gallery

This process protects us against the issues we had with the previous system in the following ways:

1.  Preventing concurrent processes from duplicating data
    1.  The [warehouse replication process][6] aggressively locks our replication marker table to guard against concurrent transactions
    2.  It also filters the events being replicated down to those that are newer than the replication marker
2.  Scaling to perform far better than 2-3 times the throughput needed, but instead about 25+ times what's needed
    1.  Pumping entire batches in and performing set-based operations allows SQL to shine the way it was meant to
    2.  At 25+ times the speed that download events occur, we can process a day's events in one hour or less
    3.  We were able to catch up our 6 weeks of data in about a day
3.  Allowing the job to exit and providing extremely verbose logging
    1.  The new job [emits logs][7] that have detailed information around how many records are left to process, the speed at which it's processing them, and how much estimated time remains
    2.  The job reads the gallery's latest download event before it begins the loop, and then it will only process up to that point
    3.  This allows the job to exit and work against a decent-sized batch the next time it's invoked, instead of continuing to process the trickle of events

### In Production Now

We put this new warehouse and process into production on Monday, August 18th. The deployment went smoothly and we've been actively monitoring the replication job--it's running very well.

## Results

So what does all this mean? Let's break down the impact all of this has had:

### Data Loss

We lost the "Packages Downloaded Per Month" report on <http://www.nuget.org/stats>. This chart shows the trend of package downloads for the whole system over time. It usually shows the trend over the past 6 months; now it just shows a dot for July. Once August wraps up, we'll see a new dot for August as well.

During all of this, we also had a window of a 8 days where we were failing to capture download events entirely. This was an accidental regression introduced into our [Metrics Service][8] deployment where it was failing to record events into the gallery database entirely. To be honest, we were distracted by the larger problems with download statistics and overlooked that service was failing as well. We failed to capture download events from July 30th to August 7th. That hit will likely be visible in the "Packages Downloaded Per Month" report at the close of August.

### Search Results

Because package installs over the last 6 weeks boost packages' search relevance, we had some periods of search relevance being off as our old warehouse lacked recent data but our new warehouse didn't yet exist. That is all corrected now and search relevance is properly boosted based on recent installs again.

### Lower Numbers

With the statistics reports back up and running, package authors will likely see that their download numbers are lower than they were before. That's because we're no longer duplicating the data--the numbers were inflated before.

Note that only the numbers shown on the package statistics reports were inaccurate before. The overall/total download counts have remained accurate through this whole process since those numbers aren't affected by the warehouse replication process.

## Going Forward

We've improved our monitoring for this whole system, and more members of the team thoroughly understand the processes now too. Overall, we should be able to maintain high confidence in our package statistics. We will keep a better eye on it nonetheless.

 [1]: /20140603/nuget-stats.html
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/dataflow.png
 [3]: /20140603/nuget-stats.html#background
 [4]: http://en.wikipedia.org/wiki/Star_schema
 [5]: http://www.nuget.org/stats
 [6]: https://github.com/NuGet/NuGet.Warehouse/blob/master/src/NuGet.Warehouse/dbo/Stored%20Procedures/AddDownloadFacts.sql
 [7]: https://github.com/NuGet/NuGet.Services.Work/blob/master/src/NuGet.Services.Work/Jobs/Stats/ReplicatePackageStatisticsJob.cs#L334
 [8]: https://github.com/nuget/NuGet.Services.Metrics