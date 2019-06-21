---
ID: 226034
post_title: Package Signing
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/package-signing/
published: true
post_date: 2015-02-03 00:00:00
---
Package signing has been a major discussion point for a long time in the NuGet ecosystem. However, the NuGet Team didn't want to rush into an implementation and end up creating something that restricted the ecosystem unnecessarily. Well, we now think we are ready to begin a process to introduce Package Signing, and we want to share our plans with the community so you can see what we're thinking and give us feedback.

## Goals

Up until now, the transfer of packages was secured from NuGet.org down to your machine, and from publisher's machines up to NuGet.org using TLS/SSL. This has served us fairly well so far, but it suffers from a few issues:

1.  It is dependent upon the NuGet server providing a secure connection. NuGet.org does so, but others may not.
2.  It is dependent upon the security of NuGet.org itself. If NuGet.org is compromised, all bets are off and verification becomes very difficult ([RubyGems had an issue like this recently][1]).
3.  If you use a server or file share which mirrors packages from NuGet.org, you must rely on the security of that server.
4.  There is no way for a publisher to easily communicate to users that their credentials have been compromised and their packages should no longer be trusted.
5.  The "multi-source" model in NuGet means packages can be downloaded from any of your configured sources. Nothing ensures that the package you download is from the same source you initially installed it from.

Given these issues, we came up with a few goals for a Package Signing system:

1.  After a package consumer installs a package, we want the system to be able to ensure that all future downloads of that package come from the same publisher.
2.  When a consumer updates a package, we want the same assurance: That the new version is from the same publisher.
3.  These assurances should be made external to the server hosting the package, meaning the package can be downloaded from any server and the assurance can still be verified.
4.  Create a signing infrastructure that doesn't restrict us from adding richer functionality later.

An important **non-goal**: Once the package is on your local hard drive, we don't think package signatures should play a role in future usage of that package. The contents of NuGet packages can already be signed using Authenticode and operating system policies can be applied to prevent the usage of unsigned or untrusted binaries. We are only trying to secure the transmission of the physical `.nupkg` file from the author to your local drive.

## What we're actually trying to do

It's important here to stop for a second and clarify what we're actually trying to do. We are not trying to provide a system that makes any claims as to the *authenticity* of a package. We are only making assertions about the *identity* of a package.

This signing system is not trying to tell you that NuGet can verify that a package is the right version of Newtonsoft.Json, from James Newton-King. Instead, we can say that it's Newtonsoft.Json from someone in control of the private key for some certificate *X*. Actually verifying that James Newton-King is in control of that certificate is a secondary process that we are not providing here. That may be something we can add to this system later, but it is ***NOT*** a primary goal of the system at the moment. More importantly, once you've gotten the package and verified that it works for your scenarios, you can be assured that future versions are from the same source.

Our goal is to give package consumers the tools they need to make those identity decisions, and to ensure that once they have chosen to trust a particular package, we always deliver them the package they expect.

## Implementation

An easy technical solution to all of our goals would be to use [Authenticode][2] to sign NuGet packages. This would solve most of the problems above (and would even solve some of the authenticity issues above), and is likely a good solution for large corporate publishers like Microsoft. Unfortunately, it is not suitable for Open-Source developers (who are a major part of our ecosystem) due to the prohibitive financial cost of Code Signing certificates. Also, Authenticode is a system designed for use on Microsoft Windows platforms, and we are targetting cross-platform audiences using ASP.NET 5. It is possible to implement Authenticode on those platforms, but given the other issues, it seems like Authenticode is not the ideal solution here. Instead, we developed our own system (inspired by Authenticode) that is a little more tailored to the unique aspects of the NuGet ecosystem.

First, we will provide tools to allow publishers to manage their own signing certificates, without requiring Root CAs. If you have, or want to get, a certificate from a real Code Signing CA, you can use it with this system. However, you are not required to do so. We will provide tools to manage these certificates and use them to sign packages.

We will also provide tools to generate signatures for NuGet packages using a signing certificate. The signature itself will be stored in a separate file and can be transferred up to NuGet servers compatible with API v3 as well as file shares. We use the [CMS][3] format for encoding the digital signature. The signature is computed over the entire package ZIP file, including the nuspec and all metadata. This does mean that editing a signed package's metadata (description, etc.) becomes more complicated, but we have some ideas for allowing authors to "re-sign" modified packages.

When a user installs a signed package using the NuGet Dialog, the signature will be verified against the file. This verification is minimal, we will just be checking that the hashes actually match up, nothing about the certificates will be verified during this process. Assuming that verification completes, we will write the package into project.json/packages.config as before, but we will include some additional data about the certificates used to sign the package (this is based on the [Public Key Pinning][4] technique commonly used in SSL). The exact format is described in the spec, linked at the end of this post.

In the future, whenever NuGet downloads that package for that project, it will do the following verification:

