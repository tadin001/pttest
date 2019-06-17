---
ID: 53
post_title: Help Us Test CDN for NuGet.org
author: Howard Dierking
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/help-us-test-nuget-cdn/
published: true
post_date: 2012-08-02 00:00:00
---
Over the past few weeks, we’ve been investigating whether to turn on the [content delivery network (CDN)][1] feature of the Azure blob storage container for NuGet packages. In theory, this would make package downloads faster – especially if you’re located outside of the United States.

We want your help to conduct an experiment to measure the difference in downloading packages with CDN enabled verses disabled. Our initial experiment deployed nuget.exe into different regions and measured the time to download a set of NuGet packages. Unfortunately, in all of the regions we deployed to, we were still sitting on an Internet backbone connection, so even our baseline measurements (with CDN disabled) showed a statistically insignificant difference between a deployment in northern Europe and a deployment in the USA. As such, it would seem that enabling CDN wouldn’t yield any measurable benefit. 

We think that the reason for the inconclusive baseline is due to the fact that there’s no [“last mile”][2] involved in the measurements, and this is where the majority of the slowdown happens. While we could try and simulate last mile time, we would rather change the experiment a bit and ask for your help. 

We’ve created a custom of NuGet.exe along with a batch file runner that you can download [here][3]. Just unpack the zip file and run NuGet-CDN.bat. This will download the top 25 NuGet packages from both the standard package URL and a CDN URL and then log the time it takes to perform the download operations. You will then be asked to optionally provide your location, which will help us get a better sense of the global impact of using the CDN.

![Running the NuGet CDN batch file][4]

The batch process will wrap the log files along with your location information into a new NuGet package. Please email that package to <nugetgallery@outercurve.org>. 

Thanks in advance for your help!

 [1]: http://www.windowsazure.com/en-us/home/features/caching/
 [2]: http://en.wikipedia.org/wiki/Last_mile
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-CDN.zip
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/use-cdn-test.png