---
title: Azure CLI Script Sample - Scale a web app worldwide with a high-availabilty architecture | Microsoft Docs
description: Azure CLI Script Sample - Scale a web app worldwide with a high-availabilty architecture
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

# Scale a web app worldwide with a high-availability architecture using Azure CLI 2.0

In this scenario you will create a resource group, two app service plans, two web apps, a traffic manager profile, and two traffic manager endpoints. Once the exercise is complete you will have a high-available architecture which allows provides global availability of your web app based on the lowest network latency.

Before running this script, ensure that a connection with Azure has been created using the `az login` command.

## Create 

[!code-azurecli[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh?highlight=7-11 "Geographic Scale")]

## Clean up deployment 

After the script sample has been run, the follow command can be used to remove the Resource Group, VM, and all related resources.

```azurecli
az group delete --name myResourceGroup
```

## Script explanation

This script uses the following commands to create a resource group, web app, traffic manager profile, and all related resources. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Creates a resource group in which all resources are stored. |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | Creates an App Service Plan. This is like a Server Farm for your App Service Web Apps. |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create) | Creates a Web App within the App Service Plan. |
| [az network traffic-manager profile create](https://docs.microsoft.com/en-us/cli/azure/network/traffic-manager/profile#create) | Creates a Traffic Manager profile. |
| [az network traffic-manager endpoint create](https://docs.microsoft.com/en-us/cli/azure/network/traffic-manager/endpoint#create) | Adds a endpoint to a Traffic Manager Profile. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentaiton](https://docs.microsoft.com/en-us/cli/azure/overview).

Additional App Service CLI script samples can be found in the [Azure App Service documentation](https://docs.microsoft.com/en-us/cli/azure/appservice).