1.  If the signature for the package NuGet downloaded cannot be found, it will be rejected and an error will be raised.
2.  If the signature for the package NuGet downloaded is invalid (hashes don't match), it will be rejected and an error will be raised.
3.  If the package NuGet downloaded is not signed by the same publisher, it will be rejected and an error will be raised. 
    1.  Note: We say "publisher" here not "key". The publisher can, and should, rotate keys frequently as good security practice. See the spec, linked at the end of this post, for more details on how we do this securely.
4.  If the package NuGet downloaded is was signed by a certificate that was revoked or expired at the time it was signed, it will be rejected and an error will be raised 
    1.  Note: The time the signature was ***made*** is used here, ***not*** the time of verification. You won't have to reissue old packages just because your certificate expired. We will encourage the use of [Trusted Timestamping][5], which is a service provided for free by most issuers of code-signing certificates, to record the time of signing. This is very similar to how Authenticode deals with the same problem.

This will also happen when NuGet updates the package. If the publisher has changed, the user warned about the change and given the option to approve it (similar to the way an SSH client presents the server's public key to the user for approval). As a NuGet publisher, it will be your responsibility to safeguard your key. If you lose your root certificate key, users will have to uninstall and reinstall your package when you release a new version with a new key. We will provide guidance and tools to backup your key in a secure manner.

## This seems familiar...

Ok, we're entering slightly more controversial territory here :). If you think this system sounds a little like [Strong-Named Assemblies][6], well... you'd be right. Signing a package means effectively adding a piece of identity to the package. It is also true that in order to be truely confident about the identity of the packages you have installed, a package *should* embed the full cryptographic identity for the packages it depends on (much like how Strong Named assemblies can only reference other Strong Named assemblies). After all, you are only as secure as your weakest link. However, I think what we've come up with is a more appropriate solution for the NuGet ecosystem. Let's start by going over some of the issues customers have encountered with Strong Names, then I will cover why we think this solution is more appropriate.

1.  Assemblies with the same name but different Public Keys are generally considered to be completely different Assemblies. This is intentional, and occasionally useful, but most of the time it's not what the user expects.
2.  It is *extremely* difficult (if not impossible) to substitute an assembly deep in your dependency graph with a different copy that uses a different key.
3.  Strong Named assemblies may only depend on other Strong Named assemblies.
4.  Strong Naming is deeply embedded in the runtime and prevents applications from running. There is almost nothing you can do (short of turning verification off entirely) to force an assembly to load when it is not properly strong-named. Also, the processes used to force an assembly to load are usually machine-level and are difficult to synchronize across developers in a team.

Here's where we think our system differs:

1.  Under our proposed system, two packages with the same package ID are **always** expected to be the same package, **regardless** of the signature. Two packages that differ by signature are interpreted as being different *variants* of the same package, not different packages entirely.
2.  Because of the way NuGet packages are referenced, it is ALWAYS possible to override a "signed" dependency with a package that is signed with a different key, or even unsigned. In ASP.NET 5, if you depend on `Foo` which depends on a signed copy of `Bar`, you can just add a direct dependency on `Bar` (with any key you want, or even no key at all) and the dependency further down the graph will be overridden by the top-level explicit dependency. Similarly, in packages.config, the indirect dependencies are listed right alongside direct dependencies and you can always remove or change the signature requirement on a specific package reference.
3.  We will not be enforcing that signed packages depend solely on other signed packages. This does decrease the security of the system, but as with the previous point, you can always use an explicit dependency to enforce the signature restriction on a normally unsigned package. We will likely warn publishers when they are signing packages if they depend on unsigned packages, but this won't restrict their ability to publish.
4.  This system only affects the download of the `.nupkg` file. Once the package is on disk, signatures don't matter. This means that in the absolute worst case, if you want to just drop a `.nupkg` on disk yourself, it **will** load successfully, regardless of signatures.

We think that as a package consumer, it is extremely useful for you to have some kind of externally-verifiable statement of identity for a package. A package signature gives you that ability (specifically the ability to know: "This is Newtonsoft.Json, as produced by the owner of the private key matching public key X"). Similarly, it is useful for consumers to be able to say "I only want to receive updates for this package from the owner of the private key matching public key X." However, the main lesson we feel we have learned from the concerns around Strong Naming is that we need to empower the consumer of a package to choose the identities they want to use for all packages in their dependency graph. That's what we're trying to achieve with this system. As a developer, you are always in control of the identities of **all** of your dependencies and can override them **as you see fit**.

We are hopeful that we can provide a system in which every NuGet publisher will be willing and easily able to give a cryptographically-sound identity to their packages. Other package management systems such as Maven actually [*require* signatures][7] like these in order to publish to their central repositories. We see the potential for this system to become similarly prolific and if you don't think you would sign your packages, we really want to know why!

## Timeline

The main timeline for this feature is ASP.NET 5. We are hoping to have something concrete for use with ASP.NET 5 projects when that platform releases it's 1.0 version. Support for use in packages.config and the more traditional NuGet workflow will likely come shortly after (but *will* come!). Also, we expect that our first pass at this feature will only run on Windows. We are still working out the cross-platform story for signing and verifying packages. Most of the cryptography code we are using right now is implemented *inside* the Windows kernel, so we'll need to work out exactly how we will do it on Mac and Linux (but we *will* be supporting those platforms post-RTM).

## Spec and Feedback

A detailed specification of our plan is available [on the aspnet/Signing repository][8], since the initial work is happening in the ASP.NET 5 platform. Please review that as well if you have concerns because they may have been answered there! We really want your feedback here, so please don't hesitate to comment on this post or in CodePlex!

 [1]: http://blog.rubygems.org/2013/01/31/data-verification.html
 [2]: http://msdn.microsoft.com/en-us/library/ie/ms537359%28v=vs.85%29.aspx
 [3]: https://tools.ietf.org/html/rfc5652
 [4]: https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning
 [5]: http://en.wikipedia.org/wiki/Trusted_timestamping
 [6]: http://msdn.microsoft.com/en-us/library/wd40t7ad%28v=vs.110%29.aspx
 [7]: http://central.sonatype.org/pages/requirements.html
 [8]: https://github.com/aspnet/Signing/blob/dev/Spec.md