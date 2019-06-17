---
ID: 62
post_title: A new search experience on the Gallery
author: Andrew Stanton-Nurse
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/new-search-on-the-gallery/
published: true
post_date: 2014-04-11 00:00:00
---
For a while now, we've been working on some major improvements to search. Last week we deployed these improvements to production on <http://www.nuget.org>. In this post, I'll describe how the new service works. However, before we discuss the new service, let's step back a bit and discuss the history of Search on NuGet.org

## Search via SQL

Our first search implementation was done using our existing OData endpoints. We simply used the [OData Query Operators][1] to filter data in our database by the User's query. This had two major problems: It was fairly inefficient. SQL is a good engine for data lookup, but by default it is not as efficient at full-text search operations, like we wanted on NuGet.org. Second, it was inaccurate. Again, relational databases are great at lookup and querying, but only when the query is similarly structured. Our queries to SQL ended up as just a series of `LIKE` comparisons for each field in our database `OR`ed together.

So, we opted to use a tool that was designed explicitly for full-text search to augment our SQL database...

## Lucene on the Web Server

Our second implementation was very much seen as an optimization for a very specific pair of cases: The search box in the NuGet Visual Studio Dialog, and the search box on the NuGet.org Website.

Both of those views focus solely on the latest version of a package. So, our initial optimization was to take the latest version of each package and add it to a [Lucene.NET][2] powered index running off the local file system of each web server. Each machine had a background task running that synced its local Lucene index with data from the database. Search queries were then executed against that index. The result was a dramatic speed and relevance improvement. Lucene provides very powerful boosting and scoring extension points and we integrated some of our statistics in to that process.

However, there were still a few problems:

1.  Each machine had its own copy of the index, meaning a machine could get out of sync with the others
2.  Our scoring algorithms were based off of total download counts, meaning new packages had a tough time getting noticed due to download-count-behemoths who have been in the gallery for years.
3.  Finally, we wanted to expand the kinds of queries we could do with the Lucene index, but because the index only contained the latest version of each package, we were restricted to working with that set.
4.  Having the index on the web server was useful, but constrained us to maintaining a smaller index in order avoid stressing the web server too much.

To solve those problems, we started work from the ground up on a new search infrastructure.

## NuGet Search Service

To combat the issues we had with Lucene integrated into the web server, we developed an entirely new Search Service, written from the ground up. The service, like all NuGet.org code, is completely [open-source on GitHub][3]. The new Search Service is a separate HTTP service that is responsible for answering search queries. By moving search to its own set of machines, we hoped to reduce and even remove most of the issues we encountered with the previous search models.

First, the search service stores the master copy of the Lucene index in Azure Blob Storage using the [Azure Directory for Lucene.NET][4] library. Of course, accessing the index from Blob Storage would be very inefficient, so the entire index is kept in memory on the Search Service machines at all times. It is frequently synced with the Blob Storage copy, but in general, most queries should be served directly from memory. Storing the index in a central location allows it to be updated in a single location, while the memory cache allows queries to be served very quickly. We track round-trip times between the Gallery and this new Search Service and the average seems to be hovering around 80ms, which is as fast as most of our database queries (and even faster than some of the heavier ones!). Storing the authoritative copy of the index in blob storage also gives us a few major benefits. For example, we can easily spin up new Search Service nodes and they will just grab the latest index from blob storage (loading the whole index into memory takes about 2-5 minutes). Also, our *write* operations into the index (Adding new packages, deleting packages, updating existing packages, etc.) can be centralized and need only update the blobs.

We also **dramatically** increased the scope of the Index. It now contains every single version of every package ever uploaded (see note below). At the time of this post, that comes to about 254,886 documents (the number only differs from the total package count on the Gallery due to packages being unlisted by their authors, something which we frequently do with test packages as well ;)). Despite the size, the total index size comes out to around 600MB. Of course, when the index was on the web server, this would be an unacceptable about of memory pressure to add, but on the Search Service, we have free reign over the entire machine! As the index grows, we can safely continue to scale up the available memory by moving to more memory-intensive Azure VM profiles. Of course, scaling up isn't a perfect solution, so we will continue to monitor memory growth, but at our growth rates, it's going to be the ideal solution for a long time.

***Note:** We do still hide unlisted packages from search queries. However, this is a good time to remind everyone that unlisting is **not** a secure way to remove data from NuGet. It is a mechanism to reduce a package's visibility, **not** a way to prevent download of your package. If you need your package to be **completely removed**, use the Contact Support link on your package page to request that we delete the package.*

Lastly, we overhauled our scoring and analysis algorithms. This work started back in June of 2013, with the release of NuGet 2.6. In that release, the client began supplying an additional HTTP header when it requested a package for download: `NuGet-Operation`. This header contained a value indicating what the user was doing in order to cause the package to be downloaded. Once NuGet 2.6 started sending this data, we began collecting download data into a data warehouse and categorizing it based on many different aspects, including this Operation value. This began to manifest in the Package Statistics pages you can view from each Package detail page. The next step was to take this data and use it in scoring.

Whereas our previous algorithm scored results based on total download count, our new algorithm scores them based on "Recent Installs." Specifically, it uses the number of downloads requested with a `NuGet-Operation` value of `Install` or `Update` within the past **6 weeks**. This allows really popular packages like jQuery, EntityFramework and Newtonsoft.Json to remain fairly high up, because they are being Installed frequently, but also allows new packages to climb the ranks a little faster by shortening the time window and giving them a chance to catch up. Filtering by the `Install` and `Update` operations also allows us to filter out the noise caused by build servers using [Package Restore][5] to download packages on every build.

## Improving Relevance

John Taylor, an engineer on our team, spent the last few months of 2013 diving in to Lucene scoring and fiddling with parameters trying to nail down some of the best ways to score results. We had a few smaller-scale tests where we released some sample algorithms to progressively larger groups of people to get feedback. In the end, we managed to nail down an algorithm which gave us confidence that we could handle most of our requests efficiently and accurately. 

Still, testing with a broad audience doesn't cover everything, so we know there are going to be gaps. Please do not hesitate to give us feedback on our results by [filing bugs][6] or [pinging us on twitter][7]. Tuning our search algorithm will be a never-ending process so keep telling us what you think!

 [1]: http://www.odata.org/documentation/odata-version-2-0/uri-conventions/#QueryStringOptions
 [2]: https://lucenenet.apache.org/
 [3]: https://github.com/NuGet/NuGet.Services.Search
 [4]: https://azuredirectory.codeplex.com/
 [5]: https://docs.nuget.org/docs/reference/package-restore
 [6]: https://github.com/NuGet/NuGetGallery/issues
 [7]: https://www.twitter.com/nuget