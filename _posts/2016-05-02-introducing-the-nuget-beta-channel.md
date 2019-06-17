---
ID: 107
post_title: Introducing The NuGet Beta Channel
author: Harikrishna Menon
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/introducing-the-nuget-beta-channel/
published: true
post_date: 2016-05-02 00:00:00
---
Today, we would like to introduce you to the NuGet Beta Channel for the Visual Studio 2015 NuGet Package Manager. In the last 2 months, we have been focusing on improving the quality of the package management experience in Visual Studio 2015 and we have released over 3 consecutive versions of the package manager with added features, improved quality and performance. We want to able to accelerate this cadence and make sure that our customers are able to access new features and bug fixes faster. 

We have been trying to accelerate our releases, but we realized that even when we share beta bits, it takes too much effort to discover them, and we end up getting bug reports only after we release the RTM version. We would like to reach interested users with our beta releases for feedback. This way we can simultaneously share improvements faster, and at the same time impact a smaller ring of users if we end up regressing a scenario.

## Who is this for?

The beta channel is recommended for the following users: -

*   You like to stay on the latest and greatest.
*   You want to try out new features.
*   You are experiencing a blocking issue and want to get access to the build with the fix before it hits RTM.

We also hope that you use this opportunity to give us feedback on the quality of the release. If you run into any issues whatsoever, feel free to [open an issue on GitHub][1].

## What goes into making a Beta build?

Beta fixes go through a decent amount of dedicated validation and dog-fooding by the team and our partners. Even though it’s called a beta, we will only release near RTM quality builds into this channel. We want to use the extra runway on our end to make sure that we incorporate any feedback that we might get from the users of the channel and catch any blocking issues early on.

## How do I get access to the Beta Feed?

You can get access to the Beta builds by the following the steps outlined below. We have published the 3.4.4 RC build fixing these [issues][2] to this channel today.

*   Add the Beta Feed: <https://dotnet.myget.org/F/nuget-beta/vsix/> to the Additional Extension Galleries list in Tools->Options->Environment->Extensions and Updates.

![Extensions and Updates Settings][3]

*   Navigate to Tools->Extensions and Updates and select Online. You should now be able to see the NuGet-Beta Feed there. Install the NuGet Package Manager Extension.

![Extensions and Updates][4]

*   You are all set to get beta releases! Remember if you run into any issues while dogfooding the beta build or have an idea, [open an issue on GitHub][1]. 

## We want to hear your feedback!

We want NuGet to meet the evolving needs of our community. If you would like share your pain points and your current and future needs, use the [calendly link][5] to set up a quick 15-30 min call with us. If you would like to send us an email instead, hit us up at <feedback@nuget.org>.

You can also leave a comment below, and as always, if you run into any issues or have an idea, [open an issue on GitHub][1].

 [1]: https://github.com/Nuget/Home
 [2]: https://github.com/Nuget/Home/issues?q=is%3Aissue+milestone%3A3.4.4+is%3Aclosed
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/ToolsSettings.png
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/ExtensionUpdate.png
 [5]: https://calendly.com/harikm/nuget-feedback