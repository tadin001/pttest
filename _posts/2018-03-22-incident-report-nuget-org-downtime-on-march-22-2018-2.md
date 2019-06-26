---
ID: 226223
post_title: 'Incident report &#8211; NuGet.org downtime on March 22, 2018'
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/incident-report-nuget-org-downtime-on-march-22-2018-2/
published: true
post_date: 2018-03-22 00:00:00
---
We did this blog post to [report about the incident that happened on March 22, 2018][1]. In the last couple of days we digged deeper into the incident. Here is the summary of our findings and proposed next steps.

### Customer Impact

NuGet.org website and V2 APIs were unavailable for 2 hours on March 22, 2018 between 8:45AM - 11:30AM UTC. More than 1.5 million requests failed.

### What Happened?

On March 22nd, a certificate used internally for authentication with Key Vault expired. It was rotated on all components except for a single Search service. This triggered a chain reaction that made the Gallery service unavailable, resulting in continuous failover attempts between the primary and secondary instances. The incident was mitigated by redeployment of the search service with a renewed certificate.

To dive deeper into the analysis of the incident we first need to understand how the Gallery and Search services are integrated.

Our team runs two Search services – *<https://api-v2v3search-0.nuget.org>*, *<https://api-v2v3search-1.nuget.org>* in paired Azure regions. This is done to enable failover in case one of the services is down. The failover logic for search differs in different clients - for example Visual Studio NuGet Client always uses the first search endpoint in the [service index][2], and fails over to the secondary endpoint when it's not available.

The search client in the Gallery service uses the [service index][2] to discover available search endpoints, and employs a [weighted random selection algorithm][3] to load balance between available, healthy endpoints. During the incident, the Search service at *<https://api-v2v3search-0.nuget.org>* started returning HTTP 500 responses. A [bug][4] in the selection algorithm caused an infinite loop and the CPU utilization on Gallery instances spiked to 100%.

![alt text][5] *CPU utilization on Gallery instances during the incident.*

The next stage in the chain reaction was Azure Traffic Manager failover. The Gallery service is deployed in two Azure paired regions - a primary service that is normally used, and a secondary read-only service that is used for disaster recovery. Traffic Manager is used for automatic failover between those regions. However, failovers did not help in this incident as all Gallery deployments were affected by the same [bug][4]. This resulted in the Traffic Manager continuously failing over between the primary and secondary Gallery instances.

### Mitigation

The incident was mitigated by redeployment of the Search service with a renewed certificate. Once Search service was healthy, the Gallery service CPU utilization dropped to normal levels, and Traffic Manager stopped failing over.

### Next Steps

In this incident we identified two main areas for improvement:

1.  Our secret rotation processes - we are working and will continue to work on automation of certificate/secret rotation and introduce monitoring to ensure the expected certificate is in use.
2.  Disaster recovery - we will continue improving NuGet.org's resilience and disaster recovery. We need to run fire drills frequently and make sure the service behaves as expected. The immediate work item is to fix the [bug][4] in the weighted random selection algorithm. The next step would be to introduce a simpler and more reliable integration between Search service and Gallery service by using Traffic Manager for load balancing. Tracked on [GitHub][6].

We apologize for the impact this had on our users. This incident highlighted areas of improvement in our disaster recovery scenarios, and we are committed to delivering the improvements needed to avoid related issues in the future.

 [1]: #initial-update-incident-on-march-22-2018
 [2]: https://docs.microsoft.com/en-us/nuget/api/overview#service-index
 [3]: https://github.com/NuGet/NuGetGallery/blob/master/src/NuGet.Services.Search.Client/Client/RetryingHttpClientWrapper.cs
 [4]: https://github.com/NuGet/NuGetGallery/issues/5703
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/2018-03-27-GalleryCPU.jpg "CPU"
 [6]: https://github.com/NuGet/NuGetGallery/issues/5700