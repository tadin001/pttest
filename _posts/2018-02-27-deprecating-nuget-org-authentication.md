---
ID: 226129
post_title: Deprecating NuGet.org authentication
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/deprecating-nuget-org-authentication/
published: true
post_date: 2018-02-27 00:00:00
---
As announced in our [NuGet Fall 2017 Roadmap blog post][1], we are transitioning away from NuGet.org’s home-grown authentication mechanism which will eventually allow us to add support for additional security systems such as two-factor authentication (2-FA). In preparation for this transition, we had already added support for Microsoft accounts (MSA) to sign in to NuGet.org and are now announcing support for Azure Active Directory (AAD) that can be used to sign in to NuGet.org. We recommend that all NuGet.org publishers start using either MSA or AAD to manage their accounts as soon as possible. We will ensure a smooth transition for existing accounts using a phased roll out approach and at no point will your existing packages, API keys and other account settings be impacted. Read on for further details.

## Creating new NuGet.org accounts

We plan to remove new registrations using the NuGet.org’s username/password-based authentication mechanism shortly. Going forward, you will need to use your existing or new MSA or AAD credentials to sign up for the NuGet.org functionality. Please note that you can do this today and is our strong recommendation – just that we will remove the alternative NuGet.org username/password method.

## Using existing NuGet.org credentials

Starting today, when you sign in to NuGet.org using the username/password method, you will see a new warning message that asks you to link your account to an MSA or an AAD. Once you link your account to an MSA/AAD, the NuGet.org username/password option will be disabled and removed. All your existing settings such as API keys will be retained through this transition.

As a part of the transition, we will no longer allow multiple MSAs linked to your NuGet.org account. We understand that some users use this mechanism to share package access across a group/team. To address this scenario, we will shortly be introducing the support for creating [Organizations on NuGet.org][2] that will help users manage packages as a group. We recommend that you wait until the availability of the Organizations feature on NuGet.org before adding or changing any linked MSA/AAD accounts if you care about retaining multiple MSA/AAD credentials linked to your NuGet.org account. The transition step will only allow a single MSA or AAD credential linked to a NuGet.org account.

**We will disable NuGet.org username/password login for all users on May 15th, 2018**. Beyond this date, you will not be able to perform any operations on NuGet.org unless you complete the transition step.

## We want to hear your feedback!

If you have any question, comment or feedback you can reach out to me by commenting on the blog, emailing at <anangaur@microsoft.com> or by tagging [@nuget][3] in your tweets. You can also create an issue on our [Github repository][4]. We will be sure to announce any changes or updates regarding the key dates on our [NuGet/Announcements][5] repo.

 [1]: https://blog.nuget.org/20170809/NuGet-Fall-2017-Roadmap.html
 [2]: https://github.com/NuGet/Announcements/issues/4
 [3]: https://twitter.com/nuget
 [4]: https://github.com/NuGet/Home/issues/new
 [5]: https://github.com/NuGet/Announcements