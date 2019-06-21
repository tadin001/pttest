---
ID: 226127
post_title: >
  NuGet.org package publishing workflow
  – behind the scenes
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-org-package-publishing-workflow-behind-the-scenes/
published: true
post_date: 2018-02-01 00:00:00
---
In December 2017, we changed the NuGet.org backend publishing pipeline to introduce a set of validation steps for submitted packages. Our goal is to maintain the same level of experience in terms of the time and effort it would take to publish a package and have it available for download. However, these new validation steps caused a few incidents that resulted in significant delays in the publishing workflow. We wanted to share the reasons why the experience has changed over the last few months and the continuous improvements we have been making since then, based on our learnings.

## Background

When a package is submitted to NuGet.org, it undergoes a set of validations, including a malware scan. Before, these were asynchronous validations (i.e. a package may be published and available for download before the validations complete). If the validations failed, we would take down the package potentially breaking the consumers of the package.

Beginning late December, we changed the package publishing pipeline such that the packages are published after all validations have completed. Currently this step introduces an additional delay of around 10 minutes for the majority of packages that get uploaded. However, there were a few incidents that caused a delay of up to a day or more. The statistics around the package validation times are shared below (last 30 days):

![image][1]

## Learnings so far

When we deployed the new validation pipeline, we kept the same package throughput capacity as before. This capacity worked for us earlier as these validations were asynchronous, but now with the synchronous validation pipeline, this was no longer sufficient. We had an incident where our validation pipeline choked due to a large number of simultaneous package submissions. We have since increased our capacity so that similar incidents do not occur in the near future. We will continue to invest in our validation pipeline so that it can handle increasing package submission rates. We are also working towards throttling the number of package submissions per account so that not all package authors are impacted due to one author submitting a large number of packages ([NuGet#4679][2]).

During the weekend of 1/27, we had another incident impacting all package submissions due to an outage in our malware scanning service. In addition, there was a delay in communicating the degraded status on the [NuGet status page][3] and other forums. We have improved our processes to prioritize communication and status updates. We also recognize that the NuGet status page needs improvements such that it immediately reflects the status of degraded services ([NuGet#5369][4]).

## Next steps

We plan to add more validations such as verifying the signature and integrity of [signed packages][5] on submission. We will continue to invest in the performance and monitoring of our validation pipeline. In addition, we will also continue to expand our capacity for simultaneous package submissions. If you see considerable delays in the package submission workflow on NuGet.org, please check for known service degradations at <https://status.nuget.org>. Or, you can always reach us at <support@nuget.org>.

 [1]: https://user-images.githubusercontent.com/14800916/35698720-c487ff76-0742-11e8-83e3-0f3f1fc34e7c.png
 [2]: https://github.com/NuGet/NuGetGallery/issues/4679
 [3]: https://status.nuget.org
 [4]: https://github.com/NuGet/NuGetGallery/issues/5369
 [5]: https://github.com/NuGet/Announcements/issues/6