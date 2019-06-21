---
ID: 226013
post_title: NuGet 3.0 CTP 1 Released
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-3-0-ctp-1-released/
published: true
post_date: 2014-07-15 00:00:00
---
Today marks an important milestone for [Building NuGet 3.x][1]; we've published a NuGet 3.0 CTP1 build of our Visual Studio extension. This ***preview*** release represents a few months of code refactoring and [server-side architecture][2] now being utilized by the client code. While the release doesn't show off any slick new NuGet features or even address very many bugs, it reflects a lot of engineering work that unblocks us in several ways. We'd like to show you some of the changes we've made under the hood and ask for your help validating we haven't regressed anything outside our test matrix.

## What We Released

The updated extensions can be found on our CodePlex project site under the [NuGet 3.0][3] release page. Our pre-release versions don't get published to the [Visual Studio Extension Gallery][4], so you have to download the updates from CodePlex in order to upgrade.

### Updates for Visual Studio 2013 and Visual Studio "14"

For this first CTP of NuGet 3.0, we're only updating the extensions for Visual Studio 2013 and Visual Studio "14". We're not releasing an update for Visual Studio 2010 or Visual Studio 2012 at this time. We have always had a single extension that targets both of those versions, whereas we have separate extension for Visual Studio 2013 and Visual Studio "14". NuGet 3.0 is now using some libraries that require .NET 4.5, and that would rule out updates for Visual Studio 2010.

Whether or not subsequent releases for NuGet 3.0 will target Visual Studio 2010 is still undecided, but we're strongly leaning toward cutting Visual Studio 2010 from the release. If we do make that decision, we'll remove Visual Studio 2010 support from the Visual Studio 2010/2012 extension, making it Visual Studio 2012 only. We'd then provide support for Visual Studio 2012, Visual Studio 2013, and Visual Studio "14".

Rest assured that existing Visual Studio 2010 users will still be supported and will be able to use NuGet and NuGet.org to download and install packages. The NuGet 2.8.1 extension for Visual Studio 2010 will remain supported even though NuGet 3.x would be available for newer versions of Visual Studio.

To update to the new version, download the VSIX file and double-click it to install it into Visual Studio. Note that for Visual Studio "14" you will first need to uninstall the existing NuGet extension; uninstalling requires running Visual Studio as Administrator.

Here are the direct download links:

*   [NuGet Package Manager for Visual Studio 2013][5]
*   [NuGet Package Manager for Visual Studio "14" CTP][6]

## Noticeable Changes

While this CTP mostly represents changes we've made under the hood, there are a couple noticeable changes too.

### Removed 'Enable NuGet Package Restore'

When we introduced [Automatic Package Restore][7] in [NuGet 2.7][8], we knew that we'd eventually remove support for the MSBuild-integrated package restore approach. We didn't want to prematurely remove it so we waited for the community to ask us to do so, and you have.

[NuGet Issue 4019][9] received quite a few votes and made its rounds on twitter a few times.

<blockquote class="twitter-tweet" lang="en">
  Every Time someone enables <a href="https://twitter.com/hashtag/nuget?src=hash">#nuget</a> package restore on a solution, a kitten dies. Learn the new workflow! <a href="http://docs.nuget.org/docs/reference/package-restore">http://docs.nuget.org/docs/reference/package-restore</a> — David Ebbo (@davidebbo) <a href="https://twitter.com/davidebbo/statuses/425493392475168768">January 21, 2014</a>
</blockquote>

<blockquote class="twitter-tweet" lang="en">
  Please vote for this <a href="https://twitter.com/hashtag/NuGet?src=hash">#NuGet</a> issue and save some kittens <a href="https://nuget.codeplex.com/workitem/4019">https://nuget.codeplex.com/workitem/4019</a> /cc <a href="https://twitter.com/davidebbo">@davidebbo</a> — Xavier Decoster (@xavierdecoster) <a href="https://twitter.com/xavierdecoster/statuses/432976436623060992">February 10, 2014</a>
</blockquote>

In NuGet 3.0, we are removing the context menu item that allows you to enable MSBuild-integrated package restore on new projects. If you have already enabled MSBuild-integrated package restore within your solution, the behavior will remain the same though:

1.  The project will keep using MSBuild-integrated package restore instead of Automatic package restore
2.  If you load new projects into the same solution, the integration is still viral and NuGet will silently enable MSBuild-integrated package restore on all projects within the solution

