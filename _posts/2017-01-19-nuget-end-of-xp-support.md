---
ID: 119
post_title: 'NuGet &#8211; Ending Windows XP support'
author: Karan Nandwani
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-end-of-xp-support/
published: true
post_date: 2017-01-19 00:00:00
---
At NuGet, we are constantly improving our security. One of the steps we are taking is to move our HTTPS end points to meet industry standards for algorithms and protocols. This means that connecting to nuget.org services from machines that don’t support modern cipher algorithms will no longer be supported (such as TLS 1.0 support in Windows XP). [Windows XP reached end of OS support on April 8, 2014][1], and does not meet this minimum requirement. Therefore, **we plan to end NuGet support for Windows XP on April 8, 2017**.

After support is discontinued, you will no longer be able to connect to nuget.org services from any Windows XP machines. Examples of scenarios that will no longer work include browsing the nuget.org web site, pushing packages using Nuget.exe or using the NuGet package manager inside Visual Studio. To continue using nuget.org services, you need to move to an OS that supports more modern cipher algorithms (such as Windows 10).

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. If you would like to share your pain points, and your current or future needs, hit us up at <feedback@nuget.org>. You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][2].

 [1]: https://support.microsoft.com/en-us/kb/2934207
 [2]: https://github.com/Nuget/Home