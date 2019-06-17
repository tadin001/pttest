---
ID: 138
post_title: >
  Introducing Source Code Link for NuGet
  packages
author: Maxime Rouiller
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/introducing-source-code-link-for-nuget-packages/
published: true
post_date: 2018-08-27 00:00:00
---
NuGet.org now supports surfacing source code repository link for NuGet packages. This will enable package authors to surface both the project's website and the source repository using the `projectUrl` and the `repository` properties respectively instead of having to choose between the two using just the `projectUrl` property. The nuspec has supported the `repository` property for a while and today more than 16,000 packages contain this property. We have now come a full circle by surfacing this information on the package details page (on NuGet.org). This is how it looks for the [Newtonsoft.Json][1] package: ![Source Code][2] In this post, I would like to elaborate on the different ways to light up this feature for your packages.

### Using `nuspec` If you are using a

`nuspec` file for packaging, you will need to add a repository tag within your nuspec. For example, take a look at [NUnit's nuspec][3]: <package xmlns="http://schemas.microsoft.com/packaging/2013/05/nuspec.xsd"> <metadata minClientVersion="2.12"> <!-- .. snipped ... --></metadata></package>

          <!-- Add this line and making the URL targets your project --> 
          <repository type="git" url="https://github.com/nunit/nunit" />
    
          <!-- .. snipped ... --> 
       </metadata> 
    </package>
    

### Using project file If you create your NuGet package directly with a project file, you will need to add a

`RepositoryUrl` and a `RepositoryType` to the project file to enable this new feature on your package. Here's an example on how [RestSharp][4] implements it directly within their `csproj`. <project Sdk="Microsoft.NET.Sdk"> <propertygroup> <!-- .. snipped ... --></propertygroup></project>

        <RepositoryUrl>https://github.com/restsharp/RestSharp.git</RepositoryUrl> 
        <RepositoryType>git</RepositoryType> 
        <!-- .. snipped ... --> 
       </PropertyGroup> 
       <!-- .. snipped ... --> 
    </Project>
    

### Using SourceLink

[SourceLink][5] enables source debugging directly within your .NET Core packages. At the same time, it also inserts the repostory attribute in your project to have the link show up on the NuGet package's details page. To use it, you need to add the following to your `csproj` file.

<pre class="highlight"><code>&lt;PropertyGroup&gt;
    &lt;!-- Optional: Publish the repository URL in the built .nupkg (in the NuSpec &lt;Repository&gt; element) --&gt;
    &lt;PublishRepositoryUrl&gt;true&lt;/PublishRepositoryUrl&gt;
&lt;/PropertyGroup&gt;

&lt;ItemGroup&gt;
  &lt;!-- For GitHub --&gt;
  &lt;PackageReference Include="Microsoft.SourceLink.GitHub" Version="1.0.0-beta-63127-02" PrivateAssets="All"/&gt;

  &lt;!-- For Visual Studio Team Services --&gt;
  &lt;PackageReference Include="Microsoft.SourceLink.Vsts.Git" Version="1.0.0-beta-63127-02" PrivateAssets="All"/&gt;

  &lt;!-- For Team Foundation Server --&gt;
  &lt;PackageReference Include="Microsoft.SourceLink.Tfs.Git" Version="1.0.0-beta-63127-02" PrivateAssets="All"/&gt;
  &lt;SourceLinkTfsGitHost Include="tfs-server-name" VirtualDirectory="tfs"/&gt;

  &lt;!-- For GitLab --&gt;
  &lt;PackageReference Include="Microsoft.SourceLink.GitLab" Version="1.0.0-beta-63127-02" PrivateAssets="All"/&gt;

  &lt;!-- For BitBucket --&gt;
  &lt;PackageReference Include="Microsoft.SourceLink.Bitbucket.Git" Version="1.0.0-beta-63127-02" PrivateAssets="All"/&gt;

&lt;/ItemGroup&gt;</code></pre>

For example, you can refer to the

[Newtonsoft.Json's implementation][6].

### Using Cake Build Many open source projects use

[Cake Build][7] to package their projects. Here’s how you need to change your `NuGetPackSettings` to allow the source code repository link to be surfaced on NuGet.org:

<pre class="highlight"><code>var nuGetPackSettings = new NuGetPackSettings
    {
        // snipped
        Repository = new NuGetRepository {
                Type = "git",
                Url = "https://github.com/org/repository"
            }
        // snipped
    };</code></pre>

### Conclusion With these small changes to your packaging process, you will quickly get a source code link next to your project. You won't have to choose between your project page or your project repository anymore. For more information on how nuspec and csproj properties are mapped, look at

[our documentation][8]. If you want to reach out to us, you can either [create a new GitHub issue][9] or tag [@nuget][10] in your tweets.

 [1]: https://www.nuget.org/packages/Newtonsoft.Json/
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/source-code-example.png
 [3]: https://github.com/nunit/nunit/blob/master/nuget/framework/nunit.nuspec#L11
 [4]: https://github.com/restsharp/RestSharp/blob/develop/RestSharp/RestSharp.csproj#L8
 [5]: https://github.com/dotnet/sourcelink
 [6]: https://github.com/JamesNK/Newtonsoft.Json/blob/master/Src/Newtonsoft.Json/Newtonsoft.Json.csproj#L24,L38
 [7]: https://cakebuild.net/
 [8]: https://docs.microsoft.com/en-us/nuget/reference/msbuild-targets#pack-target/
 [9]: https://github.com/NuGet/Home/issues/new
 [10]: https://twitter.com/nuget