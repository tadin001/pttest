---
ID: 126
post_title: NuGet Package Signing
author: Ricardo Minguez (Rido)
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/package-signing-2/
published: true
post_date: 2017-09-14 00:00:00
---
In our [NuGet Fall 2017 Roadmap][1], we highlighted security as the main area of investment over the next few months. This blog post describes a major part of that roadmap in greater detail – package signing.

We started talking about supporting signed packages on NuGet.org a while ago. For example, in 2015 we published a post on [Package Signing][2] as well as a related [specification][3] from the ASP.NET team, and more recently we described package signing as a part of our future plans in the post on [NuGet Package Identity and Trust][4]. We´ve received some great feedback from our various discussions with you (such as the ones in the comments on the GitHub issues [here][5], [here][6] and [here][7]), and we think we are ready to act on it.

To get further feedback from our community before we start implementation, we are publishing this post as a refreshed set of goals and design principles. As we continue to refine our design, we will publish additional technical details in the form of technical specifications that will be announced via our newly setup [announcement repo][8].

We are committed to bringing these various experiences to you in a way that minimizes disruptions to your development or package authoring workflows, and at the same time balances the goal of making NuGet.org a trustworthy destination for all .NET developers to share and acquire packages.

### Package Signing Goals

We have two primary goals we would like to accomplish with package signing:

**Package Integrity**: We want to ensure the package contents have not been modified from the time the package was authored to when a developer downloads it for use in their projects. We also recognize that users copy packages from NuGet.org to multiple locations (such as setting up mirrors or copying them locally) and we want to ensure that such packages have not been modified before consumption.

**Package Authenticity**: We’ve heard from the community that it is often difficult to determine the origin of a package. In a previous [blog post][4], we proposed some resolutions to the problem such as the ability for users to reserve package ID prefixes on NuGet.org. As we called out in that blog post, the next logical step is to address package signing. This will strengthen our package identity solution to provide authenticity on packages across multiple feeds.

### Design Principles

We recognize that NuGet has a very well-established ecosystem of packages that won’t all move overnight, and various NuGet consumers will have differing requirements around the levels of conformance they want to abide by. Hence, we have set up a set of principles that we will use to test any solution with:

*   Changes in the NuGet protocol or its implementation on NuGet.org will ensure **backwards compatibility**. Existing NuGet clients will be able to consume signed or unsigned packages and we should not impact their current workflows around package authoring and consumption.
*   We should not require authors to **resubmit** existing packages. We acknowledge that not every package on NuGet.org is in active development, and not all of them will be signed and resubmitted. 
*   We should **minimize the performance impact** on install and restore operations.
*   We should enable **cross platform support** - Windows, Mac and Linux - using the same protocols and workflows. 
*   We should enable **3rd party servers and clients**. All changes we are proposing to NuGet.org can be implemented by any other NuGet server or client, using well-documented protocol changes.
*   We need to help users make **informed decisions** about which packages/owners to trust. 
*   We should make use of **well-known crypto** algorithms. 
*   We should enable a **seamless and incremental rollout** of the various experiences, not requiring all the tools or packages to be updated at the same time.
*   Packages should be **immutable** once they are signed. We should not depend on workflows that require changes to packages once they have been submitted to NuGet.org.
*   We should be able to support various **automation** scenarios (i.e. CI/CD workflows). Authors and consumers should be able to integrate the new processes and tools in their existing automation pipelines with minimal changes.

### Solution

This section describes the solution and its impact from the perspective of each of the use cases in the NuGet ecosystem (package authors, package consumers and server administrators). We describe the solution using the following stages to describe how the various experiences will be enabled over time, and how they will impact each use case.

#### Stage 1: Enable package authors to sign their packages

Today, it’s not easy to know if a given package has been modified from the time it was created, to the time it is consumed. To address this, we will use [digital signatures][9]. These are cryptographic artifacts that enable package verification in terms of integrity and authenticity from authors to consumers.

The [Author Package Signing specification][10] describes the author workflow for signing packages, as well as the signed package consumption workflow in Visual Studio in greater detail. We are finalizing the technical details of the standards we will use and these will be available soon in the [Package Signatures Technical specification][11].

##### Package authors

For authors who want to sign their packages, the first step in this process is to obtain an X.509 code signing certificate. We recommend the use of certificates issued by public Certification Authorities, but certificates issued by enterprise CAs will also work in enterprise environments. NuGet.org will not prevent submissions of packages that are unsigned, but they will lack certain visual indicators described later.

We will provide command line tools for Windows to enable signing existing NuGet packages, and verify signature validity prior to package submission (support for Mac and Linux will be added in Stage 3).

##### Package consumers

