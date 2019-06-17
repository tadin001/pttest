---
ID: 63
post_title: Building NuGet 3.x
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/building-nuget-3-x/
published: true
post_date: 2014-04-24 00:00:00
---
## Evolution of NuGet

At MonkeySpace 2013 last July, we revealed some of our thinking for the [Evolution of NuGet][1]. These were the ideas we had for what would become NuGet 3.x and they included the following areas:

### Package Discovery

1.  Context-sensitive enumeration and search
2.  Statistics and reports for package authors and consumers
3.  Editable package metadata
4.  Package discovery and sharing through social graphs

### Package Trust / Incompatibility

1.  Organizations or team accounts
2.  Owner reputation
3.  Friendly license names and filters
4.  Feeds for owners' packages and followed packages
5.  Create a system for reporting package incompatibilities

### API

1.  Create a RESTful API v3 for use by both the NuGet clients and ecosystem partners
2.  Facilitate a high volume of reads on the API while significantly improving uptime
3.  Existing OData service will be eventually consistent

### Package Installation / Build

1.  Resolve references at build time for better retargeting and multi-targeting
2.  Treat direct installs differently from dependencies
3.  Global package installations
4.  NuGet packages as a natural part of build
5.  NuGet packages replace assemblies as the natural unit of reference

## Making Progress

The full list is a pretty tall order but we're making progress toward these goals. In fact, since giving that talk in July 2013, here are some of the features that have been released:

1.  [Statistics][2] for package authors and consumers
2.  [Editable package metadata][3]
3.  [Friendly license names][4]
4.  [A new search experience on the Gallery][5]

Beyond this, the team has been concentrating on the RESTful API v3 architecture and design. We came to the conclusion that a significant number of new features will be extremely data-driven and we needed to ensure the design was extremely scalable and reliable. We couldn't build those features on top of the compute-heavy architecture we have today. *Let's be honest, we already have more downtime than we'd like.*

Instead, it is time to re-architect the gallery and build up a new system based on micro-services. Because the NuGet client tools need to be able to discover and interact with these micro-services, significant changes are required to the client as well as the server. This new architecture will unlock the ability to create many new data-driven features while also providing a more reliable service for the core functions of NuGet.

We've now embarked upon NuGet 3.x!

## NuGet 3.x

### So what is NuGet 3.x?

A wave of NuGet client tools releases to begin taking advantage of a new server architecture.

### What does that mean?

As we roll out new micro-services for the NuGet Gallery, we'll be making significant changes to the NuGet client tools to utilize those services directly rather than going through a single OData-based API.

### What are these micro-services that you're creating?

Great question! We have been able to break the NuGet Gallery up into the following conceptual package services:

1.  Downloads (serving both package installs and restores)
2.  Metadata and Feeds (for enumerating packages and resolving dependencies)
3.  Search (getting context-sensitive package lists and search results)
4.  Metrics (reporting download statistics)
5.  Events (exposing a stream of activity on the gallery, which can be utilized by a variety of different consuming services)

One of the key points here is that the downloads service is separated from the metadata service. We've seen many times that package metadata needs to be fully exposed without having to download packages. And we've also seen that there are scenarios for hosting package downloads without serving package metadata.

We're breaking Search out from the metadata/feeds because we're using compute for search (via [Lucene.NET][6]), while we want package feeds/metadata to be served without compute. Furthermore, Search is for human consumption while Feeds/Metadata are for programmatic consumption.

The Metrics service allows package download statistics to be reported by the client explicitly, rather than recording those statistics *during* package download like we do today. That would also create the opportunity for us to send more information in for statistics and allow users to opt out as desired. The data collected would serve our context-sensitive search and statistics reports on where/how packages are used. More importantly though, it will allow package downloads to be served without *any* compute that could possibly fail (and presently does more than is desirable).

The Events service will expose a data stream for events that have occurred on the gallery--like new packages getting published and package ownership changing. These events can be replayed to fully replicate nuget.org, setting up the opportunity for value-adding services such as gallery mirrors.

#### More to come...

We know there will be more micro-services too; these are just the first handful we're building. The architecture allows new services to show up one-by-one.

## API v3

Virtually all of this work centers around something we call "API v3." It's the new RESTful API for connecting to NuGet services. Each micro-service will have its own resource representations, but it all comes together under general guidelines that we put under this v3 umbrella.

### Linked Data

The most critical principle for API v3 is the use of Linked Data. Specifically, we have chosen to use [JSON-LD][7] for our linked data format.

Presently, NuGet clients have to know to connect to <https://www.nuget.org/api/v2/> to consume our main feed. That URL is our OData endpoint where all of the package data is exposed. Our clients use WCF Data Services to construct OData-based URLs for specific requests--these include search queries, specific package lookups, and more. All requests funnel through a single pipeline of code. That code originally resulted in hitting our SQL server, but today we intercept queries and answer requests from our Lucene search index as often as we can. The code is gnarly.

We want to break out of this pattern and have specific resources for specific purposes. We can then fine-tune responses for specific requests without much risk of unintended consequences. Furthermore, we want to move away from the constructed URLs and generated proxies where changing the details of the server resources or HTTP responses would break existing clients. Instead, the v3 client will follow links to new resources and extract data that understands from responses, thereby enabling it to adapt as server resources evolve. Linked Data with task-specific resources will provide this for us.

If you'd like to see an example this, here's a sneak peak at some JSON-LD blobs:

*   [Packages owned by the 'nuget' user][8]
*   [The NuGet.Server package][9]
*   *These are best viewed in Chrome, using the [JSON Formatter][10] extension from the [Chrome Web Store][11]*

