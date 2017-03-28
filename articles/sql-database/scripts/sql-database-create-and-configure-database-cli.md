---
title: Azure CLI Script-Create a SQL database | Microsoft Docs
description: Azure CLI Script Sample - Create a SQL database using the Azure CLI
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management

ms.assetid:
ms.service: sql-database
ms.custom: sample
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/16/2017
ms.author: janeng
---

# Create a single SQL database and configure a firewall rule using the Azure CLI

This sample CLI script creates an Azure SQL database and configure a server-level firewall rule. Once the script has been successfully run, the SQL Database can be accessed from all Azure services and the configured IP address. 

If needed, install the Azure CLI using the instructions found in the [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli), and then run `az login` to create a connection with Azure.

This sample works in a Bash shell. For options on running Azure CLI scripts on Windows, see [Running the Azure CLI in Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## Sample script

[!code-azurecli[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Create SQL Database")]

## Clean up deployment

After the script sample has been run, the following command can be used to remove the resource group and all resources associated with it.

```azurecli
az group delete --name myResourceGroup
```

## Script explanation

This script uses the following commands. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](/cli/azure/group#create) | Creates a resource group in which all resources are stored. |
| [az sql server create](/cli/azure/sql/server#create) | Creates a logical server that hosts the SQL Database. |
| [az sql server firewall create](/cli/azure/sql/server/firewall#create) | Creates a firewall rule to allow access to all SQL Databases on the server from the entered IP address range. |
| [az sql db create](/cli/azure/sql/db#create) | Creates the SQL Database in the logical server. |
| [az group delete](/cli/azure/resource#delete) | Deletes a resource group including all nested resources. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional SQL Database CLI script samples can be found in the [Azure SQL Database documentation](../sql-database-cli-samples.md).

