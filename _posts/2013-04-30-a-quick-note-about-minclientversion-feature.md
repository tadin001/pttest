---
ID: 159
post_title: >
  A quick note about the new
  MinClientVersion attribute
author: Luan Nguyen
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/a-quick-note-about-minclientversion-feature/
published: true
post_date: 2013-04-30 00:00:00
---
In the NuGet 2.5 release, we added the [MinClientVersion][1] property to the package manifest schema to allow for a package to demand the minimum version of the NuGet client that can install it.

Because this feature was added in version 2.5, older versions of NuGet won't be able to recognize it. Therefore, older clients will *always* refuse to install packages with the MinClientVersion property set, even if you set it to a value smaller than the version of the client.

For example, if you set MinClientVersion to 1.8, and the user tries to install your package with NuGet 2.2, the installation will fail even though 1.8 < 2.2. The user will see the following error message:

**The schema version of 'MyPackage' is incompatible with version 2.2.1.0 of NuGet. Please upgrade NuGet to the latest version from <http://go.microsoft.com/fwlink/?LinkId=213942>.**

In sum, this feature will grow in its utility over future NuGet iterations, starting with version 2.6

-Luan

 [1]: http://docs.nuget.org/docs/release-notes/nuget-2.5##Add_a_%27Minimum_NuGet_Version%27_property_to_packages