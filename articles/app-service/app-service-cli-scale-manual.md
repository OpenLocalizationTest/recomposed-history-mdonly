---
title: Azure CLI Script Sample - Scale a Web App manually using Azure CLI 2.0 | Microsoft Docs
description: Azure CLI Script Sample - Scale a Web App manually using Azure CLI 2.0
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management

ms.assetid:
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/01/2017
ms.author: cfowler
---

# Scale a Web App manually using Azure CLI 2.0

In this scenario you will learn to create a resource group, app service plan and web app. You will then scale the App Service Plan from a single instance to multiple instances.

Before running this script, ensure that a connection with Azure has been created using the `az login` command.

## Create 

```azurecli
#/bin/bash

# Ensures unique id
random=$(python -c 'import uuid; print(str(uuid.uuid4())[0:8])')

# Variables
resourceGroupName="myResourceGroup$random"
appName="AppServiceManualScale$random"
location="WestUS"

# Create a Resource Group
az group create --name $resourceGroupName --location $location1

# Create App Service Plans
az appservice plan create --name AppServiceManualScalePlan --resource-group $resourceGroupName --location $location --sku B1

# Add a Web App
az appservice web create --name $appName --plan AppServiceManualScalePlan --resource-group $resourceGroupName

# Scale Web App to 2 Workers
az appservice plan update --number-of-workers 2 --name AppServiceManualScalePlan --resource-group $resourceGroupName
```

## Clean up deployment 

After the script sample has been run, the follow command can be used to remove the Resource Group, VM, and all related resources.

```azurecli
az group delete --name myResourceGroup
```

## Script explanation

This script uses the following commands to create a resource group, web app, and all related resources. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Creates a resource group in which all resources are stored. |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | Creates an App Service Plan. This is like a Server Farm for your App Service Web Apps. |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create) | Creates a Web App within the App Service Plan. |
| [az appservice plan update](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#update) | Updates properties of the App Service Plan. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentaiton](https://docs.microsoft.com/en-us/cli/azure/overview).

Additional App Service CLI script samples can be found in the [Azure App Service documentation](https://docs.microsoft.com/en-us/cli/azure/appservice).