---
ID: 180
post_title: >
  NuGet 2.8.1 Released, April 2nd-3rd
  Downtime, and the New Search Service
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-2-8-1-april-2nd-downtime/
published: true
post_date: 2014-04-03 00:00:00
---
This blog post was planned to be published on April 2nd as the NuGet 2.8.1 release announcement. However, on that same day (also the first day of [Build 2014][1]), NuGet.org suffered a severe service interruption. It didn’t seem right to blog about the NuGet 2.8.1 release without also covering the interruption, so we waited a day and combined the posts.

## NuGet 2.8.1 Released with Windows Phone 8.1 Support

Let’s cover the fun stuff first! On April 2nd, we released NuGet 2.8.1 to the Visual Studio Extension Gallery. You can get the updates from within Visual Studio's Extensions and Updates dialog, or directly from the extension gallery. We also published NuGet.exe 2.8.1.

Here are the downloads:

1.  **Visual Studio 2013**: [Visual Studio Extension Gallery][2]
2.  **Visual Studio 2010 and 2012**: [Visual Studio Extension Gallery][3]
3.  **Command-Line Utility**: [Direct Download][4]

NuGet 2.8.1 includes support for Windows Phone 8.1, including both Silverlight-based libraries and WinRT-based libraries for Universal Apps. For Silverlight-based Windows Phone 8.1 libraries, packages use the "wp81" framework name. For WinRT-based Windows Phone App 8.1 libraries, packages use the "wpa81" framework name.

In addition to the Windows Phone 8.1 support, we also fixed over a dozen bugs--mostly in nuget.exe. See the [release notes][5] for other details about the release.

## April 2nd-3rd Downtime

As we tweeted about, we learned we had a few more vulnerabilities to heavy load than we had previously understood. Perhaps due to the Build conference, or perhaps just a coincidence, [www.nuget.org][6] was experiencing unusually high browser traffic early in the morning on both April 2nd and April 3rd. The extra load ultimately led to interruptions in most of our services.

### Services Impacted

During the times of interruption, the following services were impacted:

*   The website's Packages page showed 0 packages
*   Search on the website and in Visual Studio reported no packages
*   The feed for Visual Studio reported no packages
*   Users were unable to sign into the website or upload packages
*   Some users' package restore operations failed

### Root Cause

The www.nuget.org home page makes http requests to a /stats/totals endpoint that performs a query to get the home page statistics to show the number of packages and downloads. The request was configured to be cached, but the cache wasn't behaving as we expect. This resulted in SQL queries for each request. Under heavy load, these requests backed up and the queries became locked on each other.

Additionally, all of our web server instances were maintaining their own copies of our Lucene search index. On a schedule, the servers would all query the database to update the index as needed. These queries are expensive and as we scaled out to more instances under load, the queries were running frequently. With so many expensive queries running, these queries started timing out and causing the search indexes to become corrupt.

### Solution

In order to reduce the load on our SQL database, we have implemented two changes that are now deployed to www.nuget.org.

1.  We have completely disabled the home page query (instead returning static numbers for the time-being).
2.  We have deployed our new Search Service which was planned to be released next week.

## New Search Service

At 8:00pm PDT on April 3rd, we deployed a significant update to [www.nuget.org][6] that changes the search implementation to use a dedicated [Search Service][7].

We had planned to deploy this new search service next week, but the downtime we encountered April 2nd-3rd changed our plans. The goals of the new Search Service include reducing load on our SQL Azure database and moving our Lucene search index out of the web servers and into Azure Blob Storage. The Search Service runs independently, reading the index from blob storage, and the index is now maintained by back-end processes rather than on the web servers themselves. We're also able to direct more queries to the search service than we could handle with our previous index, relying less on SQL.

The root cause analysis of our downtime uncovered that the SQL Azure load was the primary culprit, with the Lucene index updates being a significant contributor. This new Search Service allows us to control the SQL load from our backend processes rather being tied to web traffic.

We will publish a detailed blog post next week, but here's what to expect from the new Search Service:

1.  Search relevance has been completely overhauled. We now boost search results by "recent installs" (the last 6 weeks of direct installs/updates) and we have drastically improved text analysis of package metadata.
2.  Sort Options have been removed from the website. Sorting by recent installs now produces the best results. The sort options remain in Visual Studio and are still respected by our API.
3.  The dropdown for "Include Prerelease" and "Stable Only" has been removed from the website. As Prerelease packages have become more popular, this feature tended to caused confusion rather than provide benefit. The dropdown is still in use in Visual Studio.
4.  The search box on the website is now much bigger, promoted into the header, and more user-friendly. Given search is the primary use of the site, this change was long overdue!

![Updated header for www.nuget.org][8]

Please let us know what feedback you have on the new Search Service.

## Downtime Timeline and Details

Here is our timeline of the downtime. All times are Pacific Daylight Time.

*   **2014-04-01**
    *   **11:30pm** - Some users started reporting incomplete search results late in the night on April 1st. This appeared to just be our search index's eventual consistency (although we now know that wasn't the case).
