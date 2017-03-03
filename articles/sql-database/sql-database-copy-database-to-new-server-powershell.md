---
title: Azure PowerShell Script-Copy a SQL database to new server | Microsoft Docs
description: Azure PowerShell Script Sample - Copy a SQL database to a new server using PowerShell
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management

ms.assetid:
ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/01/2017
ms.author: janeng
---

# Copy a SQL database to a new server using PowerShell

This sample PowerShell script creates a copy of an existing database in a new server. 

Before running this script, ensure that a connection with Azure has been created using the `Add-AzureRmAccount` cmdlet.

## Copy a database to a new server

[!code-powershell[main](../../powershell_scripts/sql-database/copy-database-to-new-server/copy-database-to-new-server.ps1 "Copy database to new server")]

## Clean up deployment

After the script sample has been run, the following command can be used to remove the Resource Group and all resources associated with it.

```azurepowershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## Script explanation

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Creates a resource group in which all resources are stored. |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Creates a logical server that hosts a database or elastic pool. |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Creates a database in a logical server as a single or a pooled database. |
| [New-AzureRmSqlDatabaseCopy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabasecopy) | Creates a copy of a database that uses the snapshot at the current time. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Deletes a resource group including all nested resources. |
|||

## Next steps

For more information on the Azure PowerShell, see [Azure PowerShell documentation](https://docs.microsoft.com/powershell/).

Additional SQL Database PowerShell script samples can be found in the [Azure SQL Database PowerShell scripts](sql-database-powershell-samples.md).
