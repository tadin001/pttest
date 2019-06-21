---
ID: 226153
post_title: >
  Lock down your dependencies using
  configurable trust policies
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/lock-down-your-dependencies-using-configurable-trust-policies/
published: true
post_date: 2018-12-05 00:00:00
---
For the past several months we have focused on various features to improve package security and trust. Around a year back, we had [announced our plans on various signing functionalities][1] that we have been implementing at a steady pace. We enabled [package author signing][2] and [NuGet.org repository signing][3] earlier this year. Continuing on the signing journey, we are happy to announce configurable client policies to secure developer environments for packages. With this feature, developers can now customize their environment to define package authors and/or package repositories they trust thereby allowing only trusted packages to be installed. This information is stored in the `nuget.config` file and can be configured to match your needs.

## How to lock down your environment

### Turn on `require` mode

You can enforce all your package dependencies to be signed just by enabling the `require` mode.

<pre><code class="language-cmd">nuget.exe config -set signatureValidationMode=require</code></pre>

### Configure trusted package repositories

You can then define your trust boundaries by specifying `trustedSigners`. For example, * You may just want to trust all packages available in NuGet.org:

<pre><code class="language-cmd"> nuget.exe trusted-signers add -name NuGet.org -serviceindex https://api.nuget.org/v3/index.json</code></pre>

If you want to trust packages from specific NuGet authors/accounts, you can specify the collection of owners:

<pre><code class="language-cmd"> nuget.exe trusted-signers add -name NuGet.org -serviceindex https://api.nuget.org/v3/index.json -owners microsoft;nuget</code></pre>

> NuGet.org adds repository signature to all new packages. We have started signing existing packages and we will announce when we are done. Subscribe to [NuGet/Announcements][4] repo for latest NuGet updates.

### Configure trusted package authors

You can configure trust based on the author signature by specifying the certificate fingerprint in SHA256. You can get the SHA256 fingerprint from any signed package using the [verify command][4]. This enables you to consume packages from this trusted author irrespective of the package repository/source.

<pre><code class="language-cmd">nuget.exe trusted-signers add -name Microsoft -certificateFingerprint 3F9001EA83C560D712C24CF213C3D312CB3BFF51EE89435D3430BD06B5D0EECE</code></pre>

> You can also add trusted signers, author or repository, by using an existing signed package. For more information see the [trusted-signers][5] command reference.

### Best practices

#### Share your `nuget.config`

`nuget.config` is a great option to share your settings across all your team members and even CI machines. If you keep your `nuget.config` file with the solution folder, install and restore operations will always use these settings. And this file can be easily shared along with your source code. To create a `nuget.config` file you can use the [`dotnet new nugetconfig`][6] from your solution root folder.

#### Use a different global-packages folder

If you have different repos/solutions on your machine with different trust configurations, you must isolate the global-packages folder for each solution. This is because NuGet does trust validation only on package extraction to the `globalPackagesFolder` i.e. if a package is already present in the `globalPackagesFolder`, there is no check performed.

### Example Config File

The following `nuget.config` file uses `require` mode and trusts packages in NuGet.org from the *Microsoft* account. Additionally, it also trusts packages signed with a private certificate.

> Note that the global package folder has also been customized.

<pre><code class="xml">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;configuration&gt;

  &lt;config&gt;
    &lt;add key="signatureValidationMode" value="require" /&gt;    
    &lt;add key="globalPackagesFolder" value="%USERPROFILE%.nugetTrustedPackages" /&gt;
  &lt;/config&gt;

 &lt;packageSources&gt;
    &lt;clear /&gt;
    &lt;add key="local" value="\myserverpackages" /&gt;
    &lt;add key="nuget" value="https://api.nuget.org/v3/index.json" /&gt;
 &lt;/packageSources&gt;

 &lt;trustedSigners&gt; 
  &lt;author name="MyCompanyCert"&gt;
    &lt;certificate fingerprint="F23175B9B052CE9C9D7E1546316F48A422DA3051FC79F4DB58ED5D78E372CEEC" 
                 hashAlgorithm="SHA256" 
                 allowUntrustedRoot="true" /&gt; &lt;!-- Enable private certificates--&gt;
  &lt;/author&gt;

  &lt;repository name="nuget.org" serviceIndex="https://api.nuget.org/v3/index.json"&gt;
    &lt;certificate fingerprint="0E5F38F57DC1BCC806D8494F4F90FBCEDD988B46760709CBEEC6F4219AA6157D" 
                 hashAlgorithm="SHA256" 
                 allowUntrustedRoot="false" /&gt;
    &lt;owners&gt;Microsoft&lt;/owners&gt;
  &lt;/repository&gt;
 &lt;/trustedSigners&gt;
&lt;/configuration&gt;
</code></pre>

## Reference documentation

Here are the docs for [configuring package signature requirements][7], the nuget.config [trustedSigners section][8], and the [trusted-signers][5] command.

## Conclusion

Defining trust policies enable additional security checks to protect your entire dependency graph, not only for packages obtained from NuGet.org but also from any other package repository. As long as all the packages you consume are signed you can enable the `require` mode to detect any tampered or unsigned package. It lets you control the authors and repositories that you trust. For more information on how to protect your dependencies with signed packages, look at our [documentation][9]. If you have any feedback or encounter any issues while using this feature, do reach out to us by creating a [GitHub issue][10] or by tagging [@nuget][11] in your tweets.

 [1]: https://blog.nuget.org/20170914/NuGet-Package-Signing.html
 [2]: https://blog.nuget.org/20180522/Introducing-signed-package-submissions.html
 [3]: https://blog.nuget.org/20180810/Introducing-Repository-Signatures.html
 [4]: https://docs.microsoft.com/nuget/tools/cli-ref-verify
 [5]: https://docs.microsoft.com/nuget/tools/cli-ref-trusted-signers
 [6]: https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-new?tabs=netcore21
 [7]: https://docs.microsoft.com/en-us/nuget/consume-packages/installing-signed-packages#configure-package-signature-requirements
 [8]: https://docs.microsoft.com/nuget/reference/nuget-config-file#trustedsigners-section
 [9]: https://docs.microsoft.com/nuget/reference/signed-packages-reference
 [10]: https://github.com/NuGet/Home/issues
 [11]: https://twitter.com/nuget