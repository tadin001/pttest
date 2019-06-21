---
ID: 1018
post_title: Organizations on NuGet.org
author: Serhii Rohatin
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/organizations-on-nuget-org-2/
published: true
post_date: 2018-04-17 00:00:00
---
We are happy to announce support for [Organizations][1] on NuGet.org. This will help businesses and open-source projects collaborate on packages using a single nuget.org identity.

### Why organizations?

NuGet.org used to allow you to create an account and publish packages through that account with little support to manage and publish packages as a team or a group. To overcome the single account - single user limitation, many users shared credentials of the account across the group. Obviously this is not a secure way to collaborate and has no audit trail for package updates across different users. With the [deprecation of NuGet.org's homegrown username/password mechanism][2], sharing credentials would mean sharing your Microsoft account's credential - an absolute non-starter!

Organizations allows you to keep a single identity for all your packages but lets different users collaborate on the organizations' packages. You can be a member of multiple organizations and manage all your packages together by signing in to your user account. At any point in time, you have the audit trail of all the packages' updates across all the [organization's members][3].

**Note**: For a package consumer, an organization account appears same as an existing user account on nuget.org.

### Adding an organization account

[Adding a new organization][4] is very simple - just provide the account name and email address like you would do for creating a user account:

![Add an organization][5]

### Transforming an existing account to an organization

If you want to keep using the existing nuget identity for your packages, you can [transform the existing NuGet.org account to an organization][6] in a matter of few clicks.

### How do I submit packages that belong to the organization?

Once you have created an organization and you are a member of the organization, you can either [directly upload your packages with the organization as the package owner][7] or [create an API key scoped to an organzation][8] and use that API key to [push the packages through the CLI][9].

### Get started with organizations today!

To get started with organizations, go to [Manage Organizations][10] and select [Add new organization][4] or [Transform your account to an organization][6] option that suits your need.

![Organizations][11]

More information about the feature is available on our [documentation][1]. If you have any feedback or suggestions specific to organizations feature, use [NuGet/NuGetGallery#4627][12]. For any other issues or feedback, do let us know by creating a [new issue on our GitHub repo][13]

 [1]: https://docs.microsoft.com/en-us/nuget/reference/organizations-on-nuget-org
 [2]: https://blog.nuget.org/20180227/Deprecating-NuGet-org-authentication.html
 [3]: https://docs.microsoft.com/en-us/nuget/reference/organizations-on-nuget-org#managing-organization-members
 [4]: https://docs.microsoft.com/en-us/nuget/reference/organizations-on-nuget-org#adding-a-new-organization
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/org-add-new-page.png
 [6]: https://docs.microsoft.com/en-us/nuget/reference/organizations-on-nuget-org#transform-existing-account-to-an-organization
 [7]: https://docs.microsoft.com/en-us/nuget/reference/organizations-on-nuget-org#uploading-packages
 [8]: https://docs.microsoft.com/en-us/nuget/reference/organizations-on-nuget-org#using-api-keys
 [9]: https://docs.microsoft.com/en-us/nuget/quickstart/create-and-publish-a-package-using-visual-studio#publish-the-package
 [10]: https://www.nuget.org/account/Organizations
 [11]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/organizations-on-nuget-org.png
 [12]: https://github.com/NuGet/NuGetGallery/issues/4627
 [13]: https://github.com/NuGet/NuGetGallery/issues/new