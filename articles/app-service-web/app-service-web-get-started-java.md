---
title: Create your first Java web app in Azure in five minutes | Microsoft Docs
description: Learn how easy it is to run web apps in App Service by deploying a sample app. 
services: app-service\web
documentationcenter: ''
author: cephalin
manager: wpickett
editor: ''

ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/06/2017
ms.author: cephalin

---
# Create your first Java web app in Azure in five minutes
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

This QuickStart helps you deploy your first Java web app to [Azure App Service](../app-service/app-service-value-prop-what-is.md) in just a few minutes.

Before starting this QuickStart, ensure that [the Azure CLI is installed](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) on your machine.

## Create a Java web app in Azure
2. Log in to Azure by running `az login` and following the on-screen directions.
   
        az login
   
3. Create a [resource group](../azure-resource-manager/resource-group-overview.md). This is where you put all the Azure resources that you want to manage together, such as 
the web app and its SQL Database back end.

        az group create --location "West US" --name my-first-app-group

    To see what possible values you can use for `---location`, use the `az appservice list-locations` Azure CLI command.

3. Create a "FREE" [App Service plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. Create a new web app with a unique name in `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. Deploy a sample Java app from GitHub.

        az appservice web source-control config --name <app_name> --resource-group my-first-app-group \
        --repo-url "https://github.com/azure-appservice-samples/JavaCoffeeShopTemplate.git" --branch master --manual-integration 

5. To see your app running live in Azure, run this command.

        az appservice web browse --name <app_name> --resource-group my-first-app-group

Congratulations, your first Java web app is running live in Azure App Service.

## Next steps

Explore pre-created [Web apps CLI scripts](../app-service/app-service-cli-samples.md).
