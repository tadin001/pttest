---
ID: 92
post_title: >
  Why did my package download count
  suddenly go up
author: Maarten Balliauw
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/why-did-my-package-download-count-suddenly-go-up/
published: true
post_date: 2015-09-30 00:00:00
---
Last week, we released our [Revised Download Counts and Package Statistics][1] that are now based on the raw logs of our CDN. For this to become reality, we had to also import data from our old statistics data warehouse into the new system. As it turns out, we overlooked something very important: in the old system (and by importing data, also in the new system), NuGet download counts did not reflect non-normalized package versions. Today, we’re taking these downloads into account as well, resulting in an increase in package downloads for many NuGet packages that have been in the NuGet gallery for a long time.

Our old statistics warehouse contained download facts for non-normalized versions, whereas all of our services only take normalized versions into account. The normalized version standardizes the version formats in use and makes querying and comparing packages easier. Say a package has version 2.0.0.0, we normalize that version into 2.0.0 and work with that version number instead. The same goes for 2.0 which becomes 2.0.0, essentially considering 2.0.0.0 and 2.0 as the same version. Another example would be version 1.2.3.0115, which is normalized as 1.2.3.115.

Unfortunately, the old statistics warehouse was never updated when we started normalizing version numbers. The result of this change a few years ago was that the NuGet gallery no longer listed the correct number of package downloads, which we are now fixing with this update.

## Can you explain that again?

As an example, take [NLog][2], and more specifically its [version 3.0.0][3]. If we download the package and open its nuspec file (e.g. with [NuGet Package Explorer][4]), we can see it was published as version 3.0.0.0. That also means at one time, users were downloading version 3.0.0.0 until we switched our infrastructure to work with the normalized 3.0.0 instead. Here are the download counts for this package that we had in our old statistics warehouse:

*   NLog 3.0.0 - 42160 downloads
*   NLog 3.0.0.0 - 25531 downloads

Before the change we will be doing this week, the NuGet gallery would show 42,160 as the download count as we can see in the image below. This is only the download count for version 3.0.0, however. After this change, we converted all downloads for version 3.0.0.0 into downloads for version 3.0.0, consolidating both counts to a total download number of 67,691 downloads for this package.

<img src="https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/example.png" width="540" />

We have been hard at work to convert these old, historic downloads to also have normalized versions, which makes them visible to all of our systems now. This does mean many packages will see an increase in their number of downloads. It also means download numbers are now displaying the correct download number for packages that have been in NuGet since a long time.

 [1]: /20150924/Revised-Download-Counts-and-Package-Statistics.html
 [2]: https://www.nuget.org/packages/NLog/
 [3]: https://www.nuget.org/packages/NLog/3.0.0
 [4]: http://npe.codeplex.com/