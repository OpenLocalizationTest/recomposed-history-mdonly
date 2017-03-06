---
title: Azure PowerShell Script Sample - Connect a web app to a storage account | Microsoft Docs
description: Azure PowerShell Script Sample - Connect a web app to a storage account
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management

ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/23/2017
ms.author: cfowler
---

# Connect a web app to a storage account

In this scenario you will learn how to create an Azure storage account and an Azure web app. Then you will link the storage account to the web app using app settings.

Before running this script, ensure that a connection with Azure has been created using the `Login-AzureRmAccount` cmdlet.

## Sample script

[!code-powershell[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connect a web app to a storage account")]

## Clean up deployment 

After the script sample has been run, the follow command can be used to remove the Resource Group, web app, and all related resources.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## Script explanation

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | Creates a resource group in which all resources are stored. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Creates an App Service plan. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Creates a web app. |
| [New-AzureRMStorageAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.5.0/New-AzureRmStorageAccount) | Creates a Storage account. |
| [Get-AzureRMStorageAccountKey](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.5.0/get-azurermstorageaccountkey) | Gets the access keys for an Azure Storage account. |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | Modifies a web app's configuration. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional Azure Powershell samples for Azure App Service Web Apps can be found in the [Azure PowerShell samples](../app-service-powershell-samples.md).
