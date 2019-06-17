---
ID: 174
post_title: 'Starting Manage NuGet Packages for Solution dialog for projects under SourceGear Vault &#8211; issue and workaround'
author: Dan Liu
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/click-manage-nuget-packages-for-solution-hangs-vs/
published: true
post_date: 2013-11-18 00:00:00
---
There has been reports that VS hangs when Manage NuGet Packages for Solution menu item is clicked. When VS responds again, it reports the following error: 

"Loading the projects is required to complete the requested action. If you click the Cancel button, project loading will continue in the background, and you can try to perform the action again later."

When this happens, clicking Cancel button on the message dialog won't work and Visual Studio has to be manually closed. This issue may be related to the association of your project with SourceGear Vault, which puts the read-only attribute to the packages.config file. One workaround for the issue is to remove the read-only attribute from the packages.config file. If this does not solve your hang issue, please report it to us by filing a bug on <http://nuget.codeplex.com>.