You can learn how to migrate from MSBuild-integrated package restore to Automatic Package Restore at <http://docs.nuget.org/docs/workflows/migrating-to-automatic-package-restore>.

### New Package Source

NuGet 3.0 CTP1 introduces a new package source with the name "nuget.org (3.0.0-ctp1) preview". This preview package source is based on our new "API v3" protocol with [JSON-LD][10]. The URL for the package source is `http://preview.nuget.org/ver3-ctp1/`. Here are a few notes on this package source:

1.  The root URL is a 404 (for now) - This is a temporary means for the client to identify that the package source might be v3.
2.  In the 404 condition, the client will then send a request to /intercept.json and check for a JSON response.
3.  This intercept.json resource identifies the resources the client can use for consuming package metadata instead of issuing OData requests. We'll cover this more below in the *API v3 Requests* section.

There's a known issue that this package source is getting registered as disabled by default for some users. To switch from the 'nuget.org' package source to the new 'nuget.org (3.0.0-ctp1) preview' package source, edit your NuGet Package Sources.

![Enable the Preview Package Source][11]

### Faster!

You should notice that this new package source is noticeably faster. We haven't measured it yet, but we've observed that everything is much more responsive. Search, package installation, and update checks should all be faster for you. This is a result of client requests going to our CDN to get data directly out of storage, instead of going through our v2 OData endpoint on the server.

## Changes Under the Hood

As mentioned earlier, most of the work in this release was actually done under the hood. We have now paid off some technical debt that has been holding us back from introducing new features to NuGet.

### Dependency Resolver

NuGet's dependency resolution has always been a complicated piece of code, partly because it combined a few different concepts together.

1.  Discover and reason over package dependencies
2.  Download and unzip packages
3.  Install packages into the project, copying in files and adding assembly references

Once the deepest dependency was discovered, we'd start to perform actual package installations then we'd walk up the dependency graph until we reach the top. This was okay until some scenarios required back-tracking, which can result in uninstalling packages that were just installed. More importantly though, the implementation was a mess of spaghetti code that had gotten risky to touch.

We've now completed some drastic refactoring of the dependency resolver and gotten it to where it can perform the actual dependency resolution separately from the act of installing/uninstalling packages. This allows us to make algorithm changes and test them purely against data (as it should have always been).

As you'd expect, these code changes affect hot code paths and all of the core functionality of NuGet. We've tested these changes thoroughly, but please let us know if you find any scenarios where dependency resolution is behaving differently from before.

### API v3 Requests

We want NuGet 3.0 to use the new Linked Data protocol we're implementing for nuget.org, but it is going to take a lot of time to write the new API v3 client and get it fully integrated into the Visual Studio extension. At the same time, we wanted the client to start making API v3 requests as soon as possible, to help us vet the design and protocol. To accomplish this, we're now intercepting API v2 requests and translating them into API v3 requests when the server implements API v3.

As mentioned above, we presently detect that a package source implements API v3 by way of a (temporary, hacky) handshake.

1.  The package source URL returns a 404, and
2.  Reponds to a request for /intercept.json with a document that provides some interception resources

Here's the intercept.json resource that we're using for this preview:

<pre>{ 
  "resolverBaseAddress" : "http://preview.nuget.org/ver3-ctp1/packageregistrations/1/", 
  "isLatest" : "http://preview.nuget.org/ver3-ctp1/islatest/segment_index.json", 
  "isLatestStable" : "http://preview.nuget.org/ver3-ctp1/islateststable/segment_index.json", 
  "allVersions" : "http://preview.nuget.org/ver3-ctp1/allversions/segment_index.json", 
  "searchAddress" : "https://api-search.nuget.org/search/query", 
  "passThroughAddress" : "http://nuget.org" 
}</pre>

Once the client completes this handshake with the server, we then intercept all outgoing OData requests the client is going to make, translate them into API v3 requests for the equivalent data, get the responses, and then manufacture OData-style responses out of the data returned.

So under the hood, when you're connecting to our new 'nuget.org (3.0.0-ctp1) preview' package source, every single request the client makes ends up going against our API v3 Search Service and JSON-LD resources. The JSON-LD resources are served out of Azure Storage and through our CDN; there's no compute involved and we will achieve a significant improvement in availability.

### Package Manager Debug Console

