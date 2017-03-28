---
title: Create your first ASP.NET web app in Azure in five minutes | Microsoft Docs
description: Learn how easy it is to run web apps in App Service by deploying a simple ASP.NET application. 
services: app-service\web
documentationcenter: ''
author: cephalin
manager: wpickett
editor: ''

ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: cephalin

---
# Create your first ASP.NET web app in Azure in five minutes

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

This Quickstart helps you deploy your first ASP.NET web app to [Azure App Service](../app-service/app-service-value-prop-what-is.md) in just a few minutes. When you're finished, you'll have a simple web application up and running in the cloud.

![Updated ASP.NET application in Azure App Service](./media/app-service-web-get-started-dotnet/updated-azure-web-app.png)

This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.

## Before you begin

This tutorial demonstrates how to use Visual Studio 2017 to build and deploy an ASP.NET web app to Azure. If you don’t already have Visual Studio 2017 installed, you can download and use the free [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Make sure that you enable **Azure development** during the Visual Studio setup.

## Create an ASP.NET application

In Visual Studio, create a new project with `Ctrl`+`Shift`+`N`.

In the **New Project** dialog, click **Visual C# > Web > ASP.NET Web Application (.NET Framework)**.

Name the application **myFirstAzureWebApp**, and then click **OK**.
   
![New Project dialog box](./media/app-service-web-get-started-dotnet/new-project.png)

You can deploy any type of ASP.NET application to Azure. For this tutorial, select the **MVC** template, and make sure authentication is set to **No Authentication**.
      
Click **OK**.

![New ASP.NET Project dialog box](./media/app-service-web-get-started-dotnet/select-mvc-template.png)

## Publish to Azure

In the **Solution Explorer**, right-click your **myFirstAzureWebApp** project and select **Publish**.

![Publish from Solution Explorer](./media/app-service-web-get-started-dotnet/solution-explorer-publish.png)

Make sure that **Microsoft Azure App Service** is selected and click **Publish**.

![Publish from project overview page](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

This opens the **Create App Service** dialog, which helps you create all the Azure resources you need to run your ASP.NET application in Azure App Service.

## Sign in to Azure

In the **Create App Service** dialog, click **Add an account**, and then sign in to your Azure subscription. If you're already signed into a Microsoft account, make sure that account holds your Azure subscription. If the signed-in Microsoft account doesn't have your Azure subscription, click it to add the correct account.
   
![Sign in to Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

Once signed in, you're ready to create all the resources you need for your Azure web app in this dialog.

## Create a resource group

First, you need a _resource group_. A resource group is a logical container into which Azure resources like web apps, databases and storage accounts are deployed and managed.

Next to **Resource Group**, click **New**. name your resource group **myResourceGroup** and click **OK**.

## Create an App Service plan

Your Azure web app also needs an _App Service plan_. An App Service plan represents the collection of physical resources used to host your apps. All apps assigned to an App Service plan share the resources defined by it, which enables you to save cost when hosting multiple apps. 

App Service plans define:

- Region (North Europe, East US, Southeast Asia)
- Instance Size (Small, Medium, Large)
- Scale Count (one, two or three instances, etc.) 
- SKU (Free, Shared, Basic, Standard, Premium)

Next to **App Service Plan**, click **New**. 

In the **Configure App Service Plan** dialog, configure the new App Service plan with the following settings:

- **App Service Plan**: Type **myAppServicePlan**. 
- **Location**: Choose **West Europe**, or any other region you like.
- **Size**: Choose **Free**, or any other [pricing tier](https://azure.microsoft.com/pricing/details/app-service/) you like.

When you're finished, the dialog should look like the following screenshot:

![Configure new App Service plan](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

Click **OK**.

## Create and publish the web app

The only thing left to do now is to name your web app. In **Web App Name**, type a unique app name. You can also accept the automatically generated name, which is already unique.

When you're finished, your **Create App Service** dialog should look like the following screenshot:

![Configure web app name](./media/app-service-web-get-started-dotnet/web-app-name.png)

Click **Create** to start creating the Azure resources.

Once the wizard finishes creating the Azure resources, it automatically publishes your ASP.NET application to Azure for the first time, and then launches the published Azure web app in your default browser.

![Published ASP.NET application in Azure App Service](./media/app-service-web-get-started-dotnet/published-azure-web-app.png)

The URL uses the web app name that you specified earlier, with the format `http://<app_name>.azurewebsites.net`. 

Congratulations, your first ASP.NET web app is running live in Azure App Service.

## Update the app and redeploy

Whenever you make an update to your ASP.NET application, it is very easy to redeploy to Azure. Let's make an update to the homepage.

From the **Solution Explorer**, open **Views\Home\Index.cshtml**.

Find the `<div class="jumbotron">` XML element near the top, and replace the entire element with the following code:

```xml
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

To redeploy to Azure, right-click youre **myFirstAzureWebApp** project in **Solution Explorer** and select **Publish**.

In the publish page, click **Publish**.

When Visual Studio is finished, it launches the published Azure web app again in your browser.

![Updated ASP.NET application in Azure App Service](./media/app-service-web-get-started-dotnet/updated-azure-web-app.png)

## Next steps

Explore pre-created [Web apps PowerShell scripts](app-service-powershell-samples.md).
