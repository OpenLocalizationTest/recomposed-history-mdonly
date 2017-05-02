---
title: Azure CLI Script-Regenerate Azure Cosmos DB account key | Microsoft Docs
description: Azure CLI Script Sample - Regenerate an Azure Cosmos DB account key
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
ms.date: 04/20/2017
ms.author: mimig
---

# Regenerate an Azure Cosmos DB account key using the Azure CLI

This sample regenerates any kind of Azure Cosmos DB account key using the Azure CLI. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## Sample script

[!code-azurecli[main](../../../cli_scripts/documentdb/secure-documentdb-regenerate-keys/secure-documentdb-regenerate-keys.sh?highlight=30-34 "Regenerate Azure Cosmos DB account keys")]

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
| [az documentdb create](/cli/azure/documentdb/name#create) | Updates a DocumentDB account. |
| [az documentdb regenerate-key](/cli/azure/documentdb/regenerate-key) | Regeneratates DocumentDB account keys. |
| [az group delete](/cli/azure/resource#delete) | Deletes a resource group including all nested resources. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional Azure Cosmos DB CLI script samples can be found in the [Azure Cosmos DB CLI documentation](../documentdb-cli-samples.md).
