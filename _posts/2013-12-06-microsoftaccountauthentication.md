---
ID: 176
post_title: >
  Use your Microsoft account to log in to
  NuGet.org!
author: Andrew Stanton-Nurse
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/microsoftaccountauthentication/
published: true
post_date: 2013-12-06 00:00:00
---
Starting today you can use your Microsoft account to log in to NuGet.org. I don't know about you, but I want as few passwords as possible, so any opportunity to get rid of a password is great. Now, your NuGet.org password doesn't have to be one of those many passwords you have to forget/lose/etc.

To associate a Microsoft account with an existing account, make sure you're signed out of NuGet.org and then click the sign-in link at the top right of the page. On this page, click the new "Sign in with a Microsoft account" button:

![New Sign In Page][1]

Sign in to your Microsoft account and grant us permission to use your account. If you already have a NuGet.org account, AND the email address in your Microsoft account is the same as the one in your NuGet.org account, you'll see this page:

![We found an existing account][2]

Simply type in your current NuGet.org password and you're done! 

If you've never created a NuGet.org account, or if you use a different email address for your Microsoft account, you'll see the page below. You can either give us a username and email address to create a new account, or if you do already have an account (with a different email address) you can sign in to it and we'll associate the two accounts.

![Do we know you?][3]

**NOTE:** We will not use the email address attached to your Microsoft account for any notification purposes. We only use the email address you enter and confirm when registering.

Once you've associated your account, any time you click "Sign in with a Microsoft account" and are already logged in to your Microsoft account, you'll be immediately signed in to NuGet.org! You can review your login credentials on the new Account management page, by clicking on your username at the top right when logged in to NuGet.org. When you associate a Microsoft account, you'll be able to click the "Remove" button next to "Password Login" and we'll delete the password from our system.

![Account Management Page][4]

If you decide you want to re-enable password login for some reason, you can do that from the account page too:

![Re-enable Password][5]

We'll send you an email and you can use the link in that message to set a new password (just like we do if you forget your password). Once you've re-enabled password login, you can remove your Microsoft account if you want. Of course, we never let you remove your last login credential.

If you do lose access to your account (for example, your Microsoft account is hijacked, hacked or lost), you can still use the "Forgot My Password" link or [contact us for assistance][6].

![Forgot My Password][7]

**NOTE: None of this changes the way you interact with nuget.exe**, you still use an API Key that you get from the Account page. This may change over time, but we will continue support existing API Keys.

## Why Microsoft account? What about other providers?

You may be wondering why we chose Microsoft accounts and what our plans are for other providers. To put it simply, we chose Microsoft account, because of this:

![Visual Studio Microsoft account login][8]

Starting in Visual Studio 2013, you log in to Visual Studio with a Microsoft account. In the future, we plan to integrate more with this system so that if you are logged in to Visual Studio with the same Microsoft account you use on NuGet.org, we can provide you with a customized experience. Imagine seeing lists of your favorite packages, custom feeds you design, etc.

As for other providers, we are open to the idea, but we didn't feel that any providers added much value since only Microsoft accounts are supported in Visual Studio. NuGet is the package manager for the Microsoft platform, and we expect most users are using Visual Studio and will have a Microsoft account. Having said that, we definitely recognize that not everyone uses Visual Studio, and there may be value in adding support for other providers. Since NuGet is an **open-source project**, we are very happy to hear from you if you feel we should add support for another provider. If you really want another provider supported, file an issue on our [Issue Tracker][9].

## Coming Soon: Persistent Sessions

The next major authentication feature we plan to add is "persistent sessions". The idea here is that once you log in on a machine, you remain logged in until you sign out. Users have been asking for this for a long time, and we have been listening. However, it's not as simple as just "setting a boolean flag" to make your login persistent. Before persistent sessions can be introduced we need a few things. First, we need a feature like GitHub's ["sudo mode"][10] so that we can force users to re-authenticate before performing dangerous operations (like changing passwords) to reduce the risk of account hijacking when a user leaves a machine unattended. Second, we need a way for users to manage user sessions so they can revoke access from devices they have lost access to. Our initial plan for this second feature is to just sign you out of all sessions when you change your password (so if you lose access to a device, just change your password, even if you set it to the same thing again). In the future, we will likely have a feature where you can view a summary of all the devices you log in to NuGet.org with as well as how and when you last logged in. Something like GitHub's Security History page:

![GitHub Security History][11]

These issues require a little extra design and implementation, but they're near the top of our queue of things to do now, so expect to see them soon. I should note that if you have a Microsoft account, you **do** still have to click Sign In every time you restart your browser, but since Microsoft accounts have persistent sessions you **don't** have to enter your password again! So we're getting close!

## A small breaking change

As part of this change, we have broken a small part of our API. We had an "accidental feature" (i.e. it wasn't intended ;)) where you could call our API and pass the API key in as a query string parameter, like this:

    POST https://www.nuget.org/api/v2/package?apikey=...

We have disabled this feature in this change as it was unintentional and undocumented. None of the NuGet tools use this feature, so as long as you're using NuGet.Core, you shouldn't have any issues, but if you do, just change your code to pass the API Key in the body of the "X-NuGet-ApiKey" header (which was the intended mechanism for providing API Keys) instead. If you have any concerns about this, please post an issue in our [Issue Tracker][9]

 [1]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/1-signinwithmsa.png
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/2-existingaccount.png
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/3-doweknowyou.png
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/4-accountpage.png
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/5-enablepassword.png
 [6]: https://www.nuget.org/policies/Contact
 [7]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/6-forgotpassword.png
 [8]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/7-whymsa.png
 [9]: https://github.com/NuGet/NuGetGallery/issues
 [10]: https://github.com/blog/1513-introducing-github-sudo-mode
 [11]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/8-githubsessions.png