### Eventually Consistent Responses

We are working to eliminate compute from the request pipeline wherever possible. NuGet is an extremely read-heavy system, with millions of reads for every write. Yet today's design uses a system where writes are relatively cheap and reads are very expensive. It's time to invert that!

To achieve virtually free reads, we're moving to an eventually consistent response model. When packages get uploaded, package ownership changes, or other data writes occur, we'll write that data to our authoritative data store immediately (presently SQL). But requests for package metadata or downloads won't query against that authoritative data store. Instead, they will read JSON-LD blobs that are computed by back-end services.

We have a "Work Service" in our mix of micro-services, and it is responsible for performing scheduled work. Some of the scheduled work is to act upon data events from the authoritative data store. If 57 new packages got published in the last 30 seconds, it will then queue up work to process those 57 packages. That work will result in JSON-LD blobs getting produced and added into the system.

This means it might take a few minutes before a published package is available for discovery and download. But for that trade-off, we can scale out our read capacity in a proactically limitless manner. And we can separately scale our Work Service to make sure it can keep up with the data ingress.

### NuGet Client API

The NuGet client API is going to be overhauled--or dare we say, "rewritten." We're not sure of the full scope of this work yet or how it will be represented in our assemblies; we know it will be drastic though. The new API will provide the foundation for connecting to all of the micro-services, and it will be designed so that new micro-services can easily have new client handlers provided. The API will also address many cross-cutting concerns to make those service handlers clean.

Some cross-cutting concerns we know to exist are:

1.  NuGet service registration and connection
2.  JSON-LD document processing
3.  Asynchronous linked data navigation
4.  Heavy use of caching
5.  Support for mirrors
6.  Authentication and other networking concerns

We've already made some progress in these areas. For instance, we ported a Java-based JSON-LD processor over to C# and published it both to [GitHub][12] and [NuGet][13]. We're also working on a library that builds on top of JSON.NET to provide asynchronous linked data navigation. We'll soon be working on a good cache implementation to use underneath both of those.

The new client API will also provide some features for these areas:

1.  Traversing package metadata easily without downloading packages
2.  Downloading packages without having to first read their metadata
3.  Invoke the dependency resolver without installing packages
4.  Determine if a package is compatible with a project without actually trying to install it
5.  Support for Semantic Versioning 2.0
6.  Support for architecture, configuration, and toolset groups in packages
7.  New dependency resolver features for more complex graphs
8.  And much more...

### NuGet Services Components

Everything we're doing on the service side is open-source of course. You can find all of the NuGet Services components at <http://github.com/nuget/>. We used to just have a couple of repositories there, with one large repository for the gallery; now we have a bunch of independent repos for each of our service and operations components.

NuGet already has a thriving ecosystem, but we hope that with this refactoring of the service components, we'll enable the ecosystem to grow even further. We're hoping to see other teams eventually create new NuGet micro-services that can augment the code NuGet functionality.

When a NuGet client connects to a NuGet repository, that repository can provide whatever subset of micro-services it wants. This will allow new stand-alone services to be created by us and by the community and for users to consume them easily.

### Backwards Compatibility

I hope it goes without saying, but we will maintain backwards compatibility on both the server and the client. With the rewritten NuGet client code, we'll maintain support for connecting to V1- and V2-based NuGet package sources. And the NuGet Gallery will continue to serve its V2-based OData feed indefinitely. That V2-based feed will become eventually consistent at some point, querying off a read-only copy of the live system's database.

### Ecosystem Partners

We will encourage all ecosystem partners to adopt API v3 as soon as the first client release is out with support for it. Until then, no action is necessary.

If you want to get involved as early as possible, please hop over to [the nuget-ecosystem Google Group][14] and join us (or email <nuget-ecosystem+subscribe@googlegroups.com>). We've also been hanging out in [Jabbr][15] more often, and we're usually listening to [@nuget][16] and [#nuget][17] on Twitter.

## Timeline

We are working on the release schedule that will be used to roll out the API v3-based client updates. We expect the first release to be sometime this summer (U.S.), but we have't decided on the scope of that release yet. We'll post more details to the client project home page on [CodePlex][18] when we firm up the plans.

Between now and then, we don't expect to have any other NuGet client releases. And we're also not putting much effort into other NuGet features or bug fixes right now. We tried for a long time to chip away at this V3 work while also implementing other features, but we weren't making enough progress on V3. We're now officially heads-down building NuGet 3.x and we will remain that way for the next several months.

 [1]: http://vimeo.com/78045959
 [2]: http://www.nuget.org/stats
 [3]: http://blog.nuget.org/20130823/Introducing-Edit-Package.html
 [4]: http://blog.nuget.org/20131011/friendly-license-names.html
 [5]: http://blog.nuget.org/20140411/new-search-on-the-gallery.html
 [6]: http://www.nuget.org/packages/Lucene.Net/
 [7]: http://json-ld.org
 [8]: http://nugetdev1.blob.core.windows.net/package-metadata/owners/nuget.json
 [9]: http://nugetdev1.blob.core.windows.net/package-metadata/packages/nuget.server.json
 [10]: https://github.com/callumlocke/json-formatter
 [11]: https://chrome.google.com/webstore/detail/bcjindcccaagfpapjjmafapmmgkkhgoa
 [12]: https://github.com/NuGet/json-ld.net
 [13]: http://www.nuget.org/packages/json-ld.net/
 [14]: https://groups.google.com/forum/#!forum/nuget-ecosystem
 [15]: https://jabbr.net/#nuget
 [16]: https://twitter.com/nuget
 [17]: https://twitter.com/search?q=%23nuget
 [18]: http://nuget.codeplex.com