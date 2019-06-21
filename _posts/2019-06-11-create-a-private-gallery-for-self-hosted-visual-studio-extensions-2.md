---
ID: 226190
post_title: >
  Create a private gallery for self-hosted
  Visual Studio extensions
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/create-a-private-gallery-for-self-hosted-visual-studio-extensions-2/
published: true
post_date: 2019-06-11 08:00:17
---
Most Visual Studio extension authors publish their extensions to the public Marketplace to allow everyone to install them and benefit from the large and open ecosystem. However, some companies create extensions for internal use only. A private gallery allows them to distribute the extensions easily with the same auto-update capabilities enjoyed by any public Marketplace extension. And now, we've streamlined the process even more so that you can easily create a private gallery for your team or organization. <img class="alignnone size-full wp-image-225679" src="https://devblogs.microsoft.com/visualstudio/wp-content/uploads/sites/4/2019/06/private-gallery-online-tab.png" alt="" width="933" height="382" /> Visual Studio 2010 introduced support for private galleries, but few used them due to lacking samples and tooling. A lot has changed since then and the private gallery support has seen several updates to support extension packs and other more recent features.

## The anatomy of a private gallery A private gallery is an

[ATOM feed XML File][1] that contains meta data about the extensions. Registering the gallery with Visual Studio can be done either by the user manually under **Tools -> Options** or by an extension using a custom .pkgdef file ([example][2]). <img class="alignnone size-full wp-image-225678" src="https://devblogs.microsoft.com/visualstudio/wp-content/uploads/sites/4/2019/06/extension-options.png" alt="" width="967" height="696" /> The ATOM feed can be located on a web server, file system or file share. After registering the gallery, a new category appears under the *Online* tab in the Extension Manager dialog as shown in the first screenshot above.

## Create the ATOM feed The open source tool

[Private Gallery Creator][3] makes it straightforward to create the ATOM feed. Download the executable and run it in a folder containing the VSIX files you wish to include in the feed. The tool analyzes the VSIX files and extracts the metadata needed to produce a file called feed.xml in the same folder. You could also set up a CI/CD pipeline that automatically executes the tool to update the feed. In addition, the tool has a “watch” feature to automatically produce a new feed any time a VSIX file is added or modified in the same folder.

## Set it up in only four steps Here’s a recap of how to set up a private gallery:

1.  Put your .vsix files into an empty folder accessible to all consumers of the gallery
2.  [Download PrivateGalleryCreator.exe][4] executable into the same folder
3.  Double-click PrivateGalleryCreator.exe to produce the feed.xml file
4.  Register the feed in Visual Studio manually or from an extension’s .pkgdef file

## In summary You can learn more about private galleries from the

[documentation][5] and by checking the [Private Gallery Creator project on GitHub][4]. There are a few public offerings for hosted private galleries such as [MyGet][6] and [Open VSIX Gallery][7] that may be worth looking into as well. We’d love to hear about how you use private galleries today or why you don’t use them, so please sound off in the comments below.

 [1]: https://docs.microsoft.com/visualstudio/extensibility/how-to-create-an-atom-feed-for-a-private-gallery
 [2]: https://github.com/madskristensen/VsixGalleryExtension/blob/master/src/feed.pkgdef
 [3]: https://github.com/madskristensen/PrivateGalleryCreator
 [4]: https://github.com/madskristensen/PrivateGalleryCreator/releases
 [5]: https://docs.microsoft.com/visualstudio/extensibility/private-galleries
 [6]: https://www.myget.org/vsix
 [7]: http://vsixgallery.com/