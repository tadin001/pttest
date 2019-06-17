---
ID: 101
post_title: NuGet ContentFiles Demystified
author: Jeffrey Fritz
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/nuget-contentfiles-demystified/
published: true
post_date: 2016-01-26 00:00:00
---
*Updated - Feb 26, 2015: Thanks to [Jared Barneck][1] for some suggested edits to add some steps to the sample NuGet creation process*

In NuGet 3.3, the `contentFiles` feature was introduced to support project.json managed projects and packages that are indirectly referenced in a project. This was an important change because it brings the ability to deliver static files, .pp file transforms, and language specific code into a project. In this post, we'll explore how to make use of this feature and show some samples of how to make the most of it in your projects.

## Definition

Let's start with a quick definition. ContentFiles in NuGet are static files that the NuGet client will make available through the project.lock.json file to a project for inclusion in the project. There are three different types of files that can be included as a content file:

*   Language specific files (.cs, .vb, .fs) that are directly included in a project
*   Language specific .pp transform files that are transformed and their output is included in the project for compilation
*   Other static files (text files, image files, etc) that can be embedded in a project or added for use during development and not included when the project is compiled

ContentFiles are placed in a NuGet package in the `/contentFiles` folder and their capabilities are defined in the `/package/metadata/contentFiles` element inside the nuspec document. Attributes are defined for these files that are passed to the project system to indicate how the files should be used in a project.

When placing content into the `contentFiles` folder, we have some options to help steer the project that uses the content. There are two folder levels that are defined under contentFiles to categorize our content:

1 Language (vb, cs, fs) 2 Target Framework Moniker or TFM (net35, wp81, etc)

Either of these can be replaced with the catch-all `any` name in order to deliver content to all languages or framework types. In the case of our logo, we would like it to be delivered for all languages and for any framework type therefore we will place the logo in the `contentFiles\any\any` folder.

## Supported Project Types

**Update Oct 12, 2017** This feature is supported for projects that use PackageReference and project.json package management formats. It is not supported for projects that use packages.config.

This feature is only for packages that will be installed to projects that are managed using a project.json file. Currently only two projects types are managed by a project.json.

1.  UWP apps
2.  Portable class libraries

The contentFiles option is not available for other project types.

## First Example - Include a Branding Image

A first example that we can look at would be to include a png of your organization's logo so that all applications have the same logo in the 'About' page. In the case of NuGet, we would want to use the same .NET Foundation logo everywhere. To build a package to deliver this logo, we could create a `contentFiles` folder on disk and a nuspec to define the package.

### Step 1 - Create a contentFiles folder.

We like to work in `c:\dev`, so lets create a project folder called `ContentFilesExample` and place a folder underneath it to hold our content.

    c:\dev\ContentFilesExample\contentFiles

### Step 2 - Add a branding image

Create the following folder structure:

    c:\dev\ContentFilesExample\contentFiles\any\any\images\

Notice: We followed the procedure discussed above for our folder structure. The first "any" folder is the language. The second "any" folder is the target framework. We could have been more specific and something like used `cs\net40` to apply the contents to projects using c-sharp in .NET 4.0. Also, to make it even clearer that this is an image, we added an images folder.

Put an image into that folder. In this example, our image is called dnf.png.

    c:\dev\ContentFilesExample\contentFiles\any\any\images\dnf.png

### Step 3 - Create the contentFilesExample.nuspec and add appropriate Xml

Let's create the nuspec manifest file for this package and populate it with the information NuGet needs. We can create and place this file at:

    c:\dev\ContentFilesExample\ContentFilesExample.nuspec

Next, we need to author the nuspec appropriately to deliver this image with an EmbeddedResource action. A minimum nuspec document for this package may look like the following:

<pre><code class="language-xml">&lt;?xml version="1.0"?&gt; 
&lt;package&gt; 
  &lt;metadata minClientVersion="3.3.0"&gt; 
    &lt;id&gt;ContentFilesExample&lt;/id&gt; 
    &lt;version&gt;1.0.0&lt;/version&gt; 
    &lt;authors&gt;nuget&lt;/authors&gt; &lt;!-- The NuGet team authored this package --&gt; 
    &lt;owners&gt;nuget&lt;/owners&gt; &lt;!-- The NuGet team owns this package --&gt; 
    &lt;requireLicenseAcceptance&gt;false&lt;/requireLicenseAcceptance&gt; 
    &lt;description&gt;A content v2 example package.&lt;/description&gt; 
    &lt;tags&gt;contentv2 contentFiles&lt;/tags&gt; 
    &lt;!-- Build actions for items in the contentFiles folder --&gt; 
    &lt;contentFiles&gt; 
      &lt;!-- Include Assets as Content --&gt; 
      &lt;files include="**/images/*.*" buildAction="EmbeddedResource" /&gt; 
    &lt;/contentFiles&gt; 
   &lt;/metadata&gt; 
