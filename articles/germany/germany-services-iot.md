---
title: Azure Germany Internet of Things | Microsoft Docs
description: This provides a starting point for the IoT Suite for Azure Germany
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst

ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
---
# Azure IoT services in Azure IoT Suite

All of the required services for Azure IoT Suite are available in Azure Germany. 

## Preconfigured solutions
You might want to start with one of the preconfigured solutions, either *Remote Monitoring* or *Predictive Maintenance*. Those solutions can be deployed in two ways, via website or via PowerShell.

### Variations

The start page for the Azure IoT Suite is different from global Azure. Please use [http://www.azureiotsuite.de](http://www.azureiotsuite.de) and follow the instructions. 

### Deploy solution with PowerShell

There is the full version (using Resource Manager templates and Visual Studio) for the *Remote Monitoring* solution. Download from the [Azure-IoT-Remote-Monitoring repository on GitHub](https://github.com/Azure/azure-iot-remote-monitoring). The PowerShell deployment is ready for other environments like Azure Germany. Just provide the *Environment* parameter "AzureGermanCloud", so it will look similar to this:

    build.cmd cloud debug AzureGermanCloud

For using the Bing Maps you have to make an additional step since this service is currently not available in Azure Germany and therefore cannot be subscribed automatically. You can solve this by subscribing to the service in the global Azure cloud and use the service there (please keep in mind that you are leaving your environment by doing this). Here is how to do it:

1. Create a Bing Maps API in the global Azure portal by clicking `+ New`, search for *Bing Maps API for Enterprise* and follow the prompts to create.
2. Get your Bing Maps API for Enterprise QueryKey from global Azure portal: 
    1. Navigate to the Resource Group where your Bing Maps API for Enterprise is in the global Azure portal.
    2. Click **All Settings**, then **Key Management**. 
    3. You'll see two keys: MasterKey and QueryKey. Copy the value for QueryKey.
3. Pull down the latest code from the [Azure-IoT-Remote-Monitoring repository on GitHub](https://github.com/Azure/azure-iot-remote-monitoring)
4. Run a cloud deployment in your environment following commandline deployment guidance in the `/docs/` folder in the repository. 
5. After you've run the deployment, look in your root folder for the ***.user.config** file created during deployment. Open this file in a text editor. 
6. Change the following line to include the value you copied for your QueryKey: `<setting name="MapApiQueryKey" value="" />`
7. Redeploy the solution by repeating step 4.
 


## Next steps
For supplemental information and updates, subscribe to the 
[Azure Germany Blog](https://blogs.msdn.microsoft.com/azuregermany/).
