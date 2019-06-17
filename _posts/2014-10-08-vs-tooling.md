---
ID: 73
post_title: Visual Studio Tooling Guidance
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/vs-tooling/
published: true
post_date: 2014-10-08 00:00:00
---
As NuGet continues to gain momentum, more Microsoft teams have been seeking to install NuGet packages from their Visual Studio tooling. This began with ASP.NET, where NuGet was born, when project templates started coming with pre-installed NuGet packages. More recently, we've heard of teams all across Microsoft with tooling gestures that result in NuGet packages being installed.

We have some guidance we give these teams, but some have questioned our guidance. We're here to ask if you agree with us or if you think we should adapt to meet the expectations of these other teams.

## Installing Packages From nuget.org

The most common question is how tooling can install packages directly from [nuget.org][1].

### Benefits of Online Package Installation

Teams (that are usually new to NuGet) believe that they will begin shipping their libraries through NuGet and the libraries will no longer need to be installed on the user's machine (like traditional SDKs). They want their tooling to simply connect to nuget.org and install the package when the tooling gesture is performed.

This would make their tooling installer a bit easier to maintain because they wouldn't need to include the packages in the installer. Instead they'd only maintain the tooling that triggers the installation.

### Why We Discourage Online Installation

There are several reasons why we discourage online installation. In fact, our Visual Studio API intentionally makes it difficult to do this, but it is technically possible. Despite our firm guidance against it, some teams have proceeded to use this approach anyway.

Here are the reasons we cite:

#### It Breaks Offline Scenarios

Too many teams at Microsoft seem to think users are always online. We frequently hear from our community that we should never assume a connection (let alone a good one), and we remind other teams of this as often as we can.

If the tooling requires a connection to nuget.org, and the user is offline, there's a whole new failure path that gets opened up. What should the tooling do? Fail and show a message? Or fall back to a local package source? If there is a local package source, why not just use it every time?

#### It Violates NuGet Configurations

NuGet users can configure their own package sources--and many users do. Some users completely disable nuget.org and use their own internal package sources instead. In fact, companies can even utilize [NuGet's hierarchical configuration][2] to apply Group Policy that disables nuget.org by default.

If the user (or company) has disabled the nuget.org package source, for varying reasons including privacy, then Visual Studio tooling should respect this configuration. Otherwise, the tooling teams must ensure that they alter the Visual Studio privacy policy to indicate that their tooling will connect to nuget.org regardless of the user's NuGet configuration.

*Note: The Visual Studio privacy policy specifically calls out NuGet's Package Restore feature and that it will connect to nuget.org by default. But the policy informs users that they can change NuGet's configuration to disable nuget.org or the Package Restore feature.*

## Installing the Latest Package Version

Another frequent desire from tooling teams is to always install the latest version of the package. This is another detail where we strongly advise against this behavior, and we get strong pushback from other teams. Our Visual Studio API makes this exceptionally difficult to achieve, and teams have complained about how hacky their solutions need to be--we then explain to them that it's because we really don't want them doing it.

### Arguments for Installing the Latest Version

"We want to iterate on our packages more often than we ship our tooling." That's what we hear every time a team argues for installing the latest version of the package rather than a fixed version.

We also hear the argument that, "Our package coincides with an online service and we must keep them in sync."

### Requiring a Fixed Version

With tooling that installs the latest version of a package, the tooling gesture becomes temporal, with the results of the gesture changing over time. Perhaps this is just personal opinion, but I'd be furious if initiating a tooling gesture had a different effect today than it did three weeks ago or on a different project.

Additionally, the tooling now becomes vulnerable to changes in the package that are incompatible with the tooling. Sure, you can argue that [Semantic Versioning][3] can be used to install the latest patch or minor version where compatibility is guaranteed, but I'll respond with three counter arguments:

1.  There's no such thing as a compatibility guarantee
2.  And all you're doing is expanding your test matrix, with the possibility of handcuffing yourself
3.  Besides, this would only help users who haven't yet installed the package; it doesn't solve the problem for users who already have

What happens when you need to make a library change that breaks the tooling? I guess you're going to need to change your major version and ship new tooling anyway. If you need to plan for iterating on your tooling, then why not just iterate on the tooling in the first place?

Overall, we declare that the package consumer must be in control of when new package versions are installed. Therefore, we summarize this topic by declaring:

1.  Make the tooling install a fixed version
2.  Allow the user to utilize existing NuGet tooling to update the package when it's right for them
3.  Update your tooling when you need to make a change to the tooling and package together
4.  **Never let tooling update a package that is already installed!**

We believe the last point is especially important. If a tooling gesture that requires a package to be installed could update the package, the user's application could become broken in ways you can't predict. Worse yet, updating the package for the sake of one tooling gesture could break the application in a distant area that the user might not even realize could be affected. The package consumer is the only one who knows how the packages are being used in their application and therefore they should be the only ones controlling when packages are updated.

## NuGet Should Notify Users of Package Updates

After teams hear our guidance on installing fixed package versions through tooling, they almost always complain about how hard it is for users to update packages. This is especially true for teams whose users are relatively new to NuGet and aren't yet accustomed to checking for NuGet package updates.

### Projects Need To Be Kept Updated

Package authors generally think every update they push is an important one and their users need to update right away. Depending on the type of package, this can sometimes be valid. If the package coincides with a service, then it's possible that users must get the update. We've seen circumstances where server APIs were changed and old clients were broken. For those situations the package consumer does need to get the latest version of the package in order for their application to work. But in the vast majority of other scenarios, the package updates aren't nearly as important to package consumers as the authors would like to believe.

### Package Update Notifications Fall Short

Despite some authors thinking package update notifications would be a panacea, it certainly wouldn't be.

1.  How would NuGet control the flow of notifications to the user so that only truly important updates cause notifications?
2.  Who decides what is "important"?
3.  At what point would the notification be presented to the user?
4.  How would the user dismiss or "snooze" notifications?
5.  How would you notify the user if they aren't actively working on the project that consumes the package?

The last point is the most crucial I believe. If you're relying on notifications inside Visual Studio to inform the user that an installed package is out of date, but the user's application is deployed to production and in maintenance mode, how will they ever get the notification?

## Your Thoughts

What do you think? Is our guidance on the mark here, or do you think we should adjust? We'd appreciate hearing your feedback, especially from the package consumer point of view.

 [1]: http://www.nuget.org
 [2]: http://docs.nuget.org/docs/reference/nuget-config-file#NuGet_config_extensibility_point
 [3]: http://semver.org