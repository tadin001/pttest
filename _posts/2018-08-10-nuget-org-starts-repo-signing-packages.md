---
ID: 226147
post_title: NuGet.org starts repo-signing packages
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/nuget-org-starts-repo-signing-packages/
published: true
post_date: 2018-08-10 00:00:00
---
In May, we implemented [Stage 1 and enabled support for any NuGet.org user to submit signed packages to NuGet.org][1]. Today, we are announcing [Stage 2][2] of our NuGet package signing journey - tamper proofing the entire package dependency graph.

## What is a Repository Signature?

A repository signature is a code signing signature produced with an X.509 certificate. This signature is uniquely associated with a repository using custom [metadata][3].

## Benefits of repository signatures

NuGet.org has started to repository sign new package submissions. Author signed packages will be countersigned by NuGet.org repository signature. This will provide package integrity guarantee for packages published to NuGet.org. NuGet clients will ensure that the package contents have not been modified from the time a package was uploaded to NuGet.org to when a developer downloads it for use in their projects.

## Changes in your packages

If you are a package author, you will notice the packages you submitted to NuGet.org have been modified to include a repository signature. This will add a few kilobytes to your package. If the package submitted was unsigned you will notice, there is a new file in the package root called `.signature.p7s`. If your package was author signed, NuGet.org will add a countersignature in the existing signature file. No other changes are made to package contents.

## Rollout plan

Starting today, all new package submissions to NuGet.org will be repository signed. We also plan to repository sign all existing packages on NuGet.org.

## Check Repository Signatures

Using the [verify command][4] you can inspect the package's author and/or repository signatures. In the example below, you can see the verify command output for a package with an author primary signature and a repository countersignature.

    c:nuget.exe verify -all nuget.commandline.nupkg
    Signature Hash Algorithm: SHA256 
    Signature type: Author 
    Verifying the author primary signature with certificate: 
       Subject Name: CN=Microsoft Corporation, O=Microsoft Corporation, L=Redmond, S=Washington, C=US 
       SHA1 hash: F404000FB11E61F446529981C7059A76C061631E 
       SHA256 hash: 3F9001EA83C560D712C24CF213C3D312CB3BFF51EE89435D3430BD06B5D0EECE 
       Issued by: CN=DigiCert SHA2 Assured ID Code Signing CA, OU=www.digicert.com, O=DigiCert Inc, C=US 
       Valid from: 2/25/2018 4:00:00 PM to 1/27/2021 4:00:00 AM 
    
    Timestamp: 8/1/2018 11:20:19 AM 
    
    Verifying author primary signature's timestamp with timestamping service certificate: 
      Subject Name: CN=Symantec 
      SHA256 TimeStamping Signer - G3, OU=Symantec Trust Network, O=Symantec Corporation, C=US 
      SHA1 hash: A9A4121063D71D48E8529A4681DE803E3E7954B0 
      SHA256 hash: C474CE76007D02394E0DA5E4DE7C14C680F9E282013CFEF653EF5DB71FDF61F8 
      Issued by: CN=Symantec SHA256 TimeStamping CA, OU=Symantec Trust Network, O=Symantec Corporation, C=US 
      Valid from: 12/22/2017 4:00:00 PM to 3/22/2029 4:59:59 PM 
    
    Signature type: Repository 
      nuget-v3-service-index-url: https://api.nuget.org/v3/index.json 
      nuget-package-owners: microsoft, nuget 
      Verifying the repository countersignature with certificate: 
        Subject Name: CN=NuGet.org Repository by Microsoft, O=NuGet.org Repository by Microsoft, L=Redmond, S=Washington, C=US 
        SHA1 hash: 8FB6D7FCF7AD49EB774446EFE778B33365BB7BFB 
        SHA256 hash: 0E5F38F57DC1BCC806D8494F4F90FBCEDD988B46760709CBEEC6F4219AA6157D 
        Issued by: CN=DigiCert SHA2 Assured ID Code Signing CA, OU=www.digicert.com, O=DigiCert Inc, C=US 
        Valid from: 4/9/2018 5:00:00 PM to 4/14/2021 5:00:00 AM 
    
    Timestamp: 8/1/2018 11:36:04 AM 
    
    Verifying repository countersignature's timestamp with timestamping service certificate: 
       Subject Name: CN=Symantec SHA256 TimeStamping Signer - G3, OU=Symantec Trust Network, O=Symantec Corporation, C=US 
       SHA1 hash: A9A4121063D71D48E8529A4681DE803E3E7954B0 
       SHA256 hash: C474CE76007D02394E0DA5E4DE7C14C680F9E282013CFEF653EF5DB71FDF61F8 
       Issued by: CN=Symantec SHA256 TimeStamping CA, OU=Symantec Trust Network, O=Symantec Corporation, C=US 
       Valid from: 12/22/2017 4:00:00 PM to 3/22/2029 4:59:59 PM
    

## NuGet Client Requirements

NuGet clients support repository signed package verification since nuget.exe 4.7 and Visual Studio 2017 version 15.7. If a package has been tampered with, the client will block the installation.

## Repository Signature Requirements

NuGet.org is the first NuGet server implementing repository signatures. Other repositories can implement the same feature as long they satisfy the server requirements. More details can be found in the [Repository Signatures specification][5].

### Service Protocol Updates

NuGet servers that implement repository signatures should announce the list of certificates used to generate the repository signatures. This announcement will be available as part of the NuGet protocol v3 and is documented [here][6].

### Repository Signature Metadata

Repository signatures contain additional metadata to enable NuGet clients to verify if the signature has been produced by a certificate the repository announces (NuGet.org will soon starts announcing this certificate). The signature format has two fields:

*   **Service Index**: Indicates the URL to the repository [service index][7], e.g. `https://api.nuget.org/v3/index.json`. During package validation NuGet clients should require that all packages from the claimed repository of origin are signed with one of the certificates announced at the service index.
*   **Owners**: Indicates the package owners at the time of submission, e.g. `nuget, microsoft`. This field will be used by the upcoming Client Policies feature to provide flexibility for package consumers to decide which packages can be installed.

## Next Steps

We have designed this feature to improve the security and minimize the impact on package authors and package consumers. However, if you are using custom package sources such as a [local folder feed][8], or [upstream sources][9] we recommend refreshing the cache periodically to make sure you have the recent versions with the repository signature.

 [1]: https://blog.nuget.org/20180522/Introducing-signed-package-submissions.html
 [2]: https://blog.nuget.org/20170914/NuGet-Package-Signing.html#stage-2-tamper-proofing-entire-package-dependency-graphs
 [3]: #repository-signature-metadata
 [4]: https://docs.microsoft.com/en-us/nuget/tools/cli-ref-verify
 [5]: https://github.com/NuGet/Home/wiki/Repository-Signatures
 [6]: https://docs.microsoft.com/en-us/nuget/api/repository-signatures-resource
 [7]: https://docs.microsoft.com/en-us/nuget/api/service-index
 [8]: https://docs.microsoft.com/en-us/nuget/hosting-packages/local-feeds
 [9]: https://docs.microsoft.com/en-us/vsts/package/concepts/upstream-sources?view=vsts