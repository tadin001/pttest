---
ID: 113
post_title: Changes to Expiring API Keys
author: Harikrishna Menon
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/changes-to-expiring-api-keys/
published: true
post_date: 2016-08-25 00:00:00
---
In June, we published a blog post announcing [Expiring API Keys][1]. We received a lot of great feedback from the community about it. In retrospect, we did not do a great job explaining the motivation and reasoning for this security measure to the community. 

This post goes into more detail about why we introduced Expiring API Keys, the immediate changes we are making to the design to address feedback, and improvements to our process to prevent a similar situation from happening in the future.

## Your Feedback

Let’s start with a summary of the most consistent feedback we heard from the initial plan:

*   Without automated renewal mechanisms, this feature would break CI builds and automation.
*   Large organizations, such as Serilog which has over 60 repos, would find it difficult to co-ordinate and ingest Expiring API Keys because their projects use a variety of CI and build systems. 
*   Some packages are updated infrequently (only 3-4 times a year.) A 90-day expiration would cause them to have to renew the key each and every time they update. 
*   Parallels with other systems were drawn (GitHub, Lets Encrypt, NPM, etc.) to point out that they were not imposing such measures in the name of security and if they did, they had auto-renewal mechanisms in place to address CI scenarios.
*   This introduces additional friction which is unnecessary for my scenario. 

Thanks for this great feedback. It caused us to press “pause” on our efforts and re-think the problem of leaked API keys and the plan for it.

## Why Expiring API Keys?

Over the past few years, you have probably read stories about accounts being hacked on various online sites. Almost everyone has received a notification that you need to change your password on a site. Online security is a real challenge. API keys and username/passwords are not the same, but they do have a lot of similarities and have a shared set of risks on leaks and theft. 

This change was envisioned to solve 2 problems that we have today in NuGet.org.

### Leaked Keys

Leaked keys are a security hole like any another leaked credential. If a developer of a popular package has their API key stored on their box, CI machines or personal accounts and their systems or accounts gets compromised, it would be trivial for the attacker to take advantage of this leak and publish malicious content by updating a popular package. While some key leaks are exploited immediately, some attackers hold on to keys until the circumstances are in their favor to exploit it for the maximum gain. 

### Stale Keys

The NuGet ecosystem is about 5 years old now. There are many NuGet.org API keys that have not been used for greater than a year and a smaller set that have never been used to push packages. Many of the accounts tied to these keys could be dormant and might be unmonitored by the original account owners. 

If any of these dormant accounts or API keys are compromised, attackers can push updates to existing packages or create new packages that contain malicious content from these accounts.

Rotating credentials after a set time period is a proven way to minimize this threat if not completely neutralize it. An example of this would be rotating passwords for users and service accounts in enterprises.

## Immediate changes to the feature based on your feedback

*   Starting today, existing API keys that are used at least once each year will never expire. This approach enables the most active package maintainers to effectively opt-out of API key expiration. You can set expiry between 1 day and a year when you create or renew an API key. 
*   We will expire all API keys that have been unused for a year immediately. This change will prevent dormant and unmonitored accounts from publishing malicious packages.

## What’s coming next?

While the plan above addresses some of the feedback that we got from the community and some of our goals in the short term, it does not address all the feedback. 

We are working on incorporating more pieces of feedback that like auto renewal mechanisms and multiple API keys. We will publish a blog post in the next month or so detailing our proposed design.

## How are we changing rollout of security features?

In hindsight, given the scope of the feature and the impact to the ecosystem, we should have started by having the discussion with the community before rolling this out.

Some security related features and changes cannot be disclosed in advance to protect operational security and minimize the chances of an attack. But there are others like Expiring API keys, that can be shared to get feedback before rolling it out to NuGet.org. We promise to do better in this regard in the future.

## We want to hear your feedback!

If you have more specific feedback on this topic, we have a GitHub issue open [here][2] where we can continue the discussion.

We want NuGet to meet the evolving needs of our community. If you would like to send us an email, hit us up at <feedback@nuget.org>. You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][3].

 [1]: http://blog.nuget.org/20160622/NuGet-API-key-expiration.html
 [2]: https://github.com/NuGet/NuGetGallery/issues/3092
 [3]: https://github.com/Nuget/Home/issues