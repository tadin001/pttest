---
ID: 170
post_title: 'Issues uploading portable library packages [Fixed]'
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/portable-package-issues/
published: true
post_date: 2013-09-10 00:00:00
---
Starting September 3rd, some users started having issues uploading packages that contain portable libraries; on September 10th, we deployed a fix to address those issues.

## Errors Experienced

Depending on how you were publishing your package, you would have experienced the errors in a few different ways.

### Upload Package page on nuget.org

If you were uploading your package through the nuget.org website's Upload Package page, the package would have initially uploaded okay, but you would have gotten an error when submitting the verified package details.

The error would have been our standard "Error: Oh no, we broke something!" response page.

### Push with nuget.exe

If you were using nuget.exe push to upload your package to the gallery, you would have gotten a different, and misleading error. That error was coming back as "The remote server returned an error: (502) Bad Gateway."

### Publish through NuGet Package Explorer

[NuGet Package Explorer][1] has the ability to publish packages directly. Attempting to publish packages with portable dependencies would lead to a vague error about a "bad request."

## Broken Scenarios

It took us a few days to really get to the root cause of this issue, because we were observing a couple of different behaviors. When we finally had it figured out, here's what it turned out to be.

### Scenario 1: Packages with portable libraries

In the scenario of a package containing portable libraries, there were a few details necessary to hit the error.

1.  The \lib folder of the package contained a subfolder for a portable library profile. Example: `\lib\portable-net4+sl4+wp71+win8`
2.  The nupkg file was created using `nuget.exe pack`.
3.  The nupkg file was being uploaded either through the Upload Package page on the gallery, or from `nuget.exe push`.

If the nupkg file was created with NuGet Package Explorer, or just opened and saved in it, then the package could be uploaded successfully. If the nupkg file was published to the gallery directly from NuGet Package Explorer, that would also succeed.

What we learned from this scenario was that even though NuGet Package Explorer uses the same endpoint as nuget.exe push, packages created within it were immune to the error.

### Scenario 2: Packages with portable dependencies

Some package authors were bypassing scenario 1 by using NuGet Package Explorer to create their package, but they were still getting the errors. In this scenario, we found the packages had dependencies that only applied for portable libraries. For instance, a dependency on Microsoft.Bcl.Async which only applies for portable library projects. NuGet Package Explorer would save this dependency's target framework as ".NETPortable,Version=v0.0,Profile=net45+sl4+sp71" and that would trigger the errors during package uploads as well, even when publishing through NuGet Package Explorer.

## Root Cause

The root cause of this issue ended up being a little complicated. Without diving too much into the details, here's the general explanation, broken down into a few steps.

### Step 1 - Creating the package

1.  Portable library folders names include `+` characters
2.  nuget.exe (built against .NET 4.0) encodes the `+` characters, changing them to `%2B`
3.  NuGet Package Explorer (built against .NET 4.5) *does not* encode the `+` characters

### Step 2 - Uploading the package

1.  When processing a package upload, nuget.org always decodes folder names for package contents to determine the package's supported frameworks
2.  When the package was created with nuget.exe, the `%2B` was decoded back to a `+` character
3.  When the package was created or published with NuGet Package Explorer, the `+` character was decoded into a space
4.  The decoded folder names determine the list of frameworks for the package

### Step 3 - Verifying target frameworks

1.  With the list of frameworks, nuget.org verifies frameworks before storing them in the list of supported frameworks for the package
2.  If unrecognized characters (such as spaces) are found in the framework name, the framework name is thrown out immediately
3.  If the framework name contains only valid characters, and it's found to be a portable library, we load the portable libraries from the .NET Framework installation and parse them to create normalized framework names

### Outcome

We found at this stage of the process that there were two possible outcomes:

1.  Packages created or published with NuGet Package Explorer, whose decoded folder names had spaces in them, would get ignored as invalid framework names -- this was causing data corruption where we weren't capturing the portable profiles supported by the package
2.  All other packages, whose decoded folder names had the correct `+` characters, would fail to parse because of a bug in how we were reading the normalized framework names from the .NET Framework

*Note that packages in Scenario 2 above bypass the folder name encoding/decoding and they were skipping directly to outcome number 2 where the framework name parsing would fail.*

For the first outcome, we handled the exception gracefully but this led to data corruption--it was actually a silent failure.

For the second outcome, we were not handling the exception and it was causing the visible errors users reported.

## The Fix

We needed a two-part fix to address the issues here.

1.  Parse the framework names correctly so that we don't fail trying to read the normalized framework name from the .NET Framework.
2.  Prevent spaces in the decoded folder names by putting the `+` characters back in place.

On September 10th, we deployed these fixes to the gallery and portable library packages can again be uploaded (with no data corruption). We are still working on correcting the data behind the packages that were successfully published over the past week that incurred the data corruption. The symptom of the data corruption is that despite the package being available on the gallery, users with previous versions of the package won't always see the new version as an available update.

## Our Apologies

We are sorry for the trouble this issue has caused package authors over the past week. We appreciate that you immediately reached out to us and that you were patient while we investigated and fixed the bugs.

 [1]: http://npe.codeplex.com