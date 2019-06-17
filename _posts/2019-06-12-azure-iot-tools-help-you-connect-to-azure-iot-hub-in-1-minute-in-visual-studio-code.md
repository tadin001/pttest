---
ID: 225664
post_title: >
  Azure IoT Tools help you connect to
  Azure IoT Hub in 1 minute in Visual
  Studio Code
author: Jun Han
post_excerpt: ""
layout: post
permalink: >
  https://qadevblogs.wpengine.com/visualstudio/azure-iot-tools-help-you-connect-to-azure-iot-hub-in-1-minute-in-visual-studio-code/
published: true
post_date: 2019-06-12 08:00:14
---
When doing development for Azure IoT solutions, developers may want to test and debug their cloud solution with a real device. However, not every developer has a real device in their hand.  With the [Azure IoT Tools for Visual Studio Code][1], you can easily use Visual Studio Code as a device simulator to quickly interact with Azure IoT Hub. Let’s see how easy it is to send a D2C (device-to-cloud) message in Visual Studio Code! Say Hello to IoT Hub in Visual Studio Code in 1 minute! **Prerequisites** 
1.  If you don’t have an Azure subscription, create a [free account][2] before you begin.
2.  Install [Visual Studio Code][3]
3.  Install the [Azure IoT Tools][1] extension for Visual Studio Code

**Create an IoT Hub** The first step is to create an IoT Hub in your subscription from Visual Studio Code. 
1.  Click **… > Create IoT Hub** at **AZURE IOT HUB** tab, or type **Azure IoT Hub: Create IoT Hub** in Command Palette. (If you want to use an existing IoT Hub, click **… > Select IoT Hub** at **AZURE IOT HUB **tab)
2.  Choose your subscription, resource group, and the closest deploy location to you.
3.  For Pricing and scale tier, select the **F1 – Free** tier if it’s still available on your subscription.
4.  Enter the name of your IoT Hub.
5.  Wait a few minutes until the IoT Hub is created. you can see that your devices status become **No device in …**.

<img class="alignnone size-full wp-image-225668" src="https://devblogs.microsoft.com/visualstudio/wp-content/uploads/sites/4/2019/06/create-hub.gif" alt="" width="940" height="633" /> **Register a device** A device must be registered with your IoT Hub before it can connect. 
1.  Click **… > Create Device** at **AZURE IOT HUB **tab, or type **Azure IoT Hub: Create Device** in Command Palette.
2.  Enter device ID and press **Enter**.
3.  Wait a few seconds until the new device is created.

<img class="alignnone size-full wp-image-225667" src="https://devblogs.microsoft.com/visualstudio/wp-content/uploads/sites/4/2019/06/create-device.gif" alt="" width="940" height="633" /> **Say Hello to IoT Hub (Send D2C message)** Right-click your device and select **Send D2C message to IoT Hub**, then enter the message, results will be shown in **OUTPUT > Azure IoT Hub Toolkit** view. Your ‘Hello World’ is sent to Azure IoT Hub! <img class="alignnone size-full wp-image-225669" src="https://devblogs.microsoft.com/visualstudio/wp-content/uploads/sites/4/2019/06/send-d2c.gif" alt="" width="940" height="633" /> **Monitor IoT Hub D2C message in Visual Studio Code** While you could send message to your IoT Hub, it is also possible to monitor those messages in Visual Studio Code. 
*   Right-click your device and select **Start Monitoring Built-in Event Endpoint**.

<img class="alignnone size-full wp-image-225670" src="https://devblogs.microsoft.com/visualstudio/wp-content/uploads/sites/4/2019/06/start.png" alt="" width="387" height="380" /> 
*   The monitored messages will be shown in **OUTPUT > Azure IoT Hub Toolkit** view.
*   To stop monitoring, right-click the **OUTPUT** view and select **Stop Monitoring Built-in Event Endpoint**.

<img class="alignnone size-full wp-image-225671" style="font-size: 1rem" src="https://devblogs.microsoft.com/visualstudio/wp-content/uploads/sites/4/2019/06/stop.png" alt="" width="806" height="544" /> Is that cool? You could send and receive messages for Azure IoT Hub very easily in Visual Studio Code. Is that all? Not yet! Actually, you could use [Azure IoT Tools extension][1] to do lots of things when you develop with Azure IoT Hub! Checkout our [Wiki Page][4] to see the full list of features and tutorials. You could also use Azure IoT Tools to easily [call Azure IoT Hub REST APIs][5] or [generate Azure IoT application with different languages such as C#, Java, Node.js, PHP, Python][6]. For more IoT tooling announcements and tutorials, please check out [our IoT Developer Blog][7]. Azure IoT Tools make your Azure IoT development easier. Useful Resources: 
*   [Channel 9 video: Walkthrough of Azure IoT Hub Toolkit extension][8]
*   [Channel 9 video: What's new in the IoT Hub Toolkit extension for Visual Studio Code][9]
*   [Send and receive messages between your device and IoT Hub][10]
*   [Azure IoT Hub device management][11]

 [1]: https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools
 [2]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
 [3]: https://code.visualstudio.com/
 [4]: https://github.com/microsoft/vscode-azure-iot-tools/wiki
 [5]: https://devblogs.microsoft.com/iotdev/azure-iot-toolkit-1-2-0-quickly-build-your-azure-iot-application-in-vs-code-with-node-js-python-or-rest-api/
 [6]: https://devblogs.microsoft.com/iotdev/azure-iot-toolkit-supports-c-go-java-node-js-php-python-and-ruby-to-develop-azure-iot-application-in-vs-code/
 [7]: https://devblogs.microsoft.com/iotdev/
 [8]: https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-Toolkit-extension-for-Visual-Studio-Code
 [9]: https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code
 [10]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-vscode-iot-toolkit-cloud-device-messaging
 [11]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-device-management-iot-toolkit