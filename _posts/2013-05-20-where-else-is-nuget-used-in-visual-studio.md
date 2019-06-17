---
ID: 161
post_title: >
  Where else is NuGet used in Visual
  Studio?
author: Luan Nguyen
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/where-else-is-nuget-used-in-visual-studio/
published: true
post_date: 2013-05-20 00:00:00
---
All NuGet users are probably familiar with both the Manage NuGet Packages dialog and the Package Manager Console. These are the main user interfaces for installing and managing packages. 

However, NuGet is also used in a couple of more subtle places. Below are a few of them that I am aware of. With the exception of the New Project dialog, these dialogs/commands invoke NuGet through the [well-documented MEF services][1] that NuGet exports.

1.  **New Project dialog**
    
    When you create a new Web Application project, either a Web Forms or MVC app, the project template invokes NuGet to install a set of pre-defined packages into the new project.
    
    This feature is publicly documented here: <http://docs.nuget.org/docs/reference/packages-in-visual-studio-templates>. Which means it's not only available to Microsoft templates, but to any Visual Studio template.

2.  **Add Service Reference dialog**
    
    When you open the Add Service Reference dialog to add a reference to a WCF Data Service endpoint, the tool uses NuGet to install the latest version of the **Microsoft.Data.Services.Client** package and its dependencies.

3.  **Publish dialog**
    
    If you attempt to publish your web app to Windows Azure, the Publish dialog detects if your project is using SqlMembership provider, and if so, will ask you to upgrade to Universal provider in order to work with SQL Azure. If you accept, it will install the **Microsoft.AspNet.Providers** and **Microsoft.AspNet.Providers.Core** packages, as well as configure your web.config to use the Universal provider.
    
    ![Publish dialog][2]

4.  **Enable Azure Authentication command**
    
    If you enable [Windows Azure Active Directory][3] authentication in your web app, the command will install the **System.IdentityModel.Tokens.ValidatingIssuerNameRegistry** package.

5.  **Add Windows Azure Cloud Service project command**
    
    The **Add Windows Azure Cloud Service project** command, which appears on the context menu for Web projects, uses NuGet to install the **WindowsAzure.Storage** and **Microsoft.WindowsAzure.ConfigurationManager** packages.

I'm very happy to see various teams in Microsoft take advantage of NuGet to implement their own features. I expect even more widespread usage of NuGet in future versions of Visual Studio.

-Luan

 [1]: http://docs.nuget.org/docs/reference/invoking-nuget-services-from-inside-visual-studio
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/publish-dialog.png
 [3]: http://www.asp.net/vnext/overview/latest/windows-azure-authentication