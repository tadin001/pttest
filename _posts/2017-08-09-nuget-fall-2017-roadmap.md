---
ID: 124
post_title: NuGet Fall 2017 Roadmap
author: Unni Ravindranathan
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-fall-2017-roadmap/
published: true
post_date: 2017-08-09 00:00:00
---
Over the past 6 months, the NuGet team has been working hard to ensure the growth of the .NET ecosystem. NuGet has grown significantly during this timeframe: NuGet.org is closing in on 4 billion packages served (up from [a billion packages][1] just a year ago), we have enabled new .NET scenarios such as .NET Standard 2.0, and have improved the overall user experience of package authors and consumers using the service. Yet, a lot more remains to be done.

Along the way, we made some mistakes that our community has rightfully called us out on. We made changes to the client and the server that took some users by surprise. We also realized that we did not have a set of principles we would follow when it comes to nurturing the community around NuGet. While we still don’t have the answers to some of the questions asked of us, one thing is very clear – we need to significantly increase the transparency in what we do and when we do it, and we will start with this post.

## Our roadmap

Our top priorities over the next few months will be to improve security, performance, and some user experiences as called out below. For each of these experiences, we will be posting detailed specifications, including a call to action to the community to offer us feedback before we begin implementation. For now, we have created placeholders for the specifications as well as an issue to discuss the feature once the draft spec has been published.

## Security

[NuGet.org][2] usage is growing fast, which is great, but it means our security profile is getting bigger – enough to attract malicious users. Many of the changes required to enforce security can be potentially destabilizing – significant changes to the protocol, increased friction in the submission process, disruption to automation and so on. Ensuring the NuGet experience remains trustworthy and secure must be a top priority, and you'll find that will be reflected in our near-term roadmap. While we work through these changes, we are committed to minimizing disruption to the NuGet community by being transparent with our plans and design changes, as well as providing enough of a notice before any functionality gets enforced.

## Package signing

The NuGet team, and the broader community, has been debating the idea of enforcing signing requirements on packages authors and consumers. The package signing feature will enable consumers to verify the identity of the author and the integrity of the contents of the package. While we are heavily leaning towards the use of X.509 certificates for signing packages, we will also consider alternatives such as blockchains. We understand that acquiring and managing certificates can be both expensive and cumbersome to the various NuGet workflows, and we are evaluating various options to smoothen this process. We are currently in the process of collating all the good discussions that have been happening, as well as our own thoughts, and will post an initial design proposal on this topic soon at the following link. [[Feature specification][3]]

## Two-factor authentication for NuGet.org access

NuGet.org currently supports a home-grown authentication mechanism, which is not sustainable in the long term. We want to transition the NuGet.org authors to more robust and scalable solutions such as Microsoft Account (MSA) or Azure Active Directory (AAD). We will require users to enable Two-factor authentication (2FA) before they can access NuGet.org to publish new packages or new versions of existing packages (the experience for downloading packages will remain unchanged). We will also consider additional ways to securely push packages via the command line tools that are more resilient to unintentional disclosure of API keys. [[Feature specification][4]]

## Package ID prefix reservation

There are currently several identifying properties for NuGet packages originating from NuGet.org, including package title, package ID, package owner, and package author. There are several cases throughout NuGet.org where identifying properties of a package can be misaligned (e.g. author is listed as “Microsoft”, but no owners of the package are from Microsoft). To provide NuGet package authors with an ability to protect their IP, and to empower NuGet package consumers with more information, we are implementing a feature for package owners to reserve a package ID prefix. Packages that meet the right criteria will have a visual indicator to tell package consumers that the package is authentic. [[Feature specification][5]]

## Improving search

When a user searches for a package either in a NuGet client or on NuGet.org, we list packages that are often not relevant in the context of the user project. We want to improve our search results and project applicability information, so you always know what to expect out a package before you install it. [[Feature specification][6]]

## Repeatable builds

When users define floating package dependencies in their projects, consecutive builds (restores) may have different package versions if new package versions get uploaded to a NuGet feed in between these builds. We will ensure that package restore graph information is preserved across multiple builds of a project, or even across multiple projects in a repository, thereby giving users control on the restore outcomes and increasing predictability of builds. [[Feature specification][7]]

## A vibrant NuGet ecosystem

Over the past 6 months, the NuGet team (and me personally) have learnt a great deal about working more effectively with the OSS community. Whether it was the way we started the conversation around [reserving package prefixes][8] or the way we handled the [Paket pull request][9], we should have done a lot better. We remain fully committed to growing the NuGet ecosystem, and over the next few weeks, will work with the community to articulate an approach that balances our extensibility and our agility to deliver some of the experiences mentioned above.

The publication of this roadmap is just a first step in our journey. Please expect to see a lot more details on each of the items over the next few weeks. If you have feedback on any of these items, we recommend that you use the GitHub issue linked to the item, or create a new one.

 [1]: http://blog.nuget.org/20160510/The-1st-Billion.1.html
 [2]: https://nuget.org/
 [3]: https://aka.ms/nuget-pkgsigning
 [4]: https://aka.ms/nuget-2fa
 [5]: https://aka.ms/nuget-pkg-id-reservation
 [6]: https://aka.ms/nuget-contextualsearch
 [7]: https://aka.ms/nuget-rptbuilds
 [8]: https://github.com/NuGet/Home/issues/5307
 [9]: https://github.com/NuGet/NuGetGallery/pull/4437