---
ID: 83
post_title: NuGet.org server status
author: Yishai Galatzer
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-server-status/
published: true
post_date: 2015-06-08 00:00:00
---
For the last few weeks we have experienced a few hours of downtime a few times a week. The NuGet community was very engaged in sharing the issues both on Twitter, <support@nuget.org>, personal emails, and GitHub. We thank you for that and appreciate your patience.

We realize NuGet.org is a center to many of the day to day activities of a .NET developer. It is critical and we are working hard to make it better.

We promised to get back to you with an update, here it goes.

### NuGet.org state - early May 2015.

NuGet.org was initially built about four years ago on top of a SQL database and OData services and NuGet.org was a monolithic single web site. Some time ago the site was broken into micro services but most of the monolithic website was still left running with some areas of the code now calling or using the micro services.

The services are running in multiple regions to allow for geo based redundancy. The site code was pretty much unchanged since August 2014 with only minor updates and improvements to the jobs infrastructure. As time went by the number of packages increased, as well as general traffic to the service and site and we hit a point in time when things started to become unstable.

Here is a simplified diagram of NuGet.org

![Server architecture diagram][1]

### What were we seeing?

Every few days/weeks we would see the search service become unusable, each of the servers running the service will start failing to a point that the machine was not accessible anymore, but will recover within a short few minutes. Unfortunately once in a while that will happen in a loop, and we will see our machines recycle. 

The team was mostly focused on the Visual Studio 2015 release and the server issues were initially looking like temporary hiccups we thought we can deal with when the pressure for shipping the NuGet for Visual Studio 2015 subsided. We still had a few people working on server improvements, monitoring and addressing alerts. We kept things going typically by quickly recycling the servers/jobs/Web Sites and with the automatic fallbacks built into Nuget.Org.

Unfortunately this became a pretty regular trend a few weeks ago, and it was clear we don't have enough focus in the server area, and we decided to increase the focus there.

We started with a plan to improve the server, along the following lines

1.  Consolidate pieces of code that were pretty much doing the same thing.
2.  Improve our backend jobs processing the newly uploaded packages.
3.  Add additional monitoring and tracing to the servers to better pinpoint issues.
4.  Fix the clear items coming up from reviewing the logs.

### Highlights of current improvements

#### Backend jobs improvements

When the NuGet.org service recovered from one of the outages, we discovered that our fail-over data center was not being updated properly and package information was several weeks old. Packages published in the month of May did not appear in the search index.

We have investigated the issue with these backup jobs, corrected it and applied monitoring to ensure that our backup data-centers are never that far out of date again.

We consolidated all the backend jobs from two frameworks into a single framework, wrapped them in [nssm][2] for auto restart and now in the process of adding an auto restart for jobs that are hanging.

#### NuGet Service Degradation

We had an auto healing feature that would switch to a different region when the machines in the search services were not responding/down. However in this case the services were not fully down. The behavior observed was that the service will stop responding for a few minutes to respond again. The response times also became significantly longer. Our switching code interpreted this incorrectly as a healthy/recovered state, and hence we never switched off to another data center.

We made a change and now we will actively work with both data centers regularly, preferring the healthier search services. This was deployed on June 4th, which worked great in the June 5th outage, well until the other region went down as well. More on that below.

#### Search service improvements

We identified a few major issues in the search service code that caused the failures

1.  We had a code path that would cause large chunks of contiguous memory to be loaded. This caused memory spikes and Garbage collection gen 2 collections. This code is now using streaming instead and does not cause memory pressure.
2.  We had another code path that invoked a lock, it was supposed to be called mostly at startup or from a background task but we found that it is being called from some request path, creating thread contention and major down time.

On Friday June 5th we deployed a change that eliminated the top issue, and we are actively working on the second issue. With only the first fix in place we don't see any further major issues in the service.

We are currently monitoring the service and finding the next level items to be fixed.

#### Api.NuGet.org improvements

We identified that about 60% of the search queries on the site can be cached pretty trivially and reduce the stress both on the WebSite and the search service. These queries are variants of the default empty query used to list the top downloaded packages. Since the data returned from this query hardly changes, this was a quick and easy win.

We are in the process of gradually deploying caching for this set of queries. We expect this to improve responsiveness of both the website and Visual Studio.

### What's next

We want to share our service prioritization list with you in order to be more transparent about our process. We prioritize our services support in this order:

1.  Downloadable packages - Package restore, Package Install & Update from command line
2.  Searchable index
3.  Up to date search index & Package upload
4.  Package download statistics

We have brought the first 3 into a reasonable state, but we are focusing our efforts there, while download statistics is not being worked on until we feel the top three are in a good enough state.

We are tracking our gallery items on <https://GitHub.com/NuGet/NuGetGallery/issues>

Deployments are tracked by milestones, the next one is <https://GitHub.com/NuGet/NuGetGallery/milestones/S2>

We deploy daily as necessary, but bigger changes are typically deployed early on Friday at the end of a milestone so that disruptions can still be dealt with, but the stress on the server is lower (and less developers are at work).

### Summary

We are taking NuGet.org uptime very seriously, we are working at making it better one bit at a time. We appreciate all the feedback we are getting from you, good or bad.

Please reach out to us in the following avenues

For server issues - <https://GitHub.com/NuGet/nugetgallery/issues>

For bugs - <https://GitHub.com/NuGet/home/issues>

For all other issues - <support@nuget.org>

We listen to Twitter [@nuget][3], typically gets fast to us, just note it is not our official support line and the developer on call is not always on Twitter.

Feel free to reach out to me personally as well [@yigalatz][4]

 [1]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/Architecture.png
 [2]: http://www.nssm.cc/
 [3]: https://twitter.com/nuget
 [4]: https://twitter.com/yigalatz