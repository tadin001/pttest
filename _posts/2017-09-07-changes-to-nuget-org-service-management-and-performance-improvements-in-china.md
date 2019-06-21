---
ID: 1004
post_title: >
  Changes to NuGet.org service management,
  and performance improvements in China
author: Serhii Rohatin
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/changes-to-nuget-org-service-management-and-performance-improvements-in-china/
published: true
post_date: 2017-09-07 00:00:00
---
NuGet.org, the package manager for .NET, was purpose-built as a global service with high scale performance regardless of the developer’s location. We are finding that this is not always the case, particularly for developers accessing the service from China, which is the second largest region for .NET developers. They frequently face higher download times resulting in poor restore performance and service outages.

We have been actively investigating solutions to this problem. Based on our tests, developers in China should see up to 30x improvement in package download times and on average, a 10x improvement in overall package restore time. The solution requires us to co-locate our blob storage in China and use a local CDN provider to serve package requests. As a result, in order to implement this solution and better serve the global .NET community, the .NET Foundation will transfer ownership of the NuGet.org **service** to Microsoft.

The .NET Foundation’s mission is to support open source .NET projects, and will continue to do so. However, as the NuGet service grows, Microsoft is in a better position to run such global services from an infrastructure and business perspective. Microsoft’s current footprint allows us to run the service in China – something that we cannot do under the .NET Foundation’s current charter. This will allow the .NET Foundation to focus its efforts on its core mission; supporting .NET open source projects.

We remain fully committed developing NuGet in the open, and the NuGet code base will remain a .NET Foundation project. This will enable us to continue to provide an agile and reliable global service, at the same time as ensuring a healthy ecosystem.

#### Q. What does this mean for me, as a NuGet.org package author or consumer?

There are no changes to the way you publish and consume packages from NuGet.org. The Terms of Service and Privacy Statement will be updated to reflect the change in ownership, how Microsoft will process the data collected, and manage copyright and trademark infringement notices.

#### Q. What about the NuGet project on GitHub? Does the move mean that NuGet is no longer open source?

The NuGet open source codebase (both the NuGet gallery and the NuGet client) will remain open source and continue as .NET Foundation supported projects under the same terms as before. We remain fully committed to ensuring a healthy and open ecosystem around NuGet. The NuGet.org service is an implementation of the NuGet gallery open source codebase and only this piece is moving to be owned by Microsoft.

#### Q. Did the NuGet team explore other alternatives?

We explored several options, including a parallel instance of the NuGet.org feed with a different URL (i.e., a mirror of NuGet.org). However, this option would not work for developers in China. Additionally, it does not provide a seamless experience, requiring developers to re-configure IDE, build machines, etc. depending upon location, to publish and consume packages from NuGet.org.

The .NET Foundation and Microsoft want NuGet to meet the evolving needs of our community. If we can do anything to improve your NuGet experience, please send us an email at [feedback@nuget.org][1].

 [1]: mailto:feedback@nuget.org&subject=Feedback%20on%20changes%20to%20Nuget.org%20service%20management