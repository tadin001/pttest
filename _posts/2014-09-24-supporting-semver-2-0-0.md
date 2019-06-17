---
ID: 70
post_title: Supporting Semantic Versioning 2.0.0
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/supporting-semver-2-0-0/
published: true
post_date: 2014-09-24 00:00:00
---
***Updated Sept. 2015:** Our roadmap and requirements have changed and we have yet to implement these features as of NuGet v3.2. We are tracking this issue on GitHub. Please track [this issue][1] to follow our progress.*

We adopted [Semantic Versioning][2] (SemVer) in [NuGet 1.6][3]. At the time, [SemVer 1.0.0][4] was the current version of the spec. Adopting SemVer provided NuGet support of pre-release package versions.

Since introducing SemVer support, SemVer has reached [2\.0.0][5] and introduced some new features. There have been \[a lot\]() of requests for NuGet to support SemVer 2.0.0 so these features can be used.

*   [Allow Semantic Versioning 2.0.0 characters][6]
*   [Support build version information using SemVer RC1 notation][7]
*   [Support for SemVer 2.0 specification's notion of pre-release builds][8]
*   [NuGet should allow versions like 2.1-alpha.10][9]
*   [Support semver >= 2.0.0-rc.1 specification][10]
*   [Proper pre-release Semantic Versioning not respected][11]
*   [Support Full Semantic Versioning][12]
*   [Package versioning: combining prerelease & build number not supported][13]
*   [Implement semver v2.0][14]
*   [Support SemVer build version in version strings][15]
*   [NuGet imposes artificial max string length of 20 characters for "SpecialVersion" (aka pre-release)][16]
*   [Version n.n-alpha.n "is not a valid version string"][17]
*   [Update SemanticVersion to match latest specs][18]

We've reviewed these issues that have been filed, consolidated them into just a couple of open issues, and we are planning to support these new SemVer 2.0.0 features in NuGet 3.0. We expect they'll be implemented between NuGet 3.0 Beta and NuGet 3.0 RTM--probably in a NuGet 3.0 RC release.

## Dot-Separated Pre-Release Identifiers

The most popular new feature in SemVer 2.0.0 is the dot-separated identifiers within pre-release labels. For contrast, here are some pre-release version numbers using SemVer 1.0.0, ordered in the way a client understands them:

1.  2\.0.0-alpha1
2.  2\.0.0-alpha10
3.  2\.0.0-alpha2
4.  2\.0.0-beta
5.  2\.0.0-beta2

While not everyone hits Alpha 10 in their pre-releases, it's happened enough that this has been reported as an issue on NuGet. This is working by design though, as the SemVer 1.0.0 spec didn't support numeric value sorting within pre-release labels. SemVer 2.0.0 introduces the feature with dot-separated identifiers. It makes the following possible:

1.  2\.0.0-alpha.1.11.28
2.  2\.0.0-alpha.2.1.4
3.  2\.0.0-alpha.10.9.18
4.  2\.0.0-beta
5.  2\.0.0-beta.2

NuGet 3.0 will understand these new dot-separated identifiers and properly sort them.

## Build Metadata

SemVer 2.0.0 also introduced a new version segment: build metadata. Build metadata is appended to the version using the `+` character. While build metadata doesn't affect version precedence, it allows the package author to embed metadata into the version number such as a commit hash, date, build number, or anything else that could be used for diagnostics.

NuGet will have the following behaviors for version build metadata:

1.  The build metadata will be respected and retained within the nuspec.
2.  The build metadata will be shown in the UI as part of the version number.
3.  NuGet.org will not allow you to publish multiple "builds" of the same version of a package (two versions where the only difference is the build metadata).
4.  Other NuGet feeds may choose to allow multiple builds. If they do, when the NuGet client needs to select the latest version of a package, it will arbitrarily select between packages that differ only by build metadata.
5.  The NuGet client could however allow you to specifically choose a "build" of a package if the feed exposes multiple builds.

## Downstream Implications

The changes required to support SemVer 2.0.0 in the NuGet client are quite simple, yes. However, there are downstream effects that would occur, and those are harder to deal with.

1.  All NuGet Servers would need to be updated to support the new version strings; otherwise the packages will fail to publish to the server
2.  All NuGet Clients need to understand the change as well, so that when these new packages are uploaded to servers and exposed on the feeds, they will be able to parse the version
3.  Projects that consume packages using these new versioning features will require NuGet 3.0 to be able to install/uninstall/restore packages

While it's acceptable to make this change and have packages fail to upload to servers when the version isn't recognized, it's not acceptable for NuGet Servers to begin exposing these new version numbers to old NuGet clients--it causes errors. Therefore we must implement this in a way that won't expose these new version numbers to old NuGet clients.

And it's noteworthy that the third point is left open here. If you use NuGet 3.0 to install a package that uses a SemVer 2.0.0 version number, you won't be able to use NuGet 1.x or 2.x clients to work with that project any longer. That includes NuGet Package Restore where you might have nuget.exe in place on your machine to perform package restore for any project/solution.

## Versioning the NuGet Feed Protocol

The good news is that we've been through this exercise before. Back when NuGet was first adopting Semantic Versions, we were faced with the exact same problems. When we introduced support for SemVer 1.0.0, we moved from NuGet API v1 to NuGet API v2. This allowed us to change the data type of the package versions from a .NET System.Version (4-segment version number) to a string that could represent either a .NET System.Version or a Semantic Version.

We are already creating a new NuGet feed protocol, so it's convenient to make this SemVer change now. We'll allow SemVer 2.0.0 packages on NuGet API v3, but we won't allow them to show up on API v2. That means if you include build metadata in your package's version, the package will only show up for NuGet 3.0+ users.

While creating a package that uses a SemVer 2.0.0 version, we will recognize this and annotate the package as requiring NuGet 3.0 as the minimum client version. If that minimum client version wasn't already specified, we'll provide a warning in the pack console output.

While making these changes, we'll also relax the current 20-character limitation on pre-release labels. We expect to extend this to 255 characters.

## Feedback

We welcome your feedback on this plan. While we've been thinking about this for a long time, we still have a little while before it'll get baked in.

 [1]: https://github.com/NuGet/Home/issues/1359
 [2]: http://semver.org
 [3]: http://docs.nuget.org/docs/release-notes/nuget-1.6
 [4]: http://semver.org/spec/v1.0.0.html
 [5]: http://semver.org/spec/v2.0.0.html
 [6]: https://nuget.codeplex.com/workitem/3924
 [7]: https://nuget.codeplex.com/workitem/1796
 [8]: https://nuget.codeplex.com/workitem/3416
 [9]: https://nuget.codeplex.com/workitem/2988
 [10]: https://nuget.codeplex.com/workitem/2636
 [11]: https://nuget.codeplex.com/workitem/3805
 [12]: https://nuget.codeplex.com/workitem/2915
 [13]: https://nuget.codeplex.com/workitem/2346
 [14]: https://nuget.codeplex.com/workitem/2247
 [15]: https://nuget.codeplex.com/workitem/2137
 [16]: https://nuget.codeplex.com/workitem/3426
 [17]: https://nuget.codeplex.com/workitem/2931
 [18]: https://nuget.codeplex.com/workitem/1697