Visual Studio will provide a visual indicator for packages submitted to NuGet.org that have been signed with a trusted CA issued certificate, including displaying details of the package signer. Once the user has selected to download a package, Visual Studio (or NuGet.exe) will validate the package during the install operation. If the signature validation fails, the consumer will be informed that the contents of the package have been tampered with. For this stage, and for all future stages, older Visual Studio clients will simply continue to work without changes though they will not light up signature validation.

##### NuGet Package Sources other than NuGet.org

At the completion of this stage, there will be no impact visible to the end user when they consume packages from sources other than NuGet.org, including packages located on their file system. Such packages will just install irrespective of the status of their contents.

#### Stage 2: Tamper proofing entire package dependency graphs

Stage 1 establishes the foundation for authors to sign new packages, but it does not address the fact that packages are only trustworthy from an end users’ perspective if the entire dependency graph can be trusted by them. For packages that have not been signed by their authors, existing or new, we want to provide a mechanism to verify the integrity of the package contents once the package has been received by a server such as NuGet.org. To enable this scenario, the package source can add a **secure checksum** that will guarantee the package integrity from the time the package was received on the server to when it is consumed by end users, no matter where the package is physically located.

This secure checksum will improve the consumer experience in two areas:

*   When a signed package has a dependency on an unsigned package from a package source that implements secure checksums, the NuGet client will be able to verify the integrity of the complete dependency graph.
*   For scenarios where multiple package sources are used (such as NuGet mirrors), we will help the user identify (and block) packages that were modified after the package was submitted to a trusted source, such as NuGet.org.

> Note: We will publish more details on server checksums soon. You can find an index with all related specs [here][12].

##### Package authors

There will be no impact to package authors, since all packages submitted to NuGet.org, signed and unsigned, existing and new, will automatically be processed to include the checksum without user interaction.

##### Package consumers

Consumers will be able to see a visual indicator on all packages that contain the NuGet.org checksum, making it easier to identify packages that were once submitted to NuGet.org even if they are found in different sources.

If the package has been modified since its original submission to NuGet.org, the NuGet client will block the installation.

##### NuGet Package Sources other than NuGet.org

NuGet servers are not required to produce checksums, or to validate other server checksums, although we recommend that they check the checksum to avoid potential tampering while mirroring packages from NuGet.org.

Existing package sources that already have a copy of NuGet packages without the checksum make it impossible to enforce package content validation on their clients. We will recommend these sources to refresh their NuGet.org package copies to include the checksum by downloading them again from NuGet.org (in preparation for Stage 3, where such packages will simply fail to install for many user policy settings).

NuGet Server administrators interested in implementing this feature will get the detailed protocol changes and a reference implementation from our NuGet gallery source code. They can then choose if they would like to add their own checksums to new and existing packages submitted to their service.

#### Stage 3: Configurable policies to enable locked down developer environments

At this point in our journey, we will have all the pieces we need to tailor custom experiences that will serve most of our use cases: from hobbyist developers where security is not a huge constraint, to demanding enterprise scenarios that require full control on how packages will be authored and consumed.

NuGet server admins will be able to define server policies to require specific characteristics for packages before being submitted to a specific account. An example policy might be to require all new packages from the ASP.NET team to be signed.

##### Package authors

We will release package signing tools that can be used across platforms (Mac and Linux) so you no longer are restricted to using Windows for signing packages.

NuGet.org will have configurable policies that will allow us to enforce submission restrictions such as a Microsoft-owned packages and their dependencies to be signed with CA-issued certificates before they can be ingested into NuGet.org. Similarly, enterprises can choose to download unsigned packages and sign them with their own certificates, so they can enforce policies around what their users end up consuming in their projects.

##### Package consumers

Clients can configure themselves to not only accept, but more importantly **require** specific security constraints, like the ability to block unsigned packages or define a list of signed authors they trust. We will introduce a set of client policies to define the secure requirements per package source. In the table below, you can see a possible example that explains the client behaviors for different package types based on three different policies:

