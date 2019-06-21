---
ID: 225943
post_title: Introducing Package Statistics
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/introducing-package-statistics/
published: true
post_date: 2013-02-26 00:00:00
---
We have introduced a new feature on <http://nuget.org> where you can see download statistics for packages. With this initial drop, we hope to whet your appetite and collect your input on further statistics features; it's a mere fraction of what we can imagine.

## Statistics Tab

At the top of the site, a new 'Statistics' tab is displayed.

![statistics tab][1]

This tab takes you to <http://nuget.org/stats>, which shows the two high-level statistics reports we have at the moment. While we expect this page will expand with more reports, the first two we've introduced are "Downloaded Packages (Last 6 Weeks)" and "Downloaded Package Versions (Last 6 Weeks)."

## Downloaded Packages

This report shows the total download numbers for all versions of a package. The full report includes the top 100 packages over the last 6 weeks. It's no shock that packages like jQuery, Newtonsoft.Json, jQuery.Validation, and Microsoft.AspNet.Razor are at the top. But since the report is based on the last 6 weeks, instead of all time, we expect to see more changes on this report than on the full list of packages.

![downloaded packages][2]

## Downloaded Package Versions

This report shows the download numbers for a single version of a package. This full report includes the top 500 package versions over the last 6 weeks, and a package can show up multiple times if it has had more than one frequently downloaded version. From here, we can learn more about which versions of packages developers are using and how long it takes a new version to overcome an older one.

![downloaded package versions][3]

## Version Breakdown for a Package

On the 'Downloaded Packages' report, the download count is a hyperlink. Following that hyperlink for a package will show you a report for the package's downloads by version. This package-specific report lets you see the recent (6 weeks) downloads for any package, to further identify download trends for a package's versions.

![version breakdown][4]

## Statistics for any Package

When viewing the detail page for any package, there's a new 'Package Statistics' link on the left side. This link takes you directly to the version breakdown for that package, showing the download statistics for the last 6 weeks. This data will be different from the download counts displayed directly on the package page, where that is a total of downloads for all time, instead of just the last 6 weeks.

Note that there are not any reports visible only to the package owner. Everyone can see the package statistics for any package.

![package statistics][5]

## Availability

As we first launch the package statistics feature, many packages will not have statistics available right away. You may see a message that states, "Download statistics are not currently available for this package, please check back later." After the deployment of the feature, we are firing off the job to produce statistics reports for all packages. We expect this to take about a day. So if your package's statistics aren't yet available, just check back again later.

After the initial release of the feature, there will be a job running nightly to refresh every package's statistics reports.

## Feedback and Input

As mentioned, this is just the beginning of package statistics. We expect other reports to highlight which NuGet operation caused the download, whether it was a fresh Install, an Update, or a Package Restore. We are now collecting that data and in a handful of weeks we'll be able to report on it. We also expect reports that bring in dependency graph data, packages that have been recently updated, and more.

If you have feedback or input for what statistics would be useful to you, either as a package consumer or package author, please let us know. The best way is to file an issue [on the repository][6].

 [1]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/statistics-tab.png
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/downloaded-packages.png
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/downloaded-package-versions.png
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/version-breakdown.png
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/package-statistics.png
 [6]: https://github.com/nuget/nugetgallery