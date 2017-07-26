﻿---
title: Create, change, or delete an Azure public IP address | Microsoft Docs
description: Learn how to create, change, or delete a public IP address.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial

---

# Create, change, or delete a public IP address

Learn about a public IP address and how to create, change, and delete one. A public IP address is a resource with its own configurable settings. Assigning a public IP address to other Azure resources enables:
- Inbound Internet connectivity to resources such as Azure Virtual Machines (VM), Azure Virtual Machine Scale Sets, Azure VPN Gateway, Application gateways, and Internet-facing Azure Load Balancers. Azure resources cannot receive inbound communication from the Internet without an assigned public IP address. While some Azure resources are inherently accessible through public IP addresses, other resources must have public IP addresses assigned to them to be accessible from the Internet.
- Outbound connectivity to the Internet using a predictable IP address. For example, a virtual machine can communicate outbound to the Internet without a public IP address assigned to it, but its address is network address translated by Azure to an unpredictable public address. Assigning a public IP address to a resource enables you to know which IP address is used for the outbound connection. Though predictable, the address can change, depending on the assignment method chosen. For more information, see [Create a public IP address](#create-a-public-ip-address). To learn more about outbound connections from Azure resources, read the [Understand outbound connections](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) article.

## Before you begin

Complete the following tasks before completing any steps in any section of this article:

- Review the [Azure limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) article to learn about limits for public IP addresses.
- Log in to the Azure [portal](https://portal.azure.com), Azure command-line interface (CLI), or Azure PowerShell with an Azure account. If you don't already have an Azure account, sign up for a [free trial account](https://azure.microsoft.com/free).
- If using PowerShell commands to complete tasks in this article, [install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Ensure you have the most recent version of the Azure PowerShell commandlets installed. To get help for PowerShell commands, with examples, type `get-help <command> -full`.
- If using Azure command-line interface (CLI) commands to complete tasks in this article, [install and configure the Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Ensure you have the most recent version of the Azure CLI installed. To get help for CLI commands, type `az <command> --help`. Rather than installing the CLI and its pre-requisites, you can use the Azure Cloud Shell. The Azure Cloud Shell is a free Bash shell that you can run directly within the Azure portal. It has the Azure CLI preinstalled and configured to use with your account. To use the Cloud Shell, click the Cloud Shell **>_** button at the top of the [portal](https://portal.azure.com).

Public IP addresses have a nominal charge. To view the pricing, read the [IP address pricing](https://azure.microsoft.com/pricing/details/ip-addresses) page. 

## Create a public IP address

1. Log in to the [Azure portal](https://portal.azure.com) with an account that is assigned (at a minimum) permissions for the Network Contributor role for your subscription. Read the [Built-in roles for Azure role-based access control](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) article to learn more about assigning roles and permissions to accounts.
2. In the box that contains the text *Search resources* at the top of the Azure portal, type *public ip address*. When **Public IP addresses** appears in the search results, click it.
3. Click **+ Add** in the **Public IP address** blade that appears.
4. Enter or select values for the following settings in the **Create public IP address** blade that appears, then click **Create**:

	|Setting|Required?|Details|
	|---|---|---|
	|Name|Yes|The name must be unique within the resource group you select.|
    |IP Version|Yes| Select IPv4 or IPv6. While public IPv4 addresses can be assigned to several Azure resources, an IPv6 public IP address can only be assigned to an Internet-facing load balancer. The load balancer can load balance IPv6 traffic to Azure virtual machines. Learn more about [load balancing IPv6 traffic to virtual machines](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
	|IP address assignment|Yes|**Dynamic:** Dynamic addresses are assigned only after the public IP address is associated to a NIC attached to a VM and the VM is started for the first time. Dynamic addresses can change if the VM the NIC is attached to is stopped (deallocated). The address remains the same if the VM is rebooted or stopped (but not deallocated). **Static:** Static addresses are assigned when the public IP address is created. Static addresses do not change even if the VM is put in the stopped (deallocated) state. The address is only released when the NIC is deleted. You can change the assignment method after the NIC is created. If you select IPv6 for the **IP version**, the only available assignment method is **Dynamic**.|
	|Idle timeout (minutes)|No|How many minutes to keep a TCP or HTTP connection open without relying on clients to send keep-alive messages. If you select IPv6 for **IP Version**, this value can't be changed. |
	|DNS name label|No|Must be unique within the Azure location you create the name in (across all subscriptions and all customers). The Azure public DNS service automatically registers the name and IP address so you can connect to a resource with the name. Azure appends *location.cloudapp.azure.com* (where location is the location you select) to the name you provide to create the fully qualified DNS name. If you choose to create both address versions, the same DNS name is assigned to both the IPv4 and IPv6 addresses. The Azure DNS service contains both IPv4 A and IPv6 AAAA name records and responds with both records when the DNS name is looked up. The client chooses which address (IPv4 or IPv6) to communicate with.|
    |Create an IPv6 (or IPv4) address|No| Whether IPv6 or IPv4 is displayed is dependent on what you select for **IP Version**. For example, if you select **IPv4** for **IP Version**, **IPv6** is displayed here.
    |Name (Only visible if you checked the **Create an IPv6 (or IPv4) address** checkbox)|Yes, if you select the **Create an IPv6** (or IPv4) checkbox.|The name must be different than the name you enter for the first **Name** in this list. If you choose to create both an IPv4 and an IPv6 address, the portal creates two separate public IP address resources, one with each IP address version assigned to it.|
    |IP address assignment (Only visible if you checked the **Create an IPv6 (or IPv4) address** checkbox)|Yes, if you select the **Create an IPv6** (or IPv4) checkbox.|If the checkbox says **Create an IPv4 address**, you can select an assignment method. If the checkbox says **Create an IPv6 address**, you cannot select an assignment method, as it must be **Dynamic**.|
	|Subscription|Yes|Must exist in the same [subscription](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) as the resource you want to associate the public IP address to.|
	|Resource group|Yes|Can exist in the same, or different, [resource group](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) as the resource you want to associate the public IP address to.|
	|Location|Yes|Must exist in the same [location](https://azure.microsoft.com/regions), also referred to as region, as the resource you want to associate the public IP address to.|

**Commands**
Though the portal provides the option to create two public IP address resources (one IPv4 and one IPv6), the following CLI and PowerShell commands create one resource with an address for one IP version or the other. If you want two public IP address resources, one for each IP version, you must run the command twice, specifying different names and versions for the public IP address resources. 

|Tool|Command|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## View, change settings for, or delete a public IP address

1. Log in to the [Azure portal](https://portal.azure.com) with an account that is assigned (at a minimum) permissions for the Network Contributor role for your subscription. Read the [Built-in roles for Azure role-based access control](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) article to learn more about assigning roles and permissions to accounts.
2. In the box that contains the text *Search resources* at the top of the Azure portal, type *public ip address*. When **Public IP addresses** appears in the search results, click it.
3. In the **Public IP addresses** blade that appears, click the name of the public IP address you want to view, change settings for, or delete.
4. In the blade that appears for the public IP address, complete one of the following options depending on whether you want to view, delete, or change the public IP address.
	- **View**: The **Overview** section of the blade shows key settings for the public IP address, such as the network interface it's associated to (if the address is associated to a network interface). The portal does not display the version of the address (IPv4 or IPv6). To view the version information, use the PowerShell or CLI command to view the public IP address. If the IP address version is IPv6, the assigned address is not displayed by the portal, PowerShell, or the CLI. 
	- **Delete**: To delete the public IP address, click **Delete** in the **Overview** section of the blade. If the address is currently associated to an IP configuration, it cannot be deleted. If the address is currently associated with a configuration, click **Dissociate** to dissociate the address from the IP configuration.
	- **Change**: Click **Configuration**. Change settings using the information in step 4 of the [Create a public IP address](#create-a-public-ip-address) section of this article. To change the assignment for an IPv4 address from static to dynamic, you must first dissociate the public IPv4 address from the IP configuration it's associated to. You can then change the assignment method to dynamic and click **Associate** to associate the IP address to the same IP configuration, a different configuration, or you can leave it dissociated. To dissociate a public IP address, in the **Overview** section, click **Dissociate**.

>[!WARNING]
>When you change the assignment method from static to dynamic, you lose the IP address that was assigned to the public IP address. While the Azure public DNS servers maintain a mapping between static or dynamic addresses and any DNS name label (if you defined one), a dynamic IP address can change when the VM is started after being in the stopped (deallocated) state. To prevent the address from changing, assign a static IP address.

**Commands**

|Tool|Command|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#list) to list public IP addresses, [az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#show) to show settings; [az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) to update; [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) to delete|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) to retrieve a public IP address object and view its settings, [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) to update settings; [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) to delete|

## Next steps
Assign public IP addresses when creating the following Azure resources:

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) or [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtual machines
- [Internet-facing Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Site-to-site connection using an Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Virtual Machine Scale Set](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
