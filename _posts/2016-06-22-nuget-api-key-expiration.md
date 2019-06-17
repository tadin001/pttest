---
ID: 110
post_title: NuGet API key expiration
author: Maarten Balliauw
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-api-key-expiration/
published: true
post_date: 2016-06-22 00:00:00
---
**Update 6/22 (2:15 P.M PST): We have a lot of feedback coming in from the community on this topic. This change *will not have any impact for another 90 days at the minimum*. We are reviewing your feedback and will discuss further how to achieve our goal of improved security of NuGet.org. We will have an update within the next 45 days. To continue the dicussion, we have openend a new GitHub issue [here][1].** 

**EDIT** - Updates on this feature is available here - [Changes to Expiring API Keys][2]

NuGet.org is growing blazingly fast. The past couple of years have seen a tremendous growth in the usage of packages from the NuGet gallery. It's important to us and the community that package consumers are able to trust and use the packages that are uploaded to NuGet.org. Making your API key on NuGet.org more secure is one more step in that direction.

When publishing packages to [NuGet.org][3] from the command line, for example using `nuget.exe`, you need an API key so that we can authenticate the publish operation on the server side. Starting today, this API key will have to be refreshed at least every 90 days. We are introducing API key expiration as a means to keep your API key on NuGet.org shorter-lived.

## What does this mean for me?

Today, no action is required. To avoid a big rush when the API keys first expire, we have set the expiration for your current API key to a value between 90 and 110 days.

When the API key expires, it can no longer be used for publishing packages to NuGet.org. At this point, your API key will have to be regenerated from your [account page][4].

## How do I know when my API key expires?

From [your account][4], you can consult the date and time when your current API key will expire. When approaching the expiration date, we will send one e-mail informing you about the fact your API key is about to expire.

![NuGet account page - credentials section][5]

We are also working on an update of the NuGet command line to display a warning when your API key is about to expire:

![NuGet client warning when API key is about to expire][6]

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. If you would like share your pain points and your current and future needs, use the [calendly link][7] to set up a quick 15-30 min call with us. If you would like to send us an email instead, hit us up at <feedback@nuget.org>.

You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][8].

 [1]: https://github.com/NuGet/NuGetGallery/issues/3092
 [2]: http://blog.nuget.org/20160825/Changes-to-Expiring-API-Keys.html
 [3]: https://www.nuget.org
 [4]: https://www.nuget.org/account
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/account-page.png
 [6]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/api-key-about-to-expire.png
 [7]: https://calendly.com/harikm/nuget-feedback
 [8]: https://github.com/Nuget/Home