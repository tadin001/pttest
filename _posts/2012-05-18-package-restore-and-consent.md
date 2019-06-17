---
ID: 49
post_title: Package Restore and Consent
author: Howard Dierking
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/package-restore-and-consent/
published: true
post_date: 2012-05-18 00:00:00
---
A little while back, our team had the pleasure of talking about NuGet with some privacy experts. These are folks who are passionate about your privacy and laws that protect your privacy.

As we were describing NuGet's package restore feature, the privacy experts became interested in the details surrounding the package restore workflow – particularly the part where NuGet goes online to the NuGet gallery to fetch missing packages. Here’s the scenario that raised their interest:

1.  Developer A creates a project, installs NuGet packages, and enables package restore
2.  Developer A checks the project into version control. The NuGet packages aren't included because of the package restore setting
3.  Developer B checks the project out of source control and builds
4.  Because package restore is enabled, NuGet goes online to the gallery, downloads and installs the required packages, and continues with the build.

That last point is where the concern was raised. In this scenario, Developer B has never said to NuGet, "Sure, you can go online on my behalf." This can be a big deal because the simple act of making a request over the Internet can reveal information about the user (for example, from the user’s IP address, we can approximate her location). In some regions and countries, revealing this kind of information [is a big no-no][1].

Now, none of us are lawyers and certainly not the type that work in international privacy issues. We just want to build a great product that developers can feel confident using, regardless of where they are. Hence the package restore consent feature.

## What did we do exactly?

Our goal is that when someone builds a project that uses NuGet package restore, the person overseeing the build provides explicit consent to enable package restore to go online to download NuGet packages.

You’ll note that I was specific about who needs to provide the consent. When we were initially brainstorming solutions, we had talked about simply writing the consent flag into the NuGet config file or into build targets. However, these options would enable Developer A to effectively consent on behalf of Developer B, because these files are pulled down from version control, and that would defeat the purpose of requiring explicit consent. Therefore, we’ve provided 2 ways to specify package restore consent. The first approach is to check the box in NuGet’s package manager configuration dialog box as follows:

![Package restore consent in the package manager configuration dialog][2]

Checking this box will write the following XML into your NuGet.Config file (in your user profile directory tree)

<pre>&lt;packageRestore&gt; 
   &lt;add key="enabled" value="true" /&gt; 
&lt;/packageRestore&gt;</pre>

For scenarios where developers are building on their own machines, this will be the way to provide consent for package restore. However, there are cases where it's not actually a person who's doing the building and who therefore can't provide consent this way. (And where Visual Studio isn't even installed.) The prototypical example is a build server. In that case, NuGet will also look for an environment variable called EnableNuGetPackageRestore. To enable package restore for scenarios where the Visual Studio option is not practical, set this variable to true.

## What if I haven’t given consent?

If you try to build a project that uses package restore and you haven’t given consent to NuGet to download packages, you will get a build error that looks like the following:

![Build error when package restore consent is not given][3]

The build error describes the issue and provides instructions for providing consent. As the package restore consent feature rolls out, you will probably see this error, particularly on build servers, so we wanted to give you advance warning in this post.

## It’s coming

In NuGet 1.8, we’ve added all of the logic for supporting the consent requirement. However, we won’t start actively enforcing the constraint (that is, causing build failures) until NuGet 2.0. This delay gives you time to provide consent for both your developer machines and your build infrastructure, and will help avoid sudden and unexpected build breaks when we release NuGet 2.0.

 [1]: http://en.wikipedia.org/wiki/Data_Protection_Directive
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/pr-consent-configdlg.png
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/pr-consent-builderror.png