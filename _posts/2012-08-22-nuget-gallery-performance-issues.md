---
ID: 225927
post_title: NuGet Gallery Performance Issues
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-gallery-performance-issues/
published: true
post_date: 2012-08-22 00:00:00
---
Since August 16th, and especially since August 21st, the [NuGet Gallery][1] has been exhbiting intermittent performance issues. Some users are reporting errors when attempting to browse or search for NuGet packages from Visual Studio or other clients. Many users are reporting that browsing and searching are slow. There have also been timeout errors when browsing [the gallery's packages page][2].

During this time, we have been communicating with many of you on twitter, under the [#nuget tag][3], as well as in JabbR's [#nuget room][4]. We truly appreciate your patience and understanding with us.

## Status

We have been working to address these performance issues over the last several days, and we hope to have a deployment in place this evening (August 22nd, Pacific Time). It looks like the deployment will cause a short outage though--we will post more information about that when the details are available.

## Root Cause

The root cause of the performance issues boils down to some sub-optimal queries that are used when searching the gallery--especially when searching from Visual Studio. There are a couple reasons these queries are slow:

1.  Some missing indexes that could significantly aide the search queries.
2.  Table columns that have `nvarchar(max)` as their data type when a size constraint can be applied. We are seeing that when these `nvarchar(max)` columns are used in `ORDER BY` clauses, it becomes a bottleneck.

The deployment we're preparing will address both of these issues by adding size constraints to many fields and then creating several new indexes. Unfortunately, the process of altering the Packages and PackageRegistrations tables and then creating these new indexes is going to take some time to apply -- during that time, the database will be rather unresponsive. Again, when we know more about the timing of the outage, we'll share the details.

 [1]: http://nuget.org
 [2]: http://nuget.org/packages
 [3]: https://twitter.com/#!/search/%23nuget
 [4]: http://jabbr.net/#/rooms/nuget