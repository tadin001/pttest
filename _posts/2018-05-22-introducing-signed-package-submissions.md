---
ID: 135
post_title: >
  Introducing signed package submissions
  to NuGet.org
author: Ricardo Minguez (Rido)
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/introducing-signed-package-submissions/
published: true
post_date: 2018-05-22 00:00:00
---
In September 2017, we [announced][1] our plans to improve the security of the NuGet ecosystem by introducing the ability for package authors to sign packages. Today, we want to announce support for any NuGet.org user to submit signed packages to NuGet.org.

A signed NuGet package is designed to be fully compatible with pre-existing NuGet servers and clients. Only newer versions of NuGet clients will take advantage of validating package signatures. We added this capability to Visual Studio 2017 15.6 – so we encourage you to upgrade to the latest VS updates to benefit from these added security measures.

All the packages (new as well as updates) authored by Microsoft will be digitally signed. For pre-existing NuGet.org packages, we are also actively working on getting those signed as described [here][2].

## Benefits of signing packages

As we described in our original [blog post][1], we have two primary goals to accomplish:

*   **Package Integrity**: We want to ensure the package contents have not been modified from the time the package was authored to when a developer downloads it for use in their projects. We also recognize that users copy packages from NuGet.org to multiple locations (such as setting up mirrors or copying them locally) and we want to ensure that such packages have not been modified before consumption.

*   **Package Authenticity**: We’ve heard from the community that it is often difficult to determine the origin of a package. In a previous blog post, we proposed some resolutions to the problem such as the ability for users to reserve package ID prefixes on NuGet.org. As we called out in that blog post, the next logical step is to address package signing. This will strengthen our package identity solution to provide authenticity on packages across multiple feeds.

### Securing package submissions

Additionally, NuGet.org uses package signatures as another authentication mechanism when submitting packages. Package owners can [register the certificates][3] used to sign their packages. Once the certificate is in place, all future submissions must be signed with that specific certificate.

## How to sign a package

1.  [Obtain a code signing certificate][4] trusted by nuget.org.
2.  [Install nuget.exe][5] 4.6 or later.
3.  Run the [sign][6] command.
    
        nuget sign MyPackage.nupkg -CertificateSubjectName *mycertsubjectname* -Timestamper *timestampserviceurl*
        

> **Note**: [NuGet.org package signing requirements][7].

## Register Certificates

Before submitting a signed package, the package owner must [register the certificate][3] on NuGet.org. In the accounts page find the certificate section and upload a certificate in format DER binary with the `.cer` extension.

![Register Certificates][8]

> **Note**: You must login with 2FA enabled before adding or removing certificates from your account.

Once you have registered a certificate to your account, all future submissions for the packages you own will require packages signed with that certificate. Unsigned package submissions will fail.

## Submit a signed package

Now you can submit signed packages using the web site or the command line, you (the package owner) will see which versions are signed.

![View Signed Packages][9]

## Installing signed packages

NuGet.exe 4.6 and Visual Studio 2017 Update 15.6 or later perform a signature validation just before extracting the package during install or restore operations. If the signature verification fails because the package has been tampered with the installation will fail and a restore error will be shown.

![Error Tampered Package][10]

## Start signing your packages today

We’d love to see NuGet package authors to take advantage of this security feature and to sumit their first signed package. If you have any question, comment or feedback you can reach out to me at [ricardo.minguez@microsoft.com][11] or by tagging [@nuget][12] in your tweets. You can also create an issue on our [GitHub repository][13].

 [1]: https://blog.nuget.org/20170914/NuGet-Package-Signing.html
 [2]: https://github.com/NuGet/Home/wiki/Repository-Signatures
 [3]: https://docs.microsoft.com/en-us/nuget/reference/signed-packages-reference#register-certificate-on-nugetorg
 [4]: https://docs.microsoft.com/en-us/nuget/reference/signed-packages-reference#get-a-code-signing-certificate
 [5]: https://www.nuget.org/downloads
 [6]: https://docs.microsoft.com/en-us/nuget/tools/cli-ref-sign
 [7]: https://docs.microsoft.com/en-us/nuget/reference/signed-packages-reference#signature-requirements-on-nugetorg
 [8]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/cert-reg.png
 [9]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/view-signed.png
 [10]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/err-tampered.png
 [11]: mailto:ricardo.minguez@nuget.org?subject=PackageSigning
 [12]: http://twitter.com/nuget
 [13]: https://github.com/NuGet/NuGetGallery/issues/new