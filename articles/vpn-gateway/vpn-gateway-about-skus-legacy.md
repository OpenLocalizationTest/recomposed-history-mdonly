---
title: Legacy Azure virtual network gateway SKUs | Microsoft Docs
description: Old virtual network gateway SKUs.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management

ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc

---
# Working with virtual network gateway SKUs (legacy SKUs)

This article contains information about the legacy virtual network gateway SKUs. For information on the current SKUs, see [About VPN Gateway](vpn-gateway-about-vpngateways.md).

> [!NOTE]
> You can't create, upgrade, or migrate a classic virtual network gateway to the new gateway SKUs.
>
>

## Gateway SKUs

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## Estimated aggregate throughput by SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## Supported configurations by SKU and VPN type

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Resize a gateway (change a gateway SKU)

You can't resize your VPN gateways between the old SKUs and the new SKU families. For example, you can't go from a Standard SKU to a VpnGw2 SKU. However, you can resize within the same SKU family. For example, if you have a Standard SKU, you can resize to a HighPerformance SKU using the following command:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="migrate"></a>Migrate to the new gateway SKUs

If you are working with the Resource Manager deployment model, you can migrate to the new gateway SKUS. If you are working with the classic deployment model, you can't migrate to the new SKUs and must instead continue to use the legacy SKUs.

[!INCLUDE [Migrate SKU](../../includes/vpn-gateway-migrate-legacy-sku-include.md)]

Next steps

For more information about the new Gateway SKUs, see [Gateway SKUs](vpn-gateway-about-vpngateways.md#gwsku).