---
ID: 153
post_title: Recent NuGet Gallery Updates
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/recent-nuget-gallery-updates/
published: true
post_date: 2013-04-11 00:00:00
---
The NuGet Gallery crew has gotten into a rhythm of publishing updates every 2 weeks. With the increased update frequency, we found that we needed to create a changelog to highlight new features. That changelog is now available directly from within our [source repository on GitHub][1].

You can access the changelog directly here: <https://github.com/NuGet/NuGetGallery/blob/master/Changelog.md>

Here is the current log, showing the changes that have been made over the last 2 deployments.

## April 4, 2013

### Top 500 packages exposed in the feed

The nuget.org API (V2) feed now exposes the top downloaded packages (over the last 6 weeks). This can accessed be via url [nuget.org/api/v2/stats/downloads][2]. At this time, the top 500 packages are shown by default and that is also the maximum number returned.

You can limit the numbers of results using ?count in the query string. For example, [nuget.org/api/v2/stats/downloads?count=10][3] would return the top 10 downloaded packages in last 6 weeks - with information like download count, gallery url and feed url for that package.

The default and maximum count of 500 might change over time, so we recommend always specifying a count parameter if you are programmatically consuming this data.

### Numeric rank for packages stats

The [Statistics page][4] now shows the numeric rank of the package (based on the download count).

### Links to gravatar in profile page

The profile editing page now includes help text and a link to gravatar making it easy for users to update their profile picture.

### UserName optimization in DB (backend)

The "Users" table is optimized to have "UserName" as index for performance enhancements.

### Other minor bug fixes

Complete list can be found here: [Production Deployment 4/12][5]

## March 28, 2013

### Support for MinClientVersion

You can now upload packages with "[minclientVersion][6]" to the NuGetGallery.

The minclientVersion of the package will shown in the package home page right next to the package description.

### Contacting support

The "Report Abuse" page has been revamped to enable users to chose the specific issue with the package they are reporting. It also guides the user to differentiate between "Contact Owners" and "Report Abuse". 

### Improved package statistics

The package statistics now shows the break down of downloads based on the NuGet client (like NuGet CommandLine 2.1, NuGet Package Manager console 2.2 and so on. It also shows the split of the type of download operation (like Install, Restore, Update).

### Other minor bug fixes

Complete list can be found here: [Production Deployment 3/28][7]

 [1]: https://github.com/NuGet/NuGetGallery
 [2]: //nuget.org/api/v2/stats/downloads
 [3]: //nuget.org/api/v2/stats/downloads?count=10
 [4]: http://nuget.org/stats
 [5]: https://github.com/NuGet/NuGetGallery/issues?milestone=18&state=closed
 [6]: http://nuget.codeplex.com/wikipage?title=NuGet%202.5%20list%20of%20features%20for%20Testing%20days%203%2f27%20to%203%2f29%20%2c%202013
 [7]: https://github.com/NuGet/NuGetGallery/issues?milestone=17&state=closed