<style type="text/css">
  table.posttable { border-width: 1px; border-color: #004880; border-collapse: collapse; } table.posttable th { border-width: 1px; padding: 8px; border-style: solid; border-color:white; background-color: #004880; color:white; text-align: left; } table.posttable td { border-width: 1px; padding: 8px; border-style: solid; border-color: #004880; background-color: #ffffff; vertical-align: top }
</style>

<table class="posttable">
  <thead>
    <tr>
      <th>
        Package Type
      </th>
      
      <th>
        Developer Mode
      </th>
      
      <th>
        Default Mode
      </th>
      
      <th>
        Secure Mode
      </th>
    </tr>
  </thead>
  
  <tbody>
    <tr>
      <td>
        <strong>Unsigned</strong>
      </td>
      
      <td>
        Allow
      </td>
      
      <td>
        Warn
      </td>
      
      <td>
        Block
      </td>
    </tr>
    
    <tr>
      <td>
        <strong>Checksum</strong>
      </td>
      
      <td>
        Allow
      </td>
      
      <td>
        Allow
      </td>
      
      <td>
        Block
      </td>
    </tr>
    
    <tr>
      <td>
        <strong>Signed</strong>
      </td>
      
      <td>
        Allow
      </td>
      
      <td>
        Allow
      </td>
      
      <td>
        Prompt
      </td>
    </tr>
  </tbody>
</table>

*   **Allow**: The package will be installed without any error or warning.
*   **Warn**: The client will show a warning indicating there are packages in the dependency graph that does not satisfy policy constraints.
*   **Prompt**: Only packages added to a trust list will be allowed, and users can modify this list using Visual Studio user experiences.
*   **Block**: The package will not be installed, and we will show an error message.

##### NuGet Package Sources

Servers will be able to define policies to be applied during the package ingestion process, typically on per-account basis, and can define validation rules for signed packages.

> Note: We will publish more details on client and server policies soon. You can find an index with all related specs [here][12].

### Roadmap

The table below summarizes our current thinking around how we can introduce this experience in compliance with the design principles we laid out before. Once we iterate on the design with you and finalize our approach, we will propose dates for each of these stages:

<table class="posttable" >
  <thead>
    <tr>
      <th width="10%">
      </th>
      
      <th width="30%">
        Stage 1
      </th>
      
      <th width="30%">
        Stage 2
      </th>
      
      <th width="30%">
        Stage 3
      </th>
    </tr>
  </thead>
  
  <tbody>
    <tr>
      <td>
        <b>Authors</b>
      </td>
      
      <td>
        Command Line tools to enable Sign and Verify packages.
      </td>
      
      <td>
      </td>
      
      <td>
        Cross platform support.
      </td>
    </tr>
    
    <tr>
      <td>
        <b>Servers</b>
      </td>
      
      <td>
        Validate Signed Packages on submission.
      </td>
      
      <td>
        Generate source checksums.
      </td>
      
      <td>
        Configure server policies.
      </td>
    </tr>
    
    <tr>
      <td>
        <b>Consumers</b>
      </td>
      
      <td>
        Visual indicator in Visual Studio NuGet Package Manager for signed and verified packages on NuGet.org. <p>
          Validate author signatures when obtaining packages (install, restore or update) from CLI or IDE tools.
        </p>
      </td>
      
      <td>
        Validate complete dependency graph for signed packages. <p>
          Validate source checksums. <br /> Identify NuGet.org packages in multi-source environments.
        </p>
      </td>
      
      <td>
        Package Source Settings UI to specify client policy by source. <p>
          Trusted author dialog UI.
        </p>
      </td>
    </tr>
  </tbody>
</table>

### Related Specifications

You can find a list of the relevant specifications [here][12]. Some of these specifications are further along than others, but more details will be added shortly.

*   **Package Signing**: This is the master specification of the feature that can be used as an index with links to various related specs. [Package Signing][13]
*   **Package Author Signing** : Describes the user experience for producing and consuming signed packages. [Author Package Signing][10]
*   **Package Signatures Technical Details**: Contains the signature format technical details [Package Signatures Technical Details][11]

### Call to Action

I would like to again reiterate that we want to bring this experience in a way that allows everyone in the NuGet ecosystem to participate incrementally, and at the same time, allows the NuGet team to quickly iterate on delivering this experience which will greatly improve the trustworthiness of our ecosystem. We would love to hear your feedback on any of these experiences. You can share your feedback using this [GitHub issue][7], or by dropping me an [e-mail][14]. Because of the large-scale nature of this experience, we have found that phone conversations are often a good way of working through design issues – let me know if you would like to chat over the phone as well.

I would like to thank many of you who have provided ideas and suggestions on this proposal over the last month with a special call out to Oren Novotny, Kevin Jones and some folks on the .NET team. Their feedback has been invaluable so far.

 [1]: https://blog.nuget.org/20170809/NuGet-Fall-2017-Roadmap.html
 [2]: https://blog.nuget.org/20150203/package-signing.html
 [3]: https://github.com/aspnet/Signing/blob/dev/Spec.md
 [4]: https://blog.nuget.org/20170417/Package-identity-and-trust.html
 [5]: https://github.com/NuGet/Home/issues/5260
 [6]: https://github.com/NuGet/Home/issues/1882
 [7]: https://github.com/NuGet/Home/issues/2577
 [8]: https://github.com/NuGet/Announcements
 [9]: https://en.wikipedia.org/wiki/Digital_signature
 [10]: https://github.com/NuGet/Home/wiki/Author-Package-Signing
 [11]: https://github.com/NuGet/Home/wiki/Package-Signatures-Technical-Details
 [12]: https://github.com/NuGet/Home/wiki/Package-signing
 [13]: https://github.com/NuGet/Home/wiki/Package-Signing
 [14]: mailto:rmpablos@microsoft.com?subject=[Feedback]NuGetPackageSigning