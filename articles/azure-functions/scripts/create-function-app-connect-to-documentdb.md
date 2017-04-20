---
title: Create an Azure Function that connects to an Azure DocumentDB | Microsoft Docs
description: Azure CLI Script Sample - Create an Azure Function that connects to an Azure DocumentDB
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
---

# Create an Azure Function that connects to an Azure DocumentDB

This sample script creates an Azure Function App and connects to an Azure DocumentDB database. To run this sample, make sure you have installed the latest [Azure CLI 2.0](cli/azure/install-azure-cli). 

## Sample script

Create app sample

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-documentdb.sh "Create an Azure Function that connects to an Azure DocumentDB")]

## Clean up deployment

After the script sample has been run, the follow command can be used to remove the resource group and all related resources.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## Script explanation

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Login to Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Create a resource group with location |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Create a storage account |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Create a new function app |
| [az documentdb create](https://docs.microsoft.com/cli/azure/documentdb#create) | Create documentdb database |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Clean up |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional Azure Functions CLI script samples can be found in the [Azure Functions documentation](../functions-cli-samples.md).




