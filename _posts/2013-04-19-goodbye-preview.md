---
ID: 155
post_title: >
  Goodbye preview.nuget.org. Hello
  staging.nuget.org!
author: Andrew Stanton-Nurse
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/goodbye-preview/
published: true
post_date: 2013-04-19 00:00:00
---
[TL;DR][1] The [preview.nuget.org][2] site is going away. Switch to [staging.nuget.org][3] by April 26th!

## Goodbye preview.nuget.org

We have a version of the NuGet Gallery called "Preview" and it's been hosted at [preview.nuget.org][2] since early in the Gallery's lifetime. Until recently, our QA team used preview.nuget.org to do their testing, but we've also occasionally advised package owners to use the site as a sandbox environment. Over time, this has put us in the situation of the "preview" site serving two different purposes: 1) it was an internal QA testing environment, and 2) it was a customer sandbox/preview environment.

These two use cases for the environment have drastically different requirements. The QA environment needs to be sanitized to not have any real data and allow the QA team to do whatever they need to the data at any time. The customer sandbox environment needs to have a mirror of recent production data to provide a useful staging environment. Because of these conflicting requirements, we are separating our QA testing environment from the sandbox, and **we'll be decommisioning preview.nuget.org on April 26th**.

## Hello staging.nuget.org

To provide a sandbox where package authors can stage their packages to test their publishing, we are creating a new staging environment at [staging.nuget.org][3]. Staging is a very close mirror of production and its data is frequently copied from production (although not yet on any fixed schedule).

If you have packages you want to publish and you're nervous about the publishing process, please use [staging.nuget.org][3]. Your account information and API key are not guaranteed to be in sync because the data is not a live mirror, but you can register and log on to Staging just like you would on <http://nuget.org>. You can then push your packages to Staging and ensure you have your publishing process and your package metadata just right before publushing to [nuget.org][4].

 [1]: http://en.wiktionary.org/wiki/TL;DR
 [2]: http://preview.nuget.org
 [3]: http://staging.nuget.org
 [4]: http://nuget.org