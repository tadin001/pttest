---
ID: 226009
post_title: Monitoring nuget.org
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/monitoring-nuget-org/
published: true
post_date: 2014-06-26 00:00:00
---
We've recently found ourselves answering the same question repeatedly: **How do you monitor nuget.org?** If you too are interested, here are the details.

## Monitoring Approaches

We have two approaches for monitoring the gallery:

1.  From the outside, checking HTTP endpoints that indicate public-facing service interruptions
2.  From the inside, monitoring error logs and diagnostic data that indicate potential interruptions

### External Monitoring

For our external monitoring, we use [Pingdom][1]. You can see our public Pingdom status page at <http://status.nuget.org>. If you ever experience trouble connecting to [nuget.org][2] and you're wondering, "Is NuGet down or is it just me?" you can visit <http://status.nuget.org> to find out. If you see all green, then it might just be you.

#### Public Status Page

Here are the checks shown on the status page:

TABLE.pingdomcheck { border-collapse: collapse; } TABLE.pingdomcheck THEAD TR TH, TABLE.pingdomcheck TBODY TR TD { border: 1px solid gray; padding: 0.5em; } TABLE.pingdomcheck TBODY TR TD:nth-child(2), TABLE.pingdomcheck TBODY TR TD:nth-child(3) { text-align: center; } 

<table class="pingdomcheck">
  <thead>
    <tr>
      <th>
        Check
      </th>
      
      <th>
        Interval
      </th>
      
      <th>
        Notify when down
      </th>
      
      <th>
        Purpose
      </th>
    </tr>
  </thead>
  
  <tbody>
    <tr>
      <td>
        DNS: nuget.org (root domain)
      </td>
      
      <td>
        1 minute
      </td>
      
      <td>
        5 minutes
      </td>
      
      <td>
        Verify that the DNS provider we use is returning the expected IP address for our root domain's A record. We redirect from nuget.org to www.nuget.org and use a CNAME record to route to Azure's Traffic Manager.
      </td>
    </tr>
    
    <tr>
      <td>
        <a target="pingdomcheck" href="https://www.nuget.org/api/v2/Search()?$filter=IsAbsoluteLatestVersion&$orderby=DownloadCount%20desc,Id&$skip=0&$top=30&searchTerm=''&targetFramework='net40'&includePrerelease=true" rel="noopener noreferrer">feed (top 30 by downloads)</a>
      </td>
      
      <td>
        1 minute
      </td>
      
      <td>
        3 minutes
      </td>
      
      <td>
        This is the URL that is hit when you open the 'Manage NuGet Packages' dialog in Visual Studio.
      </td>
    </tr>
    
    <tr>
      <td>
        <a target="pingdomcheck" href="https://www.nuget.org" rel="noopener noreferrer">home page</a>
      </td>
      
      <td>
        1 minute
      </td>
      
      <td>
        3 minutes
      </td>
      
      <td>
        Checks the status of the web site itself, and its ability to simply serve HTML. There are no external resources required to serve the home page.
      </td>
    </tr>
    
    <tr>
      <td>
        <a target="pingdomcheck" href="https://www.nuget.org/api/v2/package/NuGet.GalleryUptime/1.0.0" rel="noopener noreferrer">package restore (download)</a>
      </td>
      
      <td>
        1 minute
      </td>
      
      <td>
        3 minutes
      </td>
      
      <td>
        We use an unlisted NuGet.GalleryUptime package to verify that packages can be downloaded by the package restore process. This operation records package download statistics and then serves the actual nupkg file. If this check is down, builds using package restore will fail.
      </td>
    </tr>
    
    <tr>
      <td>
        <a target="pingdomcheck" href="https://www.nuget.org/api/v2/Packages(Id='NuGet.GalleryUptime',Version='1.0.0')" rel="noopener noreferrer">package restore (lookup)</a>
      </td>
      
      <td>
        1 minute
      </td>
      
      <td>
        3 minutes
      </td>
      
      <td>
        Before NuGet can download a package through the 'package restore (download)' URL, it must first query the API for the package to get its metadata that includes that download URL. This lookup ends up hitting our packages index and serving the results through the API. If this check is down, builds using package restore will fail.
      </td>
    </tr>
    
    <tr>
      <td>
        <a target="pingdomcheck" href="https://www.nuget.org/packages?page=1000&prerelease=true" rel="noopener noreferrer">packages page (search)</a>
      </td>
      
      <td>
        1 minute
      </td>
      
      <td>
        3 minutes
      </td>
      
      <td>
        This page hits the website's search feature, loading results 19,981-20,000 for an empty search. This verifies that our search index is intact and complete. The check not only verifies the 200 OK response, but it also checks for the string 'Displaying results 19981 - 20000' in the response.
      </td>
    </tr>
    
    <tr>
      <td>
        <a target="pingdomcheck" href="https://api-search-0.nuget.org/search/query" rel="noopener noreferrer">search service (query)</a>
      </td>
      
      <td>
        1 minute
      </td>
      
      <td>
        3 minutes
      </td>
      
      <td>
        The Search service runs as a separate deployment--the website makes HTTP requests to it for the website's search feature. In addition to monitoring the website's ability to use the search, we also monitor the Search service directly. We check for both a 200 OK response as well as as string of '"data":[{'' in the response to ensure there was data returned.
      </td>
    </tr>
  </tbody>
