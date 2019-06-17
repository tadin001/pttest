---
ID: 47
post_title: The NuGet Gallery Outage on March 9th
author: The NuGet Team
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/the-nuget-gallery-outage-on-march-9/
published: true
post_date: 2012-03-14 00:00:00
---
The [NuGet Gallery][1] was unavailable on March 9th from approximately midnight until 7:52 A.M., nearly eight hours. During this time all HTTP requests to the gallery website failed. In addition to affecting website users, the outage affected all NuGet client programs (such as the Visual Studio extension and nuget.exe) and other software that relies on the NuGet Gallery's feed and API.

To start, we apologize for the disruption and inconvenience that this incident caused. We know that many of our customers were affected by the outage and we want to be transparent about what happened, what issues we found, how we plan to address these issues, and how we are learning from the incident to prevent a similar occurrence in the future. 

The outage was caused by a NuGet Gallery application error, compounded by some shortcomings in our monitoring system and incident-response process for the NuGet gallery. 

## What Happened Exactly

Windows Azure rebooted the NuGet Gallery servers at midnight after performing a scheduled OS upgrade. After this reboot, the gallery's web roles failed to initialize due to a previously unknown bug in a script we wrote that we execute in order to enable HTTP compression for the gallery's feed. Because the web roles could not initialize, the website became unresponsive. Our monitoring service didn’t send any alerts about this situation. However, the team became aware of the problem around 7:30 A.M. and we were able to restore service by deleting the Azure deployment and creating a new one (bypassing the scripts's bug, although we didn't know about the bug yet).

## How We Failed

As with most system failures of this severity, there was more than just one failure.

### Deployment Testing

We continually and automatically test the process of deploying new versions of the gallery to Windows Azure by using a preview site. However, we missed testing reboot scenarios, and this masked a bug in our compression script.

### Monitoring Service

As a result of this outage, we learned that our monitoring service had configuration issues that we did not know about. We understand that it's crucially important to continually test all of the systems that keep our website running, including things like monitoring, backup, and restore. We test our backup and restore continually using our preview site, but as we've learned, we were not testing monitoring sufficiently.

### Inadequate Incident-Response Process

When the outage occurred, we didn't have a robust enough incident-response process, with the result that the response time for this outage that was simply not fast enough. There has been no dedicated operations team for the NuGet Gallery, and the development team has been assuming all responsibility for operational activities. 

### Bad Assumptions

One of the reasons we had not formalized our incident response process or had people on call was that the NuGet gallery has been very reliable. (As we've now been reminded, this is no excuse for lack of proper planning.) 

We have since also learned that two assumptions about how Windows Azure deployments work contributed to this issue. First, we assumed that we would be notified that an OS upgrade requiring a reboot had been scheduled. We've learned that Windows Azure does not currently send notifications for scheduled OS upgrades, whether a reboot is required or not. We've recommended to the Azure team that they start sending notifications in these cases.

Second, we assumed that OS upgrades (or other maintenance) would be performed on one instance at a time within a deployment, and that if an upgrade failed, Azure would not then try to upgrade the remaining instances, effectively causing the entire deployment to fail. This one-at-a-time approach is how Azure handles customer-initiated upgrades. We've recommended to the Azure team that they consider changing the Azure-initiated upgrade process accordingly, although we understand there are a lot challenges in doing this.

## What We're Changing

All applications can fail, which is why it's crucial to have a proven, tested system in place to monitor the health of an application, and a proven, tested process in place to respond to incidents. To that end, we will:

*   Regularly test our monitoring service (for example, by periodically making the preview site unavailable). 
*   Create a better incident-response process. 
*   Expand the team of people who understand Azure deployment and know how to troubleshoot failures. 
*   Always have at least one person on call to respond to incidents. 

We will also regularly test server reboots after a deployment, using our preview site.

While we can't guarantee that future incidents won't happen (in fact, we know they will), we *can* guarantee that our response to any future incidents will be much more timely.

 [1]: http://nuget.org