---
ID: 120
post_title: Introducing scoped API keys
author: Anand Gaurav
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/introducing-scoped-api-keys/
published: true
post_date: 2017-02-02 00:00:00
---
**Update 2/14 (05:00 PM PST): This feature is now live! Login to your [nuget account][1] and expand the *API Keys* section to see the new experience.**

Since last year, we have been working on several fronts to advance NuGet as a secure environment for package distribution. This post describes an experience that will allow you to have better control of API keys that you use to push packages. Please note that this experience is currently only available on our staging environment and will be deployed to Nuget.org once we have incorporated your feedback, as described below.

## Feature summary

This feature will give you better control on your APIs as you can now:

*   [Create multiple scoped API keys][2] that can be used for different packages with varying expiration timeframes.
*   [Obtain API keys securely][3].
*   [Edit existing API keys][4] to change package applicability.
*   [Refresh or delete existing API keys][5] without hampering operations using other keys.

**We would love for you to try this feature and give us feedback!**

Go to <https://int.nugettest.org> to try the feature. Since this is a staging environment, you might have to create a new account and push new packages to try out the feature. Also, to set API key and then to push packages, you have to use `-Source` option as specified below:

    nuget.exe setApiKey [your API key] -Source https://int.nugettest.org/api/v2/package 
    nuget.exe push MyPackage.1.0.nupkg -Source https://int.nugettest.org/api/v2/package

Share your feedback with us via comments in the blog, filing a [Github issue][6] or sending an [email to our team][7].

## Why introduce the concept of scoped API keys?

We made some [changes to API keys][8] last year. We want to further that experience with the introduction of the concept of scopes for API keys that will allow you to have more fine-grained permissions. Today, NuGet offers a single API key for an account, and that approach has several drawbacks:

*   **One API key to control all packages:** With a single API key that is used to manage all packages, it becomes really difficult to securely share the key when multiple developers are involved with different packages, and share a publisher account. An example of an account where we see a lot of pain on this front is our own Microsoft account that we use to push hundreds of packages across many teams.
*   **All permissions or none:** Anyone with access to the API key has all permissions (publish, push and un-list) on the packages. This is often not desirable in a multiple developer/team environment.
*   **Single point of failure:** A single API key also means a single point of failure. If the key is compromised, all packages associated with the account could potentially be compromised. Refreshing the API key is the only way to plug the leak which results in an interruption to your CI/CD workflow. In addition, there may be cases when you want to revoke access to the API key for an individual (for example, when an employee leaves the organization). There isn't a clean way to handle this today.

With scoped API keys, we have tried to address the problems stated above and at the same time, we have made sure that none of the existing workflows will break.

## <a name="create-scoped-api-keys"></a>Create scoped API keys

You can now create multiple API keys based on your requirements. An API key can apply to one or more packages, have varying scopes that grant specific privileges, and have an expiration date associated with it.

![overview][9]

In the screenshot above, you can have an API key named 'Contoso service CI' that can be used to push packages for certain 'Contoso.Service’ packages, and is valid for 365 days. This is a typical scenario where different teams within the same organization work on different packages and the members of the team are provided the key which grants them privileges only on the package they are working on. The expiration serves as a mechanism to prevent stale or forgotten keys.

### Using glob patterns

If you are working on multiple packages and have a large list of packages to manage, you may choose to use globbing patterns to select multiple packages together. For example, if you wish to grant some key certain scopes on all packages whose ID starts with Fabrikam.Service, you could do so by specifying “fabrikam.service.*” in the Glob pattern text box.

![Glob patterns][10]

Using glob patterns to determine API key permissions will also apply to new packages matching the glob pattern. For example, if you try to push a new package named ‘Fabrikam.Service.Framework’, you can do so with the key you would have created in the above step, since the package matches the glob pattern “fabrikam.service*”

## <a name="obtain-api-keys"></a>Obtain API keys securely

For security, a newly created key is never shown on the screen and is only available with the copy button. Similarly, it will not be accessible again after the page is refreshed.

![Obtain API keys][11]

## <a name="edit-existing-api-keys"></a>Edit existing API keys

Another use case is being able to update the key permissions and scopes without changing the key itself. If you have a key with specific scope(s) for a single package, you may choose to apply the same scope(s) on one or many other packages.

![Edit API keys][12]

## <a name="refresh-or-delete-existing-api-keys"></a>Refresh or delete existing API keys

The account owner can choose to refresh the key, in which case the permission (on packages), scope and expiry remain the same but a new key is issued making the old key unusable. This is helpful in dealing with stale keys or where there is any potential of an API key leakage.

![Refresh API keys][13]

You can also choose to delete these keys if they are not needed anymore. Deletion will remove the key and make it unusable.

## FAQs

**What happens to my old (legacy) API key?**

Your old API key (legacy) continues to work and can work as long as you want it to work. However, as was the case before, these keys will be retired if they have not been used for more than 365 days to push a package. Refer to the blog post [changes expiring to API keys][8] for more details. You can no longer refresh this key. You need to delete the legacy key and create a new scoped key, instead.

**Note:** This key has all permissions on all the packages and it never expires. You should consider deleting this key and creating new keys with scoped permissions and definite expiry.

**How many API keys can I create?**

There is no limit on the number of API keys you can create. However, we advise you to keep it to a manageable count so that you do not end up having many stale keys with no knowledge of where and who is using them.

**Can I delete my legacy API key or discontinue using now?**

Yes. You can, and you probably should. We recommend you delete the legacy API key and start using the new key(s) as soon as we move this feature into our production service (this feature is currently only present in our staging environment).

**Can I get back my API key that I deleted by mistake?**

No. Once deleted, you can only create new keys. There is no recovery possible for accidently deleted keys.

**Does the old API key continue to work upon API key refresh?**

No. Once you refresh a key, a new key gets generated that has the same scope, permission and expiry as the old one. The old key ceases to exist.

**Can I give more permissions to an existing API key?**

You cannot modify the scope but you can edit the package list it is applicable to.

**How do I know if any of my keys expired or getting expired?**

If any key expires, we will let you know through a warning message at the top of the page. We also send a warning e-mail to the account holder 10 days before the expiration of the key so that you can act on it well in advance.

![Expired keys][14]

## Next steps

We would love to hear your feedback. Keep them flowing via comments in the blog, filing a [Github issue][6] or by [sending an email][7] to our team. Once we address the feedback, we would like to go live with this feature - preferably in a couple of weeks from today.

 [1]: https://www.nuget.org/account
 [2]: #create-scoped-api-keys
 [3]: #obtain-api-keys
 [4]: #edit-existing-api-keys
 [5]: #refresh-or-delete-existing-api-keys
 [6]: https://github.com/Nuget/nugetgallery/issues
 [7]: mailto:feedback@nuget.org?subject=[Scoped%20API%20keys]%20
 [8]: http://blog.nuget.org/20160825/Changes-to-Expiring-API-Keys.html
 [9]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/intro.png
 [10]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/glob-pattern.png
 [11]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/copy-api-key.png
 [12]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/edit-api-keys.png
 [13]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/refresh-api-key.png
 [14]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/expired-api-key-warning.png