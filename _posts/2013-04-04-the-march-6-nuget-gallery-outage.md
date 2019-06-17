---
ID: 152
post_title: The March 6th NuGet Gallery Outage
author: Tim Lovell-Smith
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/the-march-6-nuget-gallery-outage/
published: true
post_date: 2013-04-04 00:00:00
---
On March 6th 2013, nuget.org's package download was broken for one and a half hours. Some outages involve factors beyond our control; this time it was very much our own fault and we would like to apologize. We also want to openly discuss the outage and reassure you that we are working on preventing reoccurences.

## Timeline of events

The outage was triggered by the NuGet Gallery crew performing a regular planned upgrade to nuget.org. We had successfully completed the previous production upgrade as recently as 2 weeks prior (in fact we are now shipping code to nuget.org on regular 2-3 weeks intervals). The timeline of the outage was something like this:

2:00 - We assembled to do deployment to production. Following our existing process, we backed up the production database, reviewed the state of our master branch in git, and checked that our preproduction environment still looked healthy.

2:25 - We published the new code to the Production Web Role in Azure staging slots. Because we are using Entity Framework migrations at App Startup time, and because the VMs in staging slots run against the same database as the (still live) VMs in production slots, we did not feel safe testing these new role instances prior to doing the VIP swap which brings the new code live.

2:30 - We do the VIP swap, and the new code is now running nuget.org. We start doing sanity tests of production, and checking the error logs in case we will see evidence of any problems there.

A few minutes later, we started receiving tweets and emails stating that package downloads were failing. We acknowledged the issue on twitter. [Related tweet][1]. We really appreciate your tweets at [@nuget][2] when you encounter site issues!

2:35 - We realized gallery is redirecting package download requests to package URLs that don't exist. We also realized why. The problem is in our production environment package blobs have not been updated to the new naming convention that the new code we just deployed requires.

We started running the batch job, which already exists, and has been used on preproduction environments. This will update the production environment package blobs and fix package downloads so the world can get their nupkgs. We estimated that this batch job will take 40-60 minutes to run. 

While we monitored the progress of the batch job, we were simultaneously second guessing ourselves and trying to think of a safe way to get the site back up faster. Unfortunately VIP swapping back to the old production VMs is not a viable solution, the reason again being the way we are using Entity Framework migrations at App_Start which means our old production code is probably not going to be happy with a database which is in a state it does not understand.

2:40 - We got worried. The batch job seemed to be working very slowly, or not at all.

3:05 - We realized the batch job itself is broken. We fixed the code, and restarted the whole batch job. Again we monitored progress of the batch job. This time, eventually, we could verify it was working properly. We thought the batch job would take about an hour to complete, but it ended up taking 85 minutes. Packages gradually became available for download during this time, so some people may have seen their problems resolved sooner. [Related tweet][3].

4:30 - We verified that the batch job had completed and all packages are back up and working. [Related tweet][4].

## The Problematic Code Changes

Here's what motivated the change causing all these problems:

1.  Package IDs in URLs on the nuget gallery are case insensitive.
2.  Azure Blob Storage, which we use for holding all our nupkg files for download, is case sensitive.
3.  Package IDs have their 'canonical' casing preserved in the NuGet database, and is used to build the filename that retrieves the blob from blob storage.
4.  The canonical casing is determined the *first* time the package is published and it was immutable. Many package authors have wished to change the casing, but we couldn't support it. This meant that any given version of a package might claim a package ID that is different from the canonical ID.

The consequence of 3. and 4. is that in order to service a package restore download request, which contains the package ID in casing which may be different from blob storage, nuget.org must query the database to retrieve the canonical ID before we could service the download request. As a further consequence package download stops working any time our database goes down.

### Avoiding the database for package downloads

We updated the code that handles package download requests to look for packageID.ToLowerCase(); This means we no longer have to hit the database to retrieve package ID's canonically cased version, we can just use ToLowerCase() on the package ID sent in the download request.

*We apologize for the irony! We intended package downloads to become more robust but actually ended up breaking package downloads.*

## The Postmortem - What Went Wrong

We held a meeting the day after the outage, where we did a break-down starting with a summary of everything that went wrong, during the upgrade and outage. Here's *what* went wrong.

1.  The outage happened. We published a problem code to the VMs without the required associated data because we are missing a step in the deployment.
2.  We tested the production environment after we published the code. This is when we discovered the issue. The time spent testing and discovering the issue contributes to the total delay between the problem occurring and us fixing the problem. 
3.  We were not confident we could safely revert the VIP swap to unpublish from production. If we could have done it then package downloads would have started working immediately, and it would have been probably a 5 minute downtime, not a 1.5 hour downtime. So our inability to safely revert is a problem.
4.  The batch job for fixing the problem failed the first time. It contributed to the outage duration significantly the time to realize the script was broken and fix it again was 20 minutes or more.
5.  And finally, the missed step of running the fixed batch job to recover the gallery just takes a long time because it's doing a lot of work.

In a deeper analysis of *how* the problem code went into production in the first place without knowing that it would cause a problem, we found out that

*   The script had been run against our QA environment (called Preview) *by the developer*, without involving QA in testing the script.
*   The script had not been run against the preproduction environment (called Staging) ever. One reason for that is that Preview and Staging share the same Azure blob storage account, so it would have made no difference to observable behavior during testing.
*   Even though the need for running the script had been communicated verbally to the crew, because it was weeks earlier and because the step was not part of the deployment process for Preview and Staging, the step was completely forgotten about by the time it came to do the actual production deployment.
*   QA team had noticed issues downloading from Preview and Staging environments but not realize they were important because it was *normal for download to be broken in these environments*. The reason is these environments don't have the full set of the production blob data in their own storage account.

## Conclusions - What We Plan to Change

Plans for preventing and mitigating future nuget.org downtime caused by the NuGet gallery crew's upgrades to nuget.org.

*   Ensure our deployment checklist of ALL steps required for deployment is updated alongside any code changes that require special deployment steps.

*   Validate production deployments using automated tests immediately after going live. We want to be able to detect the problems faster than it can be tweeted to us.

*   Expect problems during upgrades, and have a clear recovery plan in place for every deployment. The plan details how to quickly and safely recover when the deployment fails.

Ideally our recovery plan should always be VIP swap. We are currently experimenting with changing our use of Entity Framework to a model which will not depend on running migrations during the deployment itself.

A second recovery plan is database restore *plus* VIP swap. We would first set the Gallery to Read-Only mode (this is new) during the deployment, which means you will be unable to create accounts and upload packages while the deployment occurs.

*   *Test* the recovery plan in preproduction before doing the production deployment. (Confidence in a plan that has been dreamed up under pressure and not been tested is low!)

*   We need to make the QA and Preproduction environments squeaky clean and as reliable as production. We should never have issues that occur there that get ignored and thereby slip into production.

*   Stopping Preview and Staging from sharing the blob storage account will help us ensure we have done clean practice deployments.

*   Enhance our service monitoring to give us better feedback on when and how package download gets broken.

 [1]: https://twitter.com/nuget/status/309432341057634305
 [2]: http://twitter.com/nuget
 [3]: https://twitter.com/nuget/status/309439911881240577
 [4]: https://twitter.com/nuget/status/309461232967110656