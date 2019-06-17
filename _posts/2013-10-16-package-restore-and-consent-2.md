---
ID: 173
post_title: Package Restore and Consent Changes
author: Howard Dierking
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/package-restore-and-consent-2/
published: true
post_date: 2013-10-16 00:00:00
---
When NuGet 2.0 released, it included the requirement that in order to use package restore, you first [needed to explicitly provide consent][1] to the NuGet client to download packages over the Internet. This requirement added some friction to normal developer workflows, and it added more friction in build and continuous integration sever scenarios. Needless to say, the feedback was not positive, and since releasing 2.0, we’ve been working with various groups to remove this friction.

With NuGet 2.7, we have been able to remove the explicit consent requirement by making consent the default behavior and providing a means to explicitly “opt-out”. If you do not wish to have NuGet download packages from the Internet, you can disable the behavior by unselecting one or both of the checkboxes in the Package Manager settings dialog.

![Package Manager Configuration Dialog][2]

Additionally, we have [documented a known issue][3] and work around where it may appear that the default consent behavior has not changed in a clean installation of Visual Studio 2013. 

Thanks for bearing with us through any friction that the explicit consent requirement added to your work, and we hope that you’ll enjoy the freedom from that requirement going forward!

 [1]: http://blog.nuget.org/20120518/package-restore-and-consent.html
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/package-restore-consent-changes-dialog.png
 [3]: http://blog.nuget.org/20130829/nuget-2.7-package-restore-consent-errors.html