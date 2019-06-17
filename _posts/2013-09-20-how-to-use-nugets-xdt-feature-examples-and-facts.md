---
ID: 171
post_title: 'How to use XDT in NuGet &#8211; Examples and Facts'
author: Dan Liu
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/how-to-use-nugets-xdt-feature-examples-and-facts/
published: true
post_date: 2013-09-20 00:00:00
---
Starting with NuGet 2.6, XML-Document-Transform (XDT) is supported to transform XML files inside a project. The XDT syntax can be utilized in the .install.xdt and .uninstall.xdt file(s) under the package's Content folder, which will be applied during package installation and uninstallation time, respectively.

One of XDT’s greatest strengths is its [simple but powerful syntax][1] for manipulating the structure of an XML DOM. Rather than simply overlaying one fixed document structure onto another structure, XDT provides controls for matching elements in a variety of ways, from simple attribute name matching to full XPath support. Once a matching element or set of elements is found, XDT provides a rich set of functions for manipulating the elements, whether that means adding, updating, or removing attributes, placing a new element at a specific location, or replacing or removing the entire element and its children.

In this blog, examples of using NuGet's XDT feature to manipulate XML DOM are demonstrated, based on the common transformation scenarios of Web.config files. Important aspects of applying this feature including its drawback are also given, which we want your feedback on how to further improve this feature and make it more useful.

## Part A: Common XDT Transformations in Web.config.(un)install.xdt file

*   Change the attribute values of an element in Web.config

To change the attribute value such as connectionString of a Web.config file, either the xdt:Transform="SetAttributes" or xdt:Transform="Replace" attribute can be used in Web.config.(un)install.xdt file, in conjunction with the xdt:Locator attribute.

The use of xdt:Transform="SetAttributes" below will update the value of the connectionString attribute only, while leaving other attribute values in the <add> element untouched.

<pre>&lt;?xml version="1.0"?&gt;
 &lt;configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"&gt;
   &lt;connectionStrings&gt; 
   &lt;add name="DefaultConnection" connectionString="value for the deployed Web.config file" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/&gt; 
   &lt;/connectionStrings&gt; 
   ... 
 &lt;/configuration &gt;</pre>

On the other hand, the use of xdt:Transform="Replace" will replace the entire <add> element named "DefaultConnection", with what's specified in the Web.config.(un)install.xdt file.

<pre>&lt;?xml version="1.0"?&gt;
 &lt;configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"&gt; 
   &lt;connectionStrings&gt; 
   &lt;add name="DefaultConnection" connectionString="value for the deployed Web.config file" 
    providerName="System.Data.SqlClient" xdt:Transform="Replace" xdt:Locator="Match(name)"/&gt; 
   &lt;/connectionStrings&gt; 
   ... 
 &lt;/configuration &gt;</pre>

*   Replace all elements under a section of Web.config

To replace all elements under the <system.web> section, the xdt:Transform="Replace" attribute can be placed at the root of the <system.web> section. For example, the following XDT transform will update the entire <system.web> section of the Web.config to just contain the two simple elements (<compilation> and <httpRuntime>) specified by the Web.config.(un)install.xdt file.

<pre>&lt;?xml version="1.0"?&gt; 
 &lt;configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"&gt; 
   &lt;system.web xdt:Transform="Replace"&gt; 
     &lt;compilation debug="false" targetFramework="4.5" /&gt; 
     &lt;httpRuntime executionTimeout="00:05:00"/&gt; 
   &lt;/system.web&gt; 
 &lt;/configuration&gt;</pre>

*   Insert a new element before the specific element in Web.config

Suppose the starting Web.config looks like below (from ASP.NET Web Forms Application):

<pre>&lt;?xml version="1.0"?&gt; 
 &lt;configuration&gt; 
   ... 
   &lt;system.webServer&gt; 
     &lt;modules runAllManagedModulesForAllRequests="true" /&gt; 
   &lt;/system.webServer&gt; 
   &lt;runtime&gt; 
   ... 
 &lt;/configuration&gt;</pre>

To insert a new <validation> element in the <system.webServer> section but before the <modules> element, the xdt:Transform="InsertBefore" attribute can be utilized in your Web.config.(un)install.xdt file:

<pre>&lt;?xml version="1.0"?&gt; 
 &lt;configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"&gt; 
   &lt;system.webServer&gt; 
     &lt;validation validateIntegratedModeConfiguration="false" xdt:Transform="InsertBefore(/configuration/system.webServer/modules)" /&gt; 
   &lt;/system.webServer&gt; 
 &lt;/configuration&gt;</pre>

*   Insert a section of elements if missing from the current Web.config

There are occasions that the starting Web.config does not have the section present, which elements need to be inserted to. In this case, the xdt:Transform="InsertIfMissing" can be used. However, for Web.config file that does have the target section available but contain different elements, the insertion won’t take place. The end result would be that the target section of the config file stays unmodified.

<pre>&lt;?xml version="1.0"?&gt; 
 &lt;configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"&gt; 
   &lt;configSections xdt:Transform="InsertIfMissing"&gt; 
     &lt;sectionGroup name="elmah"&gt; 
       &lt;section name="security" requirePermission="false" type="Elmah.SecuritySectionHandler, Elmah" /&gt;
       &lt;section name="errorLog" requirePermission="false" type="Elmah.ErrorLogSectionHandler, Elmah" /&gt; 
       &lt;section name="errorMail" requirePermission="false" type="Elmah.ErrorMailSectionHandler, Elmah" /&gt;
       &lt;section name="errorFilter" requirePermission="false" type="Elmah.ErrorFilterSectionHandler, Elmah" /&gt; 
     &lt;/sectionGroup&gt; 
   &lt;/configSections&gt; 
   ...
 &lt;/configuration&gt;</pre>

