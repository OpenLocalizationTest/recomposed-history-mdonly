---
title: Azure CLI Script Sample - Map a custom domain to a web app | Microsoft Docs
description: Azure CLI Script Sample - Map a custom domain to a web app
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management

ms.assetid: 5ac4a680-cc73-4578-bcd6-8668c08802c2
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cephalin
---

# Map a custom domain to a web app

This sample script creates a web app in App Service with its related resources, and then maps `www.<yourdomain>` to it. 

If needed, install the Azure CLI using the instruction found in the [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli), and then run `az login` to create a connection with Azure. Also, you need to have access to your domain registrar's DNS configuration page.

This sample works in a Bash shell. For options on running Azure CLI scripts on Windows client, see [Running the Azure CLI in Windows](../../virtual-machines/windows/cli-options.md).

## Sample script

[!code-azurecli[main](../../../cli_scripts/app-service/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## Script explanation

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Creates a resource group in which all resources are stored. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Creates an App Service plan. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Creates an Azure web app. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Update an App Service plan to scale its pricing tier. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional App Service CLI script samples can be found in the [Azure App Service documentation](../app-service-cli-samples.md).