For our own testing of the API v2 to API v3 interception, we found that we needed to create a Package Manager Debug Console. This console shows all of the v2 OData requests that are being made, how they're translated into API v3 requests, whether or not our HTTP cache is hit, and the response details. We decided to include this debug console in the Visual Studio extension so that you too can see how the requests are being intercepted and handled.

You can open the Package Manager Debug Console through the Tools NuGet Package Manager menu.

![Package Manager Debug Console Menu Item][12]

Once opened, all server requests are shown in the console with the request/response details. If you report any issues with the NuGet 3.0 CTP, we may ask you to provide the console output to us. Note that when you select text in the console, it doesn't appear to be selected but it really is. Once you right-click after selecting text, you'll see the text get highlighted properly.

![Package Manager Debug Console][13]

## Known Issues

### Catalog Update Frequency

Our CQRS-style architecture on the server is still under development. There are times when our catalog updates are lagging behind, running slowly, or even completely shut off for a while. Once we get to where we're happy with the server implementation, we'll publish another NuGet release and switch from a "preview" package source to one that's more stable.

We are also not presently replicating [package edits][14] into our API v3 catalog.

### Package Source Disabled by Default

As mentioned previously, some users will not see the 'nuget.org (3.0.0-ctp1) preview' package source after installing the new extension. If the package source doesn't show up for you, check your NuGet Package Source settings and see if it's disabled.

### SSL (HTTPS) Not Yet Supported

The new package source is registered as an HTTP address instead of HTTPS. This is a temporary limitation with our usage of our CDN, but we have a plan to work through it. In a future NuGet 3.0 release, we'll switch back over to using SSL through an HTTPS address.

### Existing Visual Studio "14" Extension Must Be Uninstalled

If you're using Visual Studio "14", you will need to first uninstall the existing NuGet extension and then install the new one. Our previous release in Visual Studio "14" had an incorrect extension identifier; this new release fixes it, but you'll need to uninstall the old version of NuGet.

## Sending Us Feedback

Please check out this NuGet preview release and try it out. Disable the normal 'nuget.org' package source and enable the 'nuget.org (3.0.0-ctp1) preview' package source. Perform all of your usual NuGet work using that package source and let us know if you see any unexpected changes in behavior.

Download the NuGet 3.0 CTP1 Visual Studio Extension now:

*   [NuGet Package Manager for Visual Studio 2013][5]
*   [NuGet Package Manager for Visual Studio "14" CTP][6]

To send us your feedback, feel free to:

1.  Post comments on this blog post
2.  File issues on [CodePlex][15] and mention that it's a 3.0 regression
3.  Reach us on Twitter at [<https://twitter.com/nuget>] or using [#nuget][16]

## NuGet 3.0 Plans

There's still a lot in store for NuGet 3.0. We plan to reach the final release by the beginning of September. There will in fact be some new features and bug fixes in the release. You can expect to see:

1.  A brand new package manager UI (and we'll share screen shots as soon as we have them)
2.  Ability to select specific package versions from the UI for package installs and updates
3.  Package install/update "previews" where you can see what changes would occur if you installed/updated packages
4.  Filtering packages by supported target framework, avoiding errors during package installation
5.  Review and acceptance of pending external pull requests
6.  Lots of bug fixes

We know that many of you have noticed that it's been a couple of months since we fixed many bugs or accepted pull requests. We have needed to stay focused on reaching the point of this CTP release with some drastic code refactoring, but we will now start getting caught back up. We appreciate your patience and understanding!

Stay tuned for more updates along the way too; we plan to publish more preview releases of NuGet 3.0 between now and the final release.

 [1]: /20140424/building-nuget-3.x.html
 [2]: /20140711/nuget-architecture.html
 [3]: https://nuget.codeplex.com/releases/view/116898
 [4]: http://visualstudiogallery.msdn.microsoft.com/
 [5]: https://nuget.codeplex.com/downloads/get/879740
 [6]: https://nuget.codeplex.com/downloads/get/879741
 [7]: http://docs.nuget.org/docs/reference/package-restore#Automatic_Package_Restore_in_Visual_Studio
 [8]: http://docs.nuget.org/docs/release-notes/nuget-2.7
 [9]: https://nuget.codeplex.com/workitem/4019
 [10]: http://json-ld.org
 [11]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/package-sources.png
 [12]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/debug-console-menu.png
 [13]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/debug-console.png
 [14]: /20130823/Introducing-Edit-Package.html
 [15]: http://nuget.codeplex.com
 [16]: https://twitter.com/search?q=%23nuget