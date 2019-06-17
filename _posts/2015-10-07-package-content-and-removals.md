---
ID: 93
post_title: Package Content and Removals
author: Jeffrey Fritz
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/package-content-and-removals/
published: true
post_date: 2015-10-07 00:00:00
---
The NuGet community is awesome; involved and eager to build software to make it easy for use in bigger projects that the components and libraries that they themselves are building. Every now and again, community members can get a little too eager. This post is going to address a recent issue that was presented to the NuGet.org administrators.

In the [terms of use][1] for the NuGet.org service: "*You may not upload or transmit any material that infringes or misappropriates any person's copyright, patent, trademark, or trade secret, or disclose via the Website any information the disclosure of which would constitute a violation of a confidentiality obligation on your part.*" 

This is a legal disclaimer to protect both package and software authors from unauthorized copying and distribution. Vendors who sell their software or provide paid customer support take special care with how their product is distributed, and many vendors who work with NuGet.org want to ensure that a proper quality package that they maintain is available for their customers. They also want to prevent their customers from finding and using packages with similar names and branding that could be confused as their own.

A number of packages were identified by [Oracle][2] that contained their software or were constructed in a way to appear as though Oracle produced the packages. In an effort to provide the highest level of service for their customers, and to prevent Oracle customers from acquiring software that was not produced by Oracle, we have granted their request to remove these packages from the NuGet.org service. If you are looking for the official Oracle software, they ask that you please download the [Oracle.ManagedDataAccess][3] package or the [Oracle.ManagedDataAccess.EntityFramework][4] package to work with the Oracle database using Entity Framework.

 [1]: https://www.nuget.org/policies/Terms
 [2]: http://www.oracle.com
 [3]: https://www.nuget.org/packages/Oracle.ManagedDataAccess/
 [4]: https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/