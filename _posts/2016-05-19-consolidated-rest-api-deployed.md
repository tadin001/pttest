---
ID: 109
post_title: Consolidated REST API deployed
author: Maarten Balliauw
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/consolidated-rest-api-deployed/
published: true
post_date: 2016-05-19 00:00:00
---
A few weeks back, we deployed our consolidated REST API powering NuGet.org and the NuGet client experience in Visual Studio. An invisible change for our users, but a big change for the NuGet team. This consolidated REST API lays the foundation of our future work on the server side of NuGet. In this post, we want to expand a little on the history of the NuGet services and why it matters for us.

## History of our internal REST API

Historically, [NuGet.org][1] used to serve all requests for packages directly from our database. With the number of NuGet users increasing, the number of queries on our database also increased. Around two years ago, the NuGet team built an internal REST API for use by our own services. It would provide package metadata, provide search results, handle download counts and all that - with great results: the database got some breathing room.

For the [beta release of the NuGet 3.0 client][2], we faced a difficult decision: should we expose this REST API to the public, or build a new one? We decided to build a new, public REST API that would be used by our future NuGet clients. This decision was made because of several reasons. First, we wanted to move on from our OData API that was used in previous NuGet clients. Second, the REST API we had in place was very opinionated and tailored to our own services and not well suited for public access by our users.

So we ended up with an internal and an external REST API. When new features were being added in NuGet 3.0 and beyond, we had to implement them in all of our API's - resulting in a lot of duplicate work. Both API's had their own caching layer, which proved quite the challenge in keeping data in sync.

## What we did

A few months ago, we set on an adventure of merging both REST API codebases and refactoring them to use the same caching layer. We cleaned up a lot of redundant code paths and optimized the caching layer. While not the scope of this effort, we also introduced a few minor improvements to several features - for example, we now recognize several commonly used acronyms like "EF" (Entity Framework) or "UWP" (Universal Windows Platform) in our search engine.

We fixed several tokenization bugs and differences between the two services. A large test suite for search was added as well, boosting our confidence in future changes and investment in the search engine.

Some improvements were introduced to the search results, like boosting on exact match to package id. A new logarithmic scale for download counts ranks packages with a high number of downloads *slightly* better - an algorithm which we will be tweaking further.

A lot of work has been done on performance. We profiled the API during load tests to optimize code paths and reduce the API's memory traffic and garbage collector pauses. One notable improvement is the cold start of the application, which is down to a minute as opposed to the original 7 minutes.

Every few weeks we had a "flighting" moment where we would deploy our changes to a production-like environment, serving production traffic. This allowed us to benchmark the consolidated REST API and verify its responses.

This week, we deployed this consolidated REST API. And nobody noticed - the very nature of this sort of change. We are really excited about it. Reduced maintenance is a benefit we're really happy with. The refactored code base also lays the foundation for future work on our REST API.

## What's next?

The consolidated REST API, much like our [switch from WCF OData to Web API OData][3], is a project to reduce maintenance and improve the foundation on which the NuGet services are built, for today and tomorrow. We are also working towards documenting the API.

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. If you would like share your pain points and your current and future needs, use the [calendly link][4] to set up a quick 15-30 min call with us. If you would like to send us an email instead, hit us up at <feedback@nuget.org>.

You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][5].

 [1]: https://www.nuget.org
 [2]: http://blog.nuget.org/20150223/nuget-3.0-beta.html
 [3]: http://blog.nuget.org/20160216/Switching-from-WCF-OData-to-Web-API.html
 [4]: https://calendly.com/harikm/nuget-feedback
 [5]: https://github.com/Nuget/Home