</table>

#### Other Checks

We also have some other checks that don't need to be on the public status page.

<table class="pingdomcheck">
  <thead>
    <tr>
      <th>
        Check
      </th>
      
      <th>
        Interval
      </th>
      
      <th>
        Notify when down
      </th>
      
      <th>
        Purpose
      </th>
    </tr>
  </thead>
  
  <tbody>
    <tr>
      <td>
        <a target="pingdomcheck" href="https://www-0.nuget.org/api/v2/Packages" rel="noopener noreferrer">DC0 - feed (raw packages list)</a>
      </td>
      
      <td>
        1 minute
      </td>
      
      <td>
        3 minutes
      </td>
      
      <td>
        This is the raw packages feed from our primary data center. This check tells us the feed's health in that primary data center, where this might fail despite the feed being available from our secondary data center.
      </td>
    </tr>
    
    <tr>
      <td>
        <a target="pingdomcheck" href="https://www-1.nuget.org/api/v2/Packages" rel="noopener noreferrer">DC1 - feed (raw packages list)</a>
      </td>
      
      <td>
        1 minute
      </td>
      
      <td>
        3 minutes
      </td>
      
      <td>
        This is the raw packages feed from our secondary/fail-over data center. The secondary data center runs in readonly mode and it's generally behind the primary data center by about 30 minutes. Traffic manager fails over to this data center if the primary data center is unavailable.
      </td>
    </tr>
  </tbody>
</table>

In addition to these, we also monitor the [documentation site][3] and our [build server][4].

#### Alerting

When Pingdom detects downtime, it sends a message to [PagerDuty][5] where we manage our on-call schedule and alerting rules. Our notification rules follow a pattern until the incident is acknowledged.

1.  Immediately send email
2.  Immediately send a text message
3.  After 5 minutes, send another text message
4.  After 10 minutes, make a voice call to the cell phone
5.  After 11 minutes, make a voice call to the office phone
6.  After 12 minutes, make a voice call to the home phone
7.  After 15 minutes, escalate the incident to the backup on-call person and run the same process
8.  After 30 minutes, alert half of the team through text messages
9.  After 45 minutes, alert the other half of the team through text messages

We've really enjoyed the flexible scheduling and alerting that PagerDuty offers. We have good peace of mind that if an incident does occur, we'll be alerted and woken up if needed.

### Internal Monitoring

I'll go into less detail about our internal monitoring--it's not nearly as exciting. But at a high-level, we have a few moving pieces that we utilize for keeping tabs on the service's health that is more subtle than the UP/DOWN observations that can be made by the external monitoring.

#### Dashboard

We have a dashboard website that we can look at any time we need to investigate a service incident. Sometimes we have this dashboard projected in our team room too. This is a separate project on GitHub, under the [NuGet.Services.Dashboard][6] repository.

The dashboard shows us elements for:

1.  All of the Pingdom checks
2.  Our Traffic Manager endpoint statuses
3.  Our Web Role and Worker Role instance statuses
4.  How many Web Role instances are currently running
5.  Requests per hour
6.  The current lag between the database and the search index
7.  The number of new users today
8.  The number of new packages today
9.  Trendline of our requests per hour
10. Trendline of our error per hour
11. Trendlines for response times on all of the Pingdom checks
12. Trendline of the database requests
13. Trendline of the database connections

It also has screens for drilling into a lot of the information captured on that main dashboard screen, as well as some other detailed diagnostic information, including:

