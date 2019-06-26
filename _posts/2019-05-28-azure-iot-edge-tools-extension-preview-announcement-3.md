---
ID: 226249
post_title: >
  Azure IoT Edge Tools Extension (Preview)
  Announcement
author: seroha
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/azure-iot-edge-tools-extension-preview-announcement-3/
published: true
post_date: 2019-05-28 16:10:46
---
We’re excited to announce the preview availability of the new [Azure IoT Edge Tools Extension (Preview) for Visual Studio 2019][1]. The extension provides a rich set of functionalities to support development of IoT Edge solutions with Visual Studio 2019: <img class="alignnone size-full wp-image-225524" src="https://devblogs.microsoft.com/visualstudio/wp-content/uploads/sites/4/2019/05/iotedge-tools-2019-overview.png" alt="" width="1449" height="270" /> * New Azure IoT Edge project targeting different platforms (Linux amd64, Linux arm32v7, Windows amd64) * Add a new IoT Edge module (C#/C) to solution * Edit, build and debug IoT Edge modules locally on your Visual Studio machine * Build and push docker images of IoT Edge modules * Run IoT Edge modules in a local or remote simulator * Deploy IoT solutions to IoT Edge devices (with [Cloud Explorer][2])

<img class="alignnone size-full wp-image-225493" src="https://devblogs.microsoft.com/visualstudio/wp-content/uploads/sites/4/2019/05/iotedge-tools-2019-demo.gif" alt="" width="2302" height="1311" />

### Prerequisites

*   [Visual Studio 2019][3]: ".NET desktop development" and "Azure development workload" workload installed; "Windows desktop development with C++" is needed if you plan to develop C modules
*   [Docker Desktop][4]. You need to properly set the Docker CE running in Linux container mode or Windows container mode.
*   To set up local development environment to debug, run, and test your IoT Edge solution, you need [Azure IoT EdgeHub Dev Tool][5]. Install [Python (2.7/3.6)][6], then install iotedgehubdev by running below command in your terminal. Make sure your Azure IoT EdgeHub Dev Tool version is greater than 0.8.0. 

<pre class="lang:default decode:true">pip install --upgrade iotedgehubdev</pre>

### Installation There are two options to install the new extension:

*   Download and install the new extension from the [Visual Studio Marketplace][1].
*   Alternatively, you can install the extension directly from within Visual Studio 2019 using the menu **Extensions -> Manage Extensions**. In the Manage Extensions window, select **Online** from the left panel and input **edge** in the search box on the top-right to search and download “Azure IoT Edge Tools for VS 2019 [Preview]”. <img class="alignnone size-full wp-image-225525" src="https://devblogs.microsoft.com/visualstudio/wp-content/uploads/sites/4/2019/05/iotedge-tools-search-vs.png" alt="" width="1185" height="572" />

### How to use this extension? Please refer the following tutorials to get started:

[Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge (Preview) ][7] [Easily Develop and Debug Azure IoT Edge C Modules with Azure IoT Edge Tools][8] [Visual Studio Azure IoT Edge Tools document repo][9] Please don’t hesitate to [give it a try][1]! Your feedback and suggestions are very important for us to keep improving and making it even easier to develop your IoT applications. Please share your thoughts with us by suggesting a feature or reporting an issue in our [Visual Studio Azure IoT Edge Tools repo][10].

 [1]: https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools
 [2]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019
 [3]: https://visualstudio.microsoft.com/downloads/
 [4]: https://www.docker.com/products/docker-desktop
 [5]: https://pypi.org/project/iotedgehubdev/
 [6]: https://www.python.org/
 [7]: https://aka.ms/iotedge-vs-dev-module
 [8]: https://devblogs.microsoft.com/iotdev/easily-develop-and-debug-azure-iot-edge-c-modules-with-azure-iot-edge-tools-preview-0-3-1/
 [9]: https://github.com/microsoft/vs-azure-iot-edge-docs
 [10]: https://github.com/Microsoft/vs-azure-iot-edge-docs