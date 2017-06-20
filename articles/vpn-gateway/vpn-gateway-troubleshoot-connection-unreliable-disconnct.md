---
title: VPN client disconnects Azure virtual network randomly| Microsoft Docs
description: Learn how to troubleshoot Azure VPN disconnection problems.
services: vpn-gateway
documentationcenter: na
author: genlin
manager: willchen
editor: ''
tags: ''

ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/16/2017
ms.author: genli
---

# Troubleshoot Azure VPN disconnection problems

You configure the Site-to-Site VPN connection between the on-premise network and Microsoft Azure virtual network. You may find the connection between VPN clients and Azure virtual network is not reliably. It disconnects randomly. This article provides troubleshoot steps to help you identify the cause of the problem.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Troubleshooting guidance 

## Troubleshooting steps

### Step 1 Check if a validated on-premises VPN device is used

1. Check if you are using a [validated VPN device and OS version](vpn-gateway-about-vpn-devices.md#validated-vpn-devices). If it is not a valiated VPN device, you may need to contact device manufacturer to check if there is any compatibility issue.
2. Make sure that the VPN device is in a correct confirguatin. For more informaton, see [Editing device configuration samples](vpn-gateway-about-vpn-devices.md#editing)

### Step 2 Check Security Association Mismatches (for policy-based Azure Gateways)

1. Make sure that the virtual network, subnets and ranges in the **Local network** definition in Microsoft Azure are same as the confirguation on the on-premises VPN device.
2. Make sure that the Security Association settings are matching.

### Step 3 Check one VPN Tunnel per Subnet Pair (for policy-based Azure Gateways)

Make sure that the VPN device is set to have **one VPN tunnel per subnet pair** for policy-based gateways.

### Step 4 Check for Security Association Limitation (for policy-based Azure Gateways)

The Policy-based Azure gateway has limit of 200 subnet Security Association pairs. If the number of Azure virtual network subnets multiplied times the number of local subnets is greater than 200, you will see sporadic subnets disconnecting.

### Step 5 Check on-premises VPN device External Interface Address

- If the VPN device internet facing IP address is included within the "local network" definition in Azure, you may experience sporadic disconnects.
- The device external interface must be directly on the internet. There should be no Network Address Translation or firewall between the Internet and the device.
-  If you configure Firewall Clustering with Virtual IP,  you must break the cluster and expose the VPN appliance directly to a public interface that our Azure Gateway can interface with.

### Step 6 Check if the Azure Gateway is Over Utilized

Check CPU and Bandwidth Utilization of the Azure VPN Gateway instances:

- Network and CPU utilization on VM instances
- Site-to-Site Tunnel Bandwidth
- With high utilization, it may resolve to resize to [higher SKU gateway](vpn-gateway-about-vpngateways#gateway-skus)

### Step 7 Check Azure Gateway Upgrade

 if the VPN connection disconnects just one time, but then immediately comes back on. It may be caused by the Azure Gateway Upgrade.

 ### Step 8 Check if the on-premises VPN device has Perfect forward Secrecy enabled

The **Perfect forward Secrecy** feature can cause the disconnection problems. If the VPN device has **Perfect forward Secrecy** enabled, disable the feature. Then [update the Azure Gateway IPsec policy](vpn-gateway-ipsecikepolicy-rm-powershell.md).

### Step 9 Check for User Defined Routes or Network Security Groups on Gateway Subnet

A User-defined route on the gateway subnet may be restricting some traffic and allowing other traffic giving the appearance that the VPN connection is unreliable for some traffic and ok for others. 



