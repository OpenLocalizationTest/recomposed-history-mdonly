---
title: Azure CLI Script-Get account keys for DocumentDB | Microsoft Docs
description: Azure CLI Script Sample - Get account keys for DocumentDB
services: documentdb
documentationcenter: documentdb
author: mimig1
manager: jhubbard
editor: ''
tags: azure-service-management

ms.assetid:
ms.service: documentdb
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: documentdb
ms.workload: database
ms.date: 04/16/2017
ms.author: mimig
---

# Get account keys for DocumentDB using the Azure CLI

*Content below is from SQL DB - need to replace with DocumentDB specifics*

This sample CLI script creates an Azure DocumentDB account, database and collection. Once the script has been successfully run, the DocumentDB database can be accessed from all Azure services. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

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
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create) | Creates a firewall rule to allow access to all SQL Databases on the server from the entered IP address range. |
| [az sql db create](/cli/azure/sql/db#create) | Creates the SQL Database in the logical server. |
| [az group delete](/cli/azure/resource#delete) | Deletes a resource group including all nested resources. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional DocumentDB CLI script samples can be found in the [Azure DocumentDB documentation](../documentdb-cli-samples.md).
