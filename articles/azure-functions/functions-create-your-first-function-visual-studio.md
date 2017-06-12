---
title: Create your first Azure Function using the Azure Functions Tools for Visual Studio 
description: Create an Azure Function using Azure Functions Tools for Visual Studio. 
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: ''
tags: ''
keywords: azure functions, functions, event processing, compute, serverless architecture

ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: ms-hero
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2017
ms.author: rachelap

---
# Create your first function using the Azure Functions Tools for Visual Studio 

Azure Functions lets you execute your code in a serverless environment without having to first create a VM or publish a web application.

In this tutorial, you learn how to create an Azure Function app using **Functions Core Tools** for Visual Studio. HTTP trigger invoke your function, so every time there is an HTTP request to your function's URL, your function runs.   

![Create a new Azure Function](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## Prerequisites

Before running this sample, you must have the following:

* Install [Visual Studio 2017 (version 15.3) Preview](https://www.visualstudio.com/vs/preview/)  with the Azure development workload.
* [Azure Functions Core Tools] (https://marketplace.visualstudio.com/vsgallery/e3705d94-7cc3-4b79-ba7b-f43f30774d28)
* Install the Azure development workload installed.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Create a functions app project in Visual Studio

Open Visual Studio and launch the **New Project** dialog. You'll find the Azure Functions templates under the **Visual C#** > **Cloud** node. Name your function app, select a location, and click **Ok**.

![Create a new Azure Function](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-project.png)

Visual Studio creates a project containing a `local.settings.json` and [`host.json`](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) file. The `local.settings.json` file contains application settings for the storage account and dashboard connections. Storage accounts hold unstructured data, and the SDK uses the dashboard to manage triggers and bindings. 

Now that you have created a function project, which represents a function app, create an HTTP triggered function.

## Create a new function

Right mouse click on the project node in **Solution Explorer**, then choose **Add** > **New Item**. Choose **Azure Function** from the dialog box.

![Create a new Azure Function](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

Select **HttpTrigger** and provide the following information in the next dialog box:

| Setting      | Suggested value  | Description                                        |
| ------------ |  ------- | -------------------------------------------------- |
| Function name  | myFunction | The name of your function.  |
| Access rights | Anonymous | Determines which HTTP requests can access your function  |

![Create a new Azure Function](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

## Test the function locally

Like other Visual Studio Projects, Azure Functions projects support the use of the built-in debugging tools to test your function.

Right-click the project node in **Solution Explorer** and select **Debug** > **Start new instance**. 

![Azure local runtime](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

You can obtain the endpoint for your function by examining the output of the functions runtime as shown above. The local runtime displays the local URL, port, and path to where the function is listening. You can then open a browser and navigate to the function's local URL to verify the function works. Don't forget to append a query string named *name* and a value, for example, "http://localhost:7071/api/HttpTriggerCSharp?name=Azure". The output in the browser window should display the name "Azure". 

When you run the function, the build process creates a `function.json` file with the required bindings for the function's trigger, input, and output bindings.  Visual Studio creates the `function.json` file from the attributes applied to the `Run` method in your code. In the previous example, the bindings are defined in the `HttpTrigger` attribute.

To stop debugging, click the **Stop** button on the Visual Studio toolbar.

## Publish the function to Azure

Right click the project node in **Solution Explorer** and choose **Publish**. This displays a dialog where you configure what you want to publish.

Choose **Create New** to publish this as a new function in a new Function App to publish this function. When ready, click the **Publish**. You'll be presented with the **Create App Service** dialog. 

![Azure local runtime](./media/functions-create-your-first-function-visual-studio/functions-vstools-publish.png)

The table below details the required fields in order to publish your Function App.

| Setting      | Suggested value  | Description                                        |
| ------------ |  ------- | -------------------------------------------------- |
| Function App Name | Globally unique name | The function's name. This must be unique.  |
| Subscription | Choose an existing subscription | The MSDN subscription . |
| Resource Group | myResourceGroup |  The Azure resource group to contain the function. |
| App Service Plan | Existing App Service Plan name |  The function's App Service plan.  |
| Storage Account | Globally unique name |  The storage account for the function. |

Click the **Create** button, and the final confirmation page displays. Click **Publish** to deploy. Verify that your function has been published by logging into the Azure portal and locating the function.

## Next steps

For more information about Azure Functions, see the following topics:

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]
