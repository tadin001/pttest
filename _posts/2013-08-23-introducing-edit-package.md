---
ID: 168
post_title: >
  Editing Package Metadata on the NuGet
  Gallery
author: Bhuvaneshwari Krishnamurthi
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/introducing-edit-package/
published: true
post_date: 2013-08-23 00:00:00
---
In our next deployment, which is scheduled for the week of August 26th, 2013, we have added the ability to edit package metadata at any point in time before or after publishing the package. This has been one of the frequent requests from package authors.

The changes are current dpeloyed @ [staging.nuget.org][1]. You can play around with the feature in this sandbox environment. Let us know your feedback and suggestions [here][2] in github. We are aiming production deployment on 8/27/2013 barring any ship-stopper issues. Meanwhile, we will continue testing from our end as well.

## Edit Package

Before the introduction of this feature, clicking on "Edit package" on your package page will give you the below message.

![Old EditPackage][3]

With this new feature, the "Edit package" page will display all of the metadata of the package and lets you to update them.

All the descriptive fields of the package like Title, Description, Summary, Icon Url, Project Url, Authors, Copyright, Tags and Release notes can be edited. However, updates to the functional details of the package like ID,Version, Dependencies and actual the content of the package requires a new version to be uploaded. This is inline with our current behavior. More details can be found in the [spec][4] and the [forum discussion thread][5].

## Editing different versions of a package

You can chose any specific version of the package (not necessarily the latest) and edit its metadata. The "Edit package" page lists the package versions in the left pane making it easier to switch between versions.

![Edit Package Versions][6]

## Saving and undoing edits

Once you update the desired fields of the package and "Save" the edit, the changes will be submitted. It might take a few minutes for the new nupkg file to be created and the changes to reflect in the web page. When the changes are being applied, you (as the package owner) will see the below message in the package page and the new updated data, whereas other users would still see the old data.

![Pending Edit Message][7]

You can click on "Undo pending Edits" link in the notification message to undo all the current edits before it gets applied. You can always make another edit at a later point of time to make further changes.

## Editing while uploading a new package

Package details can be edited while uploading a new package as well. The "Verify Details" page now displays all of the metadata of the package. You can update the details as required and a nupkg file with the modified data will be created and uploaded for you. This will help you in making quick edits to the package before publishing it.

![Edit Package On Upload][8]

## Search/Install/Update on edited package

Once the pending edit goes through, all search/Install/update operations on the package from VS client or the api/v2 feed will reflect the new data and nupkg.

Note, it might take up to 10 minutes for the new changes to be reflected in search results (as the index needs to be updated).

## How it Works

When a package author submits a package, we queue the edit up for being applied. Our backend system processes the queue of edits the order they were submitted, creating an updated NuGet package file (.nupkg), replacing the originally uploaded package and leaving the version number the same. The package originally uploaded, along with its corresponding metadata, are archived in the system.

As a result of the newly created nupkg file, the search index and package source feed are updated as well. This will have downstream effects on consumers of the package source feed as the package's metadata and hash will be updated. From that point forward, users that install the package will get the updated package, while users that installed the package before the edit will obviously have the old package. When package restore is used, the updated package will be downloaded during package restore, seamlessly replacing the original.

The end result is that package authors now have the ability to update package metadata without publishing a new version of the package. We hope this approach will address the many scenarios when package authors have needed the ability, while also not disturbing package consumers in any way.

 [1]: https://staging.nuget.org
 [2]: https://github.com/NuGet/NuGetGallery/issues
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/Old-EditPackage.png
 [4]: http://docs.nuget.org/docs/working-specs/editing-package-descriptions
 [5]: https://github.com/NuGet/NuGetGallery/issues/807
 [6]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/Edit-Package-Versions.png
 [7]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/Pending-Edit-Message.png
 [8]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/Edit-Package-On-Upload.png