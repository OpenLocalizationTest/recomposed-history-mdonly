---
title: Azure CLI Script Sample - Scale an Azure Redis Cache to the Premium tier | Microsoft Docs
description: Azure CLI Script Sample - Scale an Azure Redis Cache to the Premium tier
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: 
tags: azure-service-management

ms.assetid: c31a6d6a-85f8-46e9-93fb-1578258c322e
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/06/2017
ms.author: sdanie
---

# Scale an Azure Redis Cache to the Premium tier

In this scenario, you learn how to scale an Azure Redis Cache to the Premium tier.

> [!NOTE]
> For more information on scaling an Azure Redis Cache, including restrictions on scaling, see [How to Scale Azure Redis Cache](../cache-how-to-scale.md).
> 
> 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## Sample script

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/scale-cache-to-premium.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## Script explanation

This script uses the following commands to scale an Azure Redis Cache instance to the Premium tier. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis#delete) | Delete Redis Cache instance. |


## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional Azure Redis Cache CLI script samples can be found in the [Azure Redis Cache documentation](../cli-samples.md).