&lt;/package&gt;</code></pre>

Notice the file-globbing pattern on the `files/@include` attribute. We have a lot of flexibility to reference groups of files and can define features for them using this markup.

### Step 4 - Build the nuget package

You may need to download nuget.exe to do this from here: [Download NuGet][2].

For this example, we put a copy of nuget.exe in the `c:\dev\ContentFilesExample\` folder.

    c:\dev\ContentFilesExample\nuget.exe

Open a command prompt and run the following:

    cd c:\dev\ContentFilesExample nuget.exe pack ContentFilesExample.nuspec

This createas your nuget package and names it ContentFilesExample.1.0.0.nupkg

    c:\dev\ContentFilesExample\ContentFilesExample.1.0.0.nupkg

### Step 5 - Install the nuget package

1.  Open Visual Studio.
2.  Go to File | New | Project
3.  Choose one of the supported project types: UWP app, or Portable class library. In this example, we are going to choose a portable class library.
4.  Name it and click OK. For this example, we will leave the default name of ClassLibrary1.
5.  Now go to Tools | NuGet Package Manager | Package Manager Console.
6.  Run this command:

    Install-Package ContentFilesExample -source c:\dev\ContentFilesExample

You should see the image added to your project.

## Second Example - Adding files from other locations

The `contentFiles` elements in the nuspec are pointers to files that exist inside the package. We can also use the [standard NuGet files element][3] to move items from outside of the package folder hierarchy into the package. Lets start using that markup to add a CS file into the package. I can add a file called `ExampleInternals.cs` to the root of my package folder so that the file structure on disk is:

    c:\dev\ContentFilesExample\ContentFilesExample.nuspec 
    c:\dev\ContentFilesExample\ExampleInternals.cs 
    c:\dev\ContentFilesExample\contentFiles\any\any\images\dnf.png

My nuspec will now be modified to contain the following:

<pre><code class="language-xml">&lt;?xml version="1.0"?&gt; 
&lt;package&gt; 
  &lt;metadata minClientVersion="3.3.0"&gt; 
   &lt;id&gt;ContentFilesExample&lt;/id&gt; 
   &lt;version&gt;1.0.1&lt;/version&gt; 
   &lt;authors&gt;nuget&lt;/authors&gt; 
   &lt;owners&gt;nuget&lt;/owners&gt; 
   &lt;requireLicenseAcceptance&gt;false&lt;/requireLicenseAcceptance&gt; 
   &lt;description&gt;A content v2 example package.&lt;/description&gt; 
   &lt;tags&gt;contentv2 contentFiles&lt;/tags&gt; 
   &lt;!-- Build actions for items in the contentFiles folder --&gt; 
   &lt;contentFiles&gt; &lt;!-- Include Assets as Content --&gt; 
      &lt;files include="**/images/*.*" buildAction="EmbeddedResource" /&gt; 
      &lt;files include="cs/**/*.*" buildAction="Compile" /&gt; 
   &lt;/contentFiles&gt; 
  &lt;/metadata&gt; 
  &lt;files&gt; 
    &lt;file src="contentFiles\**" target="contentFiles" /&gt; 
    &lt;file src="ExampleInternals.cs" target="contentFiles\cs\any" /&gt; 
  &lt;/files&gt; 
&lt;/package&gt;</code></pre>

Notice that we now have defined all of the files that are included in the package outside of the metadata element. Since we started declaring the files to include in the nuspec, all files must be declare in this way and the convention of just referencing the location on disk is no longer observed in this mode.

## Other options

The files that are referenced by the `contentFiles` element in NuSpec have four attributes that can be set on them:

*   `include` OR `exclude` - The pattern or filename that should be included or excluded in the package
*   `buildAction` - the action the compiler is recommended to take. Possible values include: `None`, `Compile`, `EmbeddedResource`. The default value if this attribute is not provided is `Compile`
*   `copyToOutput` - should these files be copied to the output folder? The default value is false.
*   `flatten` - Should the files be all delivered to the root folder of the project or should they keep the folder structure that they were packaged in. The default value is false, which indicates they should create (if necessary) and keep the same structure they have after the TFM folder in the pacakge.

## Summary

ContentFiles in NuGet packages allows the package author to deliver more information to their consumers about the content that is delivered to a project.json managed project. The package author can help steer the compiler to provide settings for the files delivered to ensure that they are used properly by the project that installed the package. You can read more about the [contentFiles feature definition in the NuGet docs][4].

 [1]: https://twitter.com/rhyous
 [2]: https://dist.nuget.org/index.html
 [3]: http://docs.nuget.org/Create/NuSpec-Reference#specifying-files-to-include-in-the-package
 [4]: http://docs.nuget.org/Create/NuSpec-Reference#contentfiles-with-visual-studio-2015-update-1-and-later