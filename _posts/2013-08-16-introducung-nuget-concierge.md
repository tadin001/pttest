---
ID: 166
post_title: Introducing NuGet Concierge
author: Jeff Handley
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/introducung-nuget-concierge/
published: true
post_date: 2013-08-16 00:00:00
---
Twelve weeks ago, Microsoft's Azure Applications Platform & Tools team welcomed three 2nd-year college students, Jaspreet Bagga, Jeremiah Jekich, and Melissa McNeill, and gave them an opportunity to contribute to NuGet.

## Package Discovery

Discovering NuGet packages can be a daunting process. The best way to do so is either via word of mouth or online search. However, your friends aren't always available when you're looking for a new package at 3:00 in the morning. You could try to search online, but you'd need to spend unnecessary amounts of time sifting through the results before finding a package that may be helpful. We recognize that this time is better spent actually developing software. We wanted to create an accessible service to deliver package recommendations using real world data about how developers use packages.

![NuGet Concierge screenshot][1]

## NuGet Concierge

Thus was born [NuGet Concierge][2], a package recommendation service that recommends packages to developers based on the packages currently being used in their project. We envisioned developers being able to upload their project's packages.config file to the NuGet Concierge website, which would then present them with a list of packages they may find useful. Something along the lines of "Most projects that use Package A also use Package B."

So, at the beginning of the summer, we put out a call to the community via Twitter, asking for developers to upload their projects' packages.config files to help seed our newly conceived recommendation service. We asked, and you delivered! Armed with a collection of over 350 packages.config files, the NuGet Concierge project was brought to life.

## Implementation

The first step was to translate the collected .config files into a structure that would allow us to analyze the relationships between packages. How often are individual packages used? But, more importantly, how are packages used together?

So, we took the community's .config files and parsed them, using them to construct a graph. In doing so, we tracked the number of times a package was used, a value we referred to as the package's "popularity." We also tracked how many times two packages were used together, which we referred to as the packages' "pairing frequency."

## Determining Relationships

Let's say we have two packages, EntityFramework and jQuery. By looking at our graph, we know the popularity of EntityFramework and the popularity of jQuery, as well as the pairing frequency between the two. In order to determine the strength of EntityFramework's relationship with jQuery, we take the pairing frequency between the two and divide it by the popularity of EntityFramework. This gives us the percentage of time EntityFramework is used with jQuery. If we want to know how strongly jQuery is associated with EntityFramework, we simply take the pairing frequency and divide it by the popularity of jQuery.

![Package Relationships][3]

## Making Recommendations

Suppose a developer has a packages.config file that contains knockoutjs. In order to make recommendations, we look at all packages used with knockoutjs that are not currently in use in the developer's project. In this case, let's say knockoutjs is associated with Newtonsoft.Json and Modernizr at association strengths of 91% and 87% respectively. We would recommend Newtonsoft.Json first, followed by Modernizr.

![Package Graph][4]

With NuGet Concierge, we made every effort to enable improvements over time. When users upload their packages.config file, we present them with the option to contribute their file to the NuGet Concierge dataset. This increases the pool of .config files from which we generate our graph. This means that our recommendations become more accurate the more NuGet Concierge is used.

Additionally once users are presented with recommendations, they have the option to "Like" or "Dislike" each recommendation by giving it a thumbs up or a thumbs down. This information gets stored and allows us to analyze the usefulness of the recommendations we are making. If we find some packages are being recommended at a high association strength, but most users deem them unhelpful, we can adjust the system to recommend the packages at more appropriate strengths. Likewise, we can give a strength boost to packages recommended at low association strengths but deemed helpful by the community.

![Voting][5]

## NuGet Concierge's Potential

NuGet Concierge is just a conceptual prototype at the moment. But if the concept proves to be valuable, we imagine NuGet Concierge as a fully integrated part of NuGet, having a presence in the Gallery, Visual Studio's Manage NuGet Packages dialog, and the Package Manager Console. The greatest part of NuGet Concierge is the data powering it. The ability to reference real data about how packages are actively used together opens up a world of opportunities that can potentially help NuGet better serve developers.

Until then, please feel free to try out NuGet Concierge at <http://concierge.nuget.org> and let us know if you like the concept and/or the recommendations. You can also see the code for NuGet Concierge at <http://github.com/NuGet/Concierge>.

 [1]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/screenshot.png
 [2]: http://concierge.nuget.org
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/relationships.png
 [4]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/graph.png
 [5]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/voting.png