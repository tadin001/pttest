---
ID: 102
post_title: Switching from WCF OData to Web API
author: Maarten Balliauw
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/switching-from-wcf-odata-to-web-api/
published: true
post_date: 2016-02-16 00:00:00
---
One of the less visible changes since we [released NuGet 3.0][1] is that NuGet uses a new server "API v3". This new API is designed around high availability for critical scenarios such as package restore and installing packages. API v3 will be the way forward for NuGet while keeping "API v2" around for older NuGet clients. We will be making a big change to the implementation of API v2 though: we're switching from WCF OData to Web API.

In this post, we want to take you through why we are making this change and what it means to our users.

## Background

When NuGet was released, we needed a client/server protocol that would allow us to query packages, fetch package metadata and download package artifacts. The logical choice at the time was [OData][2]: by exposing our Entity Framework model over OData, the NuGet client could run any query on our database, using OData as the protocol.

What we didn't know was how popular NuGet was going to become. With more and more users, the number of queries vastly increased. And that increase was very visible on our database: since our OData implementation allowed for running pretty much any (read-only) query on the database, it often slowed down to a crawl.

Was OData a bad choice then? We don't believe so. OData has great features that allow for exposing data over HTTP, with querying and projection capabilities. However mapping that API 1:1 to our database was less brilliant in hindsight.

The NuGet v3 API is centered around a static catalog of NuGet package metadata and a Lucene-based search engine, decoupling the increase in NuGet usage from the increase in database load. Of course not everyone is upgrading to V3 immediately, so we will have to have API v2 around for another while.

## Going forward with API v2

Keeping API v2 around in its current form does mean we are bound to a few prerequisites: we can't easly change database models, we can't easily redirect all incoming queries to the Lucene search to ensure V2 and V3 return the same data - although we already do this for some.

When looking at the [NuGet Gallery code base][3], traces of a `SearchHijacker` can be found. This is the class responsible for parsing incoming query expression trees from the WCF-based OData endpoint and tranlate them to Lucene, if possible. But parsing expression trees isn't fun. There are a lot of edge cases, and as we've seen in our IIS logs, there are a lot of interesting queries coming in that can't be hijacked towards Lucene and still hit our database, sometimes quite hard.

The OData team in Microsoft has been hard at work in building a server-side OData framework for ASP.NET Web API. Where WCF OData would give us an expression tree which we have to parse ourselves, Web API OData can give us all OData query options as simple primitives (as well as the expression tree if we need it). This makes it more efficient in terms of translating queries for execution on Lucene and filter out query conditions that don't make sense. So that's what we are working on!

Our initial version of the "Web API OData based v2 service", or "new API v2" in short, is a 1:1 port of the old API v2. No changes in the API have been made: no query functionality was removed. We did make some caching changes to improve perceived performance for developers in older versions of the client that are using the v2 endpoint (2013, 2012, 2010).

The main goal for making this change was to explore if it was feasible to make the conversion and prepare for future improvements to it. Feel free to explore the [changes we made to the NuGet Gallery on GitHub][4].

## What's next?

We will now start looking at reducing the API surface where it makes sense. Some queries should not be allowed, others can be optimized. The ultimate goal is to make the API v2 a stable and performant shim on top of API v3, ensuring the data returned in both API's is identical while not breaking backward compatibility.

 [1]: /20150720/nuget-3.0.0.html
 [2]: http://www.odata.org
 [3]: https://github.com/NuGet/NuGetGallery
 [4]: https://github.com/NuGet/NuGetGallery/pull/2848