*   Two step transformation that ensures updating an existing value, or adding a new one

So far the transformations that we talked about are all one-step transformation that adheres to the XDT syntax. For some of the cases, the package authors may not know the exact content of the starting Web.configs. Therefore, the transformation could fail, due to reasons such as the targeted sections/elements cannot be found. One common case would be direct calling xdt:Transform="Insert" to a section or element that is not available in the current Web.config.

As a workaround, one can apply a two-step transformation and have code like below in the Web.config.(un)install.xdt file, i.e. by removing the matching element if existing, and then inserting the element with the same name back. Fortunately the transformations are done in sequence, so this works with a Web.config that has <appSettings> section in place.

<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt; 
 &lt;configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"&gt; 
   &lt;appSettings&gt; 
     &lt;!-- 
       Doing this in two steps (remove, then insert) ensures that we can update an existing value, or add a new one 
     --&gt; 
     &lt;add key="page:Version" xdt:Transform="Remove" xdt:Locator="Match(key)"/&gt; 
     &lt;add key="page:Version" value="2.0.0" xdt:Transform="Insert" /&gt; 
   &lt;/appSettings&gt; 
 &lt;/configuration&gt;</pre>

*   Transformation that can store/restore old values via package install/uninstall

A cool thing that the NuGet's XDT feature can do is that the original values can be stored upon package install, and then restored back via package uninstall. The example below showed that:

During package install by calling Web.config.install.xdt, the "SourceFile_advances" key is replaced with a new value, while the old value is saved as a comment in the new <local> section.

<pre>&lt;?xml version="1.0"?&gt; &lt;configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"&gt; &lt;appSettings xdt:Transform="InsertIfMissing"&gt; &lt;add key="SourceFile_Advances" keyvalue="C:\Code\SavvysoftValuations\new.csv" xdt:Locator="Match(key)" xdt:Transform="Replace" /&gt; &lt;/appSettings&gt; &lt;local xdt:Transform="Insert"&gt; &lt;!-- &lt;add key="SourceFile_Advances" keyvalue="C:\Code\SavvysoftValuations\original.csv" /&gt; --&gt; &lt;/local&gt; &lt;/configuration&gt;</pre>

During package uninstall by calling Web.config.uninstall.xdt, the <appSettings> and <local> section was removed and the original value for "SourceFile_advances" key was added back.

<pre>&lt;?xml version="1.0"?&gt; &lt;configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"&gt; &lt;appSettings xdt:Transform="Remove"&gt; &lt;/appSettings&gt; &lt;local xdt:Transform="Remove"&gt; &lt;/local&gt; &lt;appSettings xdt:Transform="InsertIfMissing"&gt; &lt;add key="SourceFile_Advances" keyvalue="C:\Code\SavvysoftValuations\original.csv" /&gt; &lt;/appSettings&gt; &lt;/configuration&gt;</pre>

## Part B: Things that you should know about the NuGet XDT feature

1.  Unlike the other NuGet packages, which files/references are automatically removed during package uninstallation, NuGet performs XML transformation specified in the .uninstall.xdt file. In that sense, XDT package uninstallation is not symmetrical to what's been added by package installation.
2.  The XDT feature can be apply to any XML files including the Web.Debug.config and Web.Release.config files. The transform .xdt files would need to be named Web.Debug.config.(un)install.xdt and Web.Release.config.(un)install.xdt accordingly.
3.  The XDT feature works well under nested Content folders, also recognizes the targetframeworks, by placing under the approriate folder such as net45.
4.  Due to limitation of the current transform syntax, authoring a universal package that can handle various forms of Web.config files (with sections/elements/attributes variations) can be difficult at this point. Especially, the combined usages of xdt:Transform="InsertIfMissing" and/or xdt:Transform="Insert" does not work well. Taking the below Web.config.install.xdt file as an example, when the <runtime> section does not exist, the whole section will be inserted, and the <dependentAssembly> element will be inserted one more time via the xdt:Transform="Insert" statement.

<pre>&lt;?xml version="1.0"?&gt; 
 &lt;configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"&gt; 
   &lt;runtime xdt:Transform="InsertIfMissing"&gt; 
     &lt;assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1"&gt; 
       &lt;dependentAssembly xdt:Transform="Insert"&gt; 
         &lt;assemblyIdentity name="X" publicKeyToken="032d34d3e998f237" culture="neutral" /&gt; 
         &lt;bindingRedirect oldVersion="0.0.0.0-2.0.1.5" newVersion="2.0.1.5" /&gt; 
       &lt;/dependentAssembly&gt; 
     &lt;/assemblyBinding&gt; \
   &lt;/runtime&gt; 
 &lt;/configuration&gt;</pre>

One potential solution to this issue would be allowing customized XDT transform, so that more complicated syntax can be applied during package install/uninstall, such as merging sections and elements between the starting Web.config files and the specified transformations. By this way, we are moving up the complication to the XDT syntax layer, instead on the transform file itself. A [sample custom transformation engine][2] has been authored by AppHarbor, with its [open source tester][3] on github.

 [1]: http://msdn.microsoft.com/en-us/library/dd465326.aspx
 [2]: http://blog.appharbor.com/2012/07/27/custom-web-config-transforms-and-merges
 [3]: https://github.com/appharbor/appharbor-transformtester