1.  Database index fragmentation
2.  Database sizes
3.  Error logs and trends
4.  Backend job statuses and logs

#### Diagnostic Alerts

As part of the Dashboard project, we also have some backend monitoring that can send alerts. For this, we use an Azure Virtual Machine that we configure Windows Scheduled Tasks onto. We use scheduled tasks to keep our service dependencies at a minimum for this monitoring. The tasks are configured through deployment scripts so there's no manual schedule task setup to perform.

We send low priority alerts out (that don't text/call us) for data that starts to look unhealthy. This is done by connecting to PagerDuty's API and issuing a Sev-2 incident. PagerDuty then sends the team an email. Here are some examples of the low priority alerts:

1.  Number of database connections/requests crossing configured thresholds
2.  Incoming website traffic rate increases dramatically
3.  A surge of noteworthy exceptions being thrown
4.  A lag between the database and search index beyond a configured threshold
5.  Poor health of individual server instances or a spike in the instance count
6.  Database size increases beyond a configured percentage of our size limit
7.  Severe database index fragmentation

### Responding to Incidents

When we do get an alert for an incident, the first thing we do is verify any service interruption. Then we will acknowledge the interruption and begin investigating.

#### Acknowledgement

Since we're using PagerDuty, we need to acknowledge the incident there so that it doesn't escalate to the next person on the team. Then we usually get onto Twitter and post an acknowledgement from the [@nuget][7] account. To be honest, sometimes we're better about doing that than others, but it's something we're improving.

If you're experiencing trouble connecting to nuget.org, here's what we recommend:

1.  Check our Pingdom public status page at <http://status.nuget.org>
2.  Check our Twitter timeline at <http://twitter.com/nuget>

If you think NuGet is down and we haven't acknowledged it yet, feel free to tweet at us by either mentioning [@nuget][7] or using the [#nuget][8] hashtag.

#### Investigating

Once an incident is acknowledged, we jump into root cause analysis and try to find the component of the system that is failing. Sometimes it's something on our side, sometimes it's a service we consume from Azure. If the issue is purely on our side, we continue investigating and loop in other members of the team as needed. If the issue is due to an Azure service interruption, we check Azure's status page and file tickets with them when needed. During the investigation, we again do our best to post updates to Twitter.

#### Post-Mortem

We always complete a post-mortem after an incident. We share this with the entire team as well as our management at Microsoft. When it makes sense to do so, we also blog about the service interruption. Here are a couple of outage blog posts:

*   [March 6, 2013][9]
*   [April 2-3, 2014][10]

### Measuring Uptime

Every month, we report our uptime to our management. To do so, we look at our Pingdom uptime report for the month and pick the *lowest* number from these three checks:

1.  Feed (top 30 by downloads) - This represents the availability of consuming the feed in Visual Studio
2.  Package Restore (lookup) - This is the package lookup that occurs to get a package's download URL
3.  Package Restore (download) - This is the act of downloading a package after looking it up

Together those numbers give us a good representation of the perceived availability of [nuget.org][2] as they indicate if users can find and download packages.

Here are the overall numbers we've had so far this calendar year:

1.  June 2014 (1st-26th): 99.99%
2.  May 2014: 99.78%
3.  April 2014: 99.80%
4.  March 2014: 99.98%
5.  February 2014: 99.99%
6.  January 2014: 99.53%

As we've been [building NuGet 3.x][11], we've been doing so with the following scenario-based availability goals:

1.  Package Restore: 99.999%
2.  Package Discovery and Fresh Install: 99.99%
3.  Package Upload and Website: 99.9%

We definitely have our work cut out for us, but we're making good progress on the re-architecture that we believe will make those goals attainable. Availability of NuGet is our team's top priority and we're making both server-side and client-side changes to make the service more reliable.

 [1]: http://www.pingdom.com
 [2]: http://www.nuget.org
 [3]: http://docs.nuget.org
 [4]: http://build.nuget.org
 [5]: http://www.pagerduty.com
 [6]: https://github.com/NuGet/NuGet.Services.Dashboard
 [7]: http://twitter.com/nuget
 [8]: https://twitter.com/search?q=%23nuget
 [9]: http://blog.nuget.org/20130404/the-march-6-nuget-gallery-outage.html
 [10]: http://blog.nuget.org/20140403/nuget-2.8.1-april-2nd-downtime.html
 [11]: http://blog.nuget.org/20140424/building-nuget-3.x.html