---
ID: 225925
post_title: 'Expected Downtime &#8211; August 22 11pm-12am PDT'
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/expected-downtime-august-22-11pm-12am-pdt/
published: true
post_date: 2012-08-22 00:00:00
---
As mentioned in the [previous post][1], we've been working to address some performance problems on the [NuGet Gallery][2].

The fixes we have in place include some time-consuming database migrations. During our dry run deployments, we found that virtually all requests to the database will time out while these migrations are running. We have decided to take the precaution of shutting the gallery down during these migrations to ensure the best and fastest possible deployment. We are sorry for the inconvenience this will cause.

## Timing

The [nuget.org][2] website is expected to be down from 11pm-12am PDT tonight. During this time, all requests to browse, search, install, or update packages will fail. You will see errors in Visual Studio when you use the Online tab in the Manage NuGet Packages dialog.

## Live Updates

Please follow [@nuget][3], or the [#nuget tag][4] on twitter for live updates. You can also check in on JabbR's [#nuget room][5].

This blog will be updated once the deployment is completed.

## Workarounds

Many users have found Scott Hanselman's tips on [How to access NuGet when NuGet.org is down (or you're on a plane)][6] to be helpful.

## Deployment Process

During this downtime, we will be doing the following:

1.  Creating a pre-migration database backup
2.  Executing the database migrations against the database
3.  Performing a post-migration database backup
4.  Deploying changes to the gallery web application
5.  Verifying the successful deployment within a Staging instance of our Web Role

Once we have verified the successful staging deployment, we will swap the instances over to production. At that time service will resume.

If you're curious about what is getting deployed, feel free to check out the [commit history on the master branch][7] in our GitHub repository at <https://github.com/NuGet/NuGetGallery>. The changes being deployed are from August 21st and 22nd.

## Feature Request

Out of this situation comes a new feature request for the gallery: read-only mode. Ideally, deployments of this type could be handled by putting the gallery into a read-only mode and performing the deployment to a new web role with a copy of the database while the read-only gallery stays online. We will invest in setting up that model to be used in the future.

 [1]: http://blog.nuget.org/20120822/nuget-performance-issues.html
 [2]: http://nuget.org
 [3]: http://twitter.com/nuget
 [4]: https://twitter.com/#!/search/%23nuget
 [5]: http://jabbr.net/#/rooms/nuget
 [6]: http://www.hanselman.com/blog/HowToAccessNuGetWhenNuGetorgIsDownOrYoureOnAPlane.aspx
 [7]: https://github.com/NuGet/NuGetGallery/commits/master