*   **2014-04-02**
    *   **12:00am to 4:00am** - The index shrank all the way down to 0 packages during this time window.
    *   **4:00am** - The index's state of 0 packages was affecting the website's Packages page, and search on the website and in Visual Studio, and the Visual Studio feeds--everything reported 0 packages. This triggered our automated alerts and it began paging us.
    *   **4:45am** - The interruption was confirmed; investigation had begun; and the initial notification was posted to twitter.
    *   **5:30am** - Some background services had been shut down to reduce load on the system. We also found that package download statistics had not been getting automatically purged after migration into the warehouse, so a manual purge had begun.
    *   **7:00am** - Azure Support was engaged and they began helping us identify blocked queries and execute some SQL maintenance scripts to recalculate DB statistics and rebuild indexes.
    *   **8:40am** - We deployed an update to www.nuget.org that removed an unnecessary http request to /stats/totals from every page. This request had been identified as triggering a SQL query that was causing locks.
    *   **9:00am** - All SQL indexes finished rebuilding and all DB statistics were recalculated. The package download statistics purge was over 33% complete.
    *   **10:30am** - We found that our Output Cache was not working for the /stats/totals request, and every single user hitting the www.nuget.org home page was resulting in SQL queries to calculate the totals.
    *   **10:45am** - We deployed another update to www.nuget.org that hard-coded the values for the /stats/totals request (that serves the home page numbers) instead of querying SQL.
    *   **11:00am** - The load on our SQL database was drastically reduced and connectivity issues went away. We rebuilt our search indexes successfully and the site was fully functional again.
    *   **12:20pm** - The package download statistics purge completed and all background services were resumed.
    *   **11:55pm** - There were reports that our index was shrinking again. We successfully rebuilt them manually.
*   **2014-04-03**
    *   **4:00am** - Our monitoring alerted us that the search index was corrupt again.
    *   **4:30am** - Our indexes were manually rebuilt successfully.
    *   **7:30am** - We were under heavy load again and our web servers were scaling out.
    *   **7:45am** - Some of our new web server instances failed to build their search indexes, manual rebuilds failed, and other web server instances' indexes became corrupt.
    *   **8:00am** - Many DB queries were failing; we engaged Azure support.
    *   **9:00am** - Diagnosis determined that the queries running to rebuild and update the Lucene indexes were significantly contributing to the SQL load. We began the work to finalize the Search Service deployment that was planned for next week.
    *   **10:45am** - Traffic dropped off, the web servers scaled back down, search indexes were successfully rebuilt.
    *   **11:00am** - We began preparing a backup strategy deployment that would still use our web server based search indexes, but would disable all automatic updates to the index (limiting the index updates to manual rebuilds only).
    *   **11:00am-8:00pm** - The website ran steadily all day under normal load. We continued our work and testing on the new Search Service while also validating our backup strategy deployment in case the Search Service deployment doesn't go smoothly (we were planning for another week of testing).
    *   **8:00pm** - We deployed the new Search Service and the updated www.nuget.org front-end that uses the Search Service. We also deployed our backup strategy deployment into the site's staging slot in case we need to switch to it.

The deployment we completed tonight should very significantly reduce the load on SQL by:

1.  Not performing queries to get the home page statistics for every page load.
2.  Running a single search index update process in the back-end instead of one on each web server.
3.  Sending more requests into our search service instead of directing them to SQL.

### Work Items Discovered

While working through this incident, we identified a handful of work items that we will tackle immediately.

1.  Determine why the ASP.NET caching isn’t working for the /stats/totals request
2.  Replace the /stats/totals query with a background service that calculates the totals on a schedule and stores the results in blob storage to be served from the gallery rather than ever calculating the numbers directly
3.  Determine why Package Restore was sporadically failing under the database load – as this was thought to be immune from database exceptions
4.  Schedule more DB maintenance tasks through our background services to rebuild indexes and recalculate DB statistics
5.  Fix the bug leading to our package statistics not being purged after being replicated into the warehouse database
6.  Complete the work for having a read-only mirror up and running for when our primary site is down • This work was already ongoing, but we are increasing the priority of it to have it finished very soon

### Appreciation

We appreciate that while many of you reported the service interruption on twitter using either [@nuget or #nuget][9], you were kind and polite about it. We know that there are countless developers around the world that rely on nuget.org being up and running all day every day, and we're sorry that we let you down. We are working to improve our reliability.

Thank you for your support,

[Jeff Handley][10] and the entire [NuGet team][11]

 [1]: http://www.buildwindows.com
 [2]: http://visualstudiogallery.msdn.microsoft.com/4ec1526c-4a8c-4a84-b702-b21a8f5293ca
 [3]: http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
 [4]: http://nuget.org/nuget.exe
 [5]: http://docs.nuget.org/docs/release-notes/nuget-2.8.1
 [6]: http://www.nuget.org
 [7]: https://github.com/nuget/NuGet.Services.Search
 [8]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/header.png
 [9]: https://twitter.com/search?q=%40nuget%20OR%20%23nuget
 [10]: http://twitter.com/jeffhandley
 [11]: http://twitter.com/nuget