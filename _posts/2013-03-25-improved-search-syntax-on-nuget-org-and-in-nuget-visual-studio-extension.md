---
ID: 225945
post_title: >
  Improved search syntax on nuget.org and
  in NuGet Visual Studio extension
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/improved-search-syntax-on-nuget-org-and-in-nuget-visual-studio-extension/
published: true
post_date: 2013-03-25 00:00:00
---
The NuGet team recently deployed a new and very useful search capability on nuget.org, which allows you to narrow your search to a pariticular attribute of a NuGet package. For example, if you want to search for packages which contain 'elmah' within the Id, you type '**[id:elmah][1]**'. (Note that this will execute a 'contains' search, not an exact match search. Which means it will return elmah, elmah.corelibrary, etc.)

Similarly you can use the same syntax to search for other attributes in the package metadata. Here's the complete list of supported attributes:

<table cellpadding="8" border="1">
  <thead>
    <tr>
      <td>
        Attribute
      </td>
      
      <td>
        Example
      </td>
    </tr>
  </thead>
  
  <tbody>
    <tr>
      <td>
        id
      </td>
      
      <td>
        <strong>id:jQuery</strong>
      </td>
    </tr>
    
    <tr>
      <td>
        title
      </td>
      
      <td>
        <strong>title:Validation</strong>
      </td>
    </tr>
    
    <tr>
      <td>
        description
      </td>
      
      <td>
        <strong>description:dependency injection</strong>
      </td>
    </tr>
    
    <tr>
      <td>
        authors
      </td>
      
      <td>
        <strong>authors:Outercurve Foundation</strong>
      </td>
    </tr>
    
    <tr>
      <td>
        owners
      </td>
      
      <td>
        <strong>owners:dotnetjunky</strong>
      </td>
    </tr>
    
    <tr>
      <td>
        tags
      </td>
      
      <td>
        <strong>tags:silverlight</strong>
      </td>
    </tr>
  </tbody>
</table>

Note that when a search query contains spaces, the gallery will perform an OR search against words in the query. AND search is currently not supported.

Even better, this feature is automatically available for any NuGet client that calls the Search service method from nuget.org. This can be seen in the Visual Studio Package Manager dialog as shown below.

![][2]

Happy searching!

 [1]: https://nuget.org/packages?q=id%3Aelmah "Search for packages with elmah in Id"
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/improved-search.png