---
ID: 123
post_title: NuGet.org Gets a Facelift
author: Jon Chu
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-gallery-gets-a-facelift/
published: true
post_date: 2017-07-18 00:00:00
---
It’s been a long time coming, and today we are excited to announce some big changes coming to NuGet.org. With almost 3 million monthly page views, NuGet.org is the gateway for .NET developers to find packages that accelerate their projects. However, as a package management website, so much more can be done. 

We decided to give NuGet.org a redesign and use it as a platform to build out new functionality. We’ve already begun to pull in a couple of new features, and have a bunch in the pipeline or planned for the future.

In this post, we’ll discuss 3 things:

1.  The motivations for the redesign and how your feedback guided our decisions
2.  An overview of what we’ve changed and added to the website
3.  A sneak peak of some other features that are currently being worked on 

We encourage you to try out the [NuGet.org Preview site][1] today! 

![New NuGet.org][2]

## Feedback and inspiration

Over the past couple of months, we’ve been talking to you, and hearing your feedback on the current website and gathering ideas on what a modern NuGet.org should look like and do. Some of the common themes we’ve heard in our discussions include:

1.  The website looks old and dated
2.  Current UI makes it hard to find existing package information to judge the legitimacy of a package
3.  Package info on the NuGet website can be lacking and makes it hard to gauge package viability

Additionally, after going through accessibility testing, we found a lot of bugs with our current site. Issues ranged from pages impossible to navigate with a keyboard to colors lacking contrast. We knew that we had to do better. 

## Overview of changes

With all the feedback in mind, we set out to create the new, fully-accessible NuGet website that empowers you to easily discover, understand, and use the packages you need.

### Accessibility fixes

We are committed to making sure that NuGet.org is fully accessible to all users. We addressed many accessibility issues for this preview in accordance with [Section 508 Laws][3] and [Web Content Accessibility Guidelines (WCAG 2.0 AA)][4]. If you find any accessibility issues, please let us know by [filing an issue on GitHub][5].

Some of the changes we’ve made include:

*   Updating tab ordering on all pages to ensure proper keyboard navigation 
*   Setting ARIA attributes on elements to make sure screen-reader technologies work on the website
*   Ensuring all interactive elements have visual feedback for when they are focused by the keyboard
*   Setting text colors to properly contrast with backgrounds with a minimum ratio of 4.5:1

![Accessibility Demo][6]

### More prominent package details

During our discussions, we heard from you what information is important when determining the viability of a package. The importance of various aspects like freshness, download count, license type, access to a project site, and many more influenced the design of a more streamlined package details page. 

![Package Details Page][7]

We created sections to divide package details to make it easier to parse through all the information to get a better understanding of a package. All the vital information is placed at the top of the page to be seen first when navigating to the page. Additionally, the project site now displays as the full URL (if it is under a certain number of characters), publish date is more readable, and license type is made more prominent. 

Now that NuGet is supported by a variety of consoles, we wanted to support the many ways users can add packages to their projects. We’ve made it easier to copy the command to install a package, and added a new tab for the command to install using the .NET CLI. 

![CLI Support Demo][8]

### Streamlined account settings

We’ve made it easier for users to access their account settings, moving away from a centralized account page that combined non-related settings together. Now, API keys and package management controls can be accessed from any page with the account dropdown. It’s much easier for authors to manage their packages.

![New Account Settings Menu][9]

### Easier package management

For package authors, we’ve made it easier to edit and manage packages. The “Manage Packages” page is more descriptive for each package, showing information that mirrors the search results page. 

![Manage Packages][10]

Additionally, we’ve improved the workflow for editing and listing a specific package version by enabling users to select a version of a package from a dropdown, rather than having to navigate to a specific version’s details page. 

### Giving credit to you, the community

Ever wonder why the [About page][11] seemed to have such a small amount of Gallery contributors, when there were definitely more people involved? We did too! So, we made sure everyone who has ever contributed to the [NuGet Gallery repo on GitHub][5] is properly thanked and shown on our About page. 

![About Page][12]

We wouldn’t be anything without our community, and we wanted to show how thankful we are for your contributions. 

### Updates to our blog and status page

Since we redesigned the main NuGet website, it only makes sense that we give the [blog][13] and [status][14] pages some love as well. We aligned the pages with the design of the new website to ensure consistency across all of NuGet.org. We cleaned up the blog to make it easier to browse for posts, and made the status page easier to understand and find operational issues. 

## What’s in the works?

Like we said before, we didn’t want to just change pixels on the website. This is not a one-time pass to leave until we need to do another redesign in a couple of years. We’re taking this opportunity to be more iterative on the website and continue to build out and bring new features that improve your experience. As such we have a couple of features that we’ve begun working on that will come to NuGet.org in the coming months. 

### Package documentation integration

We get that packages don’t always offer enough details about what the package is and how to use it. As such we want to give users more information about packages by integrating ReadMe content. 

![Documentation on Package Details][15]

Upon package upload, users will be able to upload or write a ReadMe file to attach to their package. Alternatively, they can link their package to a repository that will pull the ReadMe automatically. This will enable package consumers to get a better sense of a package without having to go to a package’s project site, and empower package authors to create content to aid in the adoption of their packages. 

### Status banner

Being notified of the operational status is important for any service. For NuGet, we want to make it easier for users to see if we are experience issues right from the homepage rather than trying to find the [Status page][14].

![Status Banner][16]

### Upcoming backlog

Additionally, we have a many more features in our backlog that we want to address in future updates to NuGet.org. Some items on our backlog include:

*   Making search better by improving the relevance of returned results and offering advanced search options (e.g. Target Framework, Author, etc.)
*   Adding deeper repository integration by showing repo statistics (open pull requests, last commit, star counts, etc.)

## Conclusion

We’re really excited to share this new experience with you, so please give the [NuGet.org Preview site][1] a try! 

We'd love to hear any feedback you have on the website, or things you'd like to see. Fill out our [feedback survey][17], or leave a comment below. If you discover any bugs with the preview site, [file an issue on GitHub][5]. 

While we work on incorporating your feedback, we will continue to maintain [NuGet.org][18] in case you find any workflow blockers. We’ll be taking the next couple of weeks to respond to your feedback and will be continuously pushing updates to the preview site. We plan to integrate the new design into NuGet.org in August.

This is just the first step, and we’re excited to continue to bring new features to NuGet.org to make your experience discovering, using, and managing packages better.

 [1]: https://preview.nuget.org
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-Home-New.png
 [3]: https://www.section508.gov/content/learn/laws-and-policies
 [4]: https://www.w3.org/TR/WCAG20/
 [5]: https://github.com/NuGet/NuGetGallery
 [6]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-Accessibility-Focus.gif
 [7]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-PackageInfo-Overview.png
 [8]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-PackageInfo-CLI.gif
 [9]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-AccountSettings.gif
 [10]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-ManagePackages.png
 [11]: https://www.nuget.org/policies/About
 [12]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-About.png
 [13]: http://blog.nuget.org
 [14]: https://status.nuget.org
 [15]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-Packages-Documentation.png
 [16]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/NuGet-Gallery-ServiceWarning.png
 [17]: https://www.research.net/r/XP9GS73
 [18]: https://www.nuget.org