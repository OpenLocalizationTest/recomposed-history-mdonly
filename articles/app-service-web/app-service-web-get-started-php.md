---
title: Create your first PHP web app in Azure in five minutes | Microsoft Docs
description: Learn how easy it is to run web apps in App Service by deploying a sample PHP app. 
services: app-service\web
documentationcenter: ''
author: cephalin
manager: wpickett
editor: ''

ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin

---
# Create your first PHP web app in Azure in five minutes
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

This Quickstart helps you deploy your first PHP web app to [Azure App Service](../app-service/app-service-value-prop-what-is.md) in just a few minutes.

Before you start, make sure that the Azure CLI has been installed. For more information, see [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli).

## Log in to Azure
Log in to Azure by running `az login` and following the on-screen directions.
   
```azurecli
az login
```
   
## Create a resource group   
Create a [resource group](../azure-resource-manager/resource-group-overview.md). This is where you put all the Azure resources that you want to manage together, such as 
the web app and its SQL Database back end.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

To see what possible values you can use for `---location`, use the `az appservice list-locations` Azure CLI command.

## Create an App Service plan
Create a "Standard" [App Service plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) that runs a Linux container. 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --is-linux --sku S1
```

## Create a web app
Create a web app with a unique name in `<app_name>`.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## Configure the Linux container
Configure the Linux container to use the default PHP 7.0.6 image.

```azurecli
az appservice web config update --php-version 7.0.6 --name <app_name> --resource-group myResourceGroup
```
## Deploy sample application
Deploy a sample PHP app from GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-php-get-started.git" --branch master --manual-integration 
```

## Browse to web app
To see your app running live in Azure, run this command.

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Congratulations, your first PHP web app is running live in Azure App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## Next steps

Explore pre-created [Web apps CLI scripts](app-service-cli-samples.md).
