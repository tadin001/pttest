---
ID: 226139
post_title: >
  Welcoming SymbolSource to the .NET
  Foundation
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/welcoming-symbolsource-to-the-net-foundation/
published: true
post_date: 2018-05-01 00:00:00
---
We are excited to welcome SymbolSource.org to the [.NET Foundation][1]! SymbolSource has been providing a valuable service to the .NET Community for years with the ability to host Symbols for public NuGet packages on SymbolSource. With recent progress made in several areas, including SymbolSource being published to [GitHub][2] and NuGet.org planning a symbol server experience, we are thrilled to announce SymbolSource has joined the .NET Foundation. This post is to explain how the SymbolSource symbol server will exist harmoniously with the upcoming NuGet.org symbol server.

With the recent open-sourcing of SymbolSource, the project is looking for new contributors. Head over to the [GitHub issues][3] if you have ideas for new features or other changes to the service, especially if you can spare time to help make them happen!

## Why two Symbol Servers?

The first question that likely comes to mind is - why should we make progress across two symbol servers? That is a great question, and we have been identifying concrete areas where both services can deliver immense value to the .NET Ecosystem. In order to do that, let me describe each of the two services separately, and the value they bring to the community.

### SymbolSource Symbol Server

SymbolSource has been around for a long time providing a huge benefit to the .NET Community by hosting symbols for NuGet packages as a free service. Over time, the service has built up quite a cache of symbols for NuGet packages. While we were in the planning phases for the NuGet.org symbol server, one of our design goals was to ensure that existing scenarios continue to work as is - we don't want to create a gap in the ecosystem by deprecating a service that already contains so many symbol artifacts, especially while we work to spin up a new service. We evaluated several ideas to maintain parity with the existing service, but ultimately realized the simplest solution was the best - just keep the service alive, and continue to make service improvements where they make sense! By recognizing the service in the .NET Foundation, proper administrative guidance and support can help the service to continue delivering the value that it has done so for many years.

That's just one area where SymbolSource is so important. In addition to continue servicing the huge archive of existing symbols, SymbolSource also supports several scenarios that the NuGet.org symbol service will not, including:

1.  The creation and publishing of symbols from existing tools (the hash of the .pdb does not need to be embedded in the .dll)
2.  Support for Windows PDBs 
3.  The ability to consume PDBs in older debugging clients

With the NuGet.org symbol server experience, we deeply evaluated the pros/cons of the existing symbol server infrastructure available, and ultimately decided on some limitations to promote an added layer of security. We recognize that it will take time for the tools to catch up to some of the decisions we have made, and with the .NET Foundation's support, SymbolSource will continue to provide the same service so no scenario is blocked!

The SymbolSource experience will be largely unchanged. You will be able to push Symbols to SymbolSource.org by specifying the push endpoint with NuGet and dotnet push. If you want to consume symbols from the service, you will need to manually reference it in the tools you are using.

### NuGet.org Symbol Server

As many of you are probably already aware, NuGet is planning to improve the [package debugging and symbols experience][4]. A huge part of this investment is spinning up our own symbol server, for various reasons described in the [spec][4]. Some of the areas where the NuGet.org service can add value is in the following:

1.  Build it directly into the tooling, so the client can tell you which packages have symbols available
2.  Make it a default symbol server option in Visual Studio
3.  Optimize the CLI to create/publish symbols packages by default

By streamlining this service with much of the tooling and making it the default, we recognized a need to invest heavily in security (hence some of the limitations). It will take some time for the tools to update to allow everyone to adopt this service, but we ultimately expect it to provide huge opportunity for the entire .NET Ecosystem.

## Conclusion

With the above information in mind, we are excited to welcome SymbolSource to the .NET Foundation, and look forward to being able to support all symbols debugging scenarios for the .NET Community in the future!

 [1]: https://dotnetfoundation.org/blog/2018/05/01/welcoming-symbolsource-to-the-net-foundation
 [2]: https://github.com/SymbolSource/SymbolSource
 [3]: https://github.com/SymbolSource/SymbolSource/issues
 [4]: https://github.com/NuGet/Home/wiki/NuGet-Package-Debugging-&-Symbols-Improvements