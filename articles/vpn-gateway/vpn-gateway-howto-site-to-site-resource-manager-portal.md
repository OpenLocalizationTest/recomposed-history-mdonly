<properties
   pageTitle="Create a virtual network with a Site-to-Site VPN connection using Azure Resource Manager and the Azure Portal | Microsoft Azure"
   description="This article walks you through creating a VNet using the Resource Manager model and connecting it to your local on-premises network using a S2S VPN gateway connection."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/07/2016"
   ms.author="cherylmc"/>

# Create a resource manager VNet with a Site-to-Site VPN connection using the Azure Portal

> [AZURE.SELECTOR]
- [Azure portal - classic](vpn-gateway-site-to-site-rm-portal.md)
- [PowerShell - Resource Manager](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

This article will walk you through creating a virtual network and a Site-to-Site VPN connection to your on-premises network using the Azure Resource Manager deployment model and the Azure portal. 

![Site-to-Site](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site2site.png)

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)] 

If you want to connect VNets together, but are not creating a connection to an on-premises location, see [Configure a VNet-to-VNet connection](vpn-gateway-vnet-vnet-rm-ps.md).

**About Azure deployment models**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## Before you begin

Verify that you have the following items before beginning configuration.

- A compatible VPN device and someone who is able to configure it. See [About VPN Devices](vpn-gateway-about-vpn-devices.md). If you aren't familiar with configuring your VPN device, or are unfamiliar with the IP address ranges located in your on-premises network configuration, you'll need to coordinate with someone who can provide those details for you.

- An externally-facing public IP address for your VPN device. This IP address cannot be located behind a NAT.
	
- An Azure subscription. If you don't already have an Azure subscription, you can activate your [MSDN subscriber benefits](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) or sign up for a [free account](http://azure.microsoft.com/pricing/free-trial/).

## About this configuration

When using these steps for your environment, be sure to substitute the values for your own. 

### VNet scenario

In the steps below, you will create a VNet, add a gateway subnet, a gateway, a local site, and a connection. This is in addition to configuring your VPN device.

When using these steps as an exercise, you can use the following values:

<a name="values"/>Values for TestVNet1:- not yet verified.

- VNet Name: TestVNet1
- Resource Group: TestRG1
- Location: East US
- TestVNet1 Address Space: 10.11.0.0/16 & 10.12.0.0/16
- Subnets: 
	- FrontEnd: 10.11.0.0/24
	- BackEnd: 10.12.0.0/24
	- GatewaySubnet: 10.12.255.0.0/27
- DNS Server: 8.8.8.8
- GatewayName: VNet1GW (not the same thing as a Gateway Subnet name)
- Public IP: VNet1GWIP
- VPNType: RouteBased
- Connection: VNet1toVNet4
- ConnectionType: Vpn
- Local Site: VNet1GWLocal




## Create a virtual network 

If you already have a virtual network, verify that the settings are correct, then skip to Specify the DNS server.
If you are doing this as an exercise, refer to these [values](#values) when creating your VNet.


[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

### Specify a DNS server

If you are doing this as an exercise, refer to these [values](#values) when specifying your DNS server.

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

### Create a gateway subnet

Before connecting your virtual network to a gateway, you'll first need to create the gateway subnet for the virtual network to which you want to connect. The gateway subnet you create must be named *GatewaySubnet* or it will not work properly.

**Important:** The gateway subnet prefix for some configurations require a subnet of /28 or larger in order to accommodate the number of IP addresses needed in the pool. This means the gateway subnet prefix needs to be /28, /27, /26 etc. You may want to create a larger subnet here in order to accomodate possible future configuration additions.

If you are doing this as an exercise, refer to these [values](#values) when creating your gateway subnet.


[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## Create the gateway

If you are doing this as an exercise, refer to these [values](#values) when creating your gateway.

[AZURE.INCLUDE [vpn-gateway-create-gw-rm-portal](../../includes/vpn-gateway-create-gw-rm-portal-include.md)]

## Add your local site

In a virtual network, the *local site* typically refers to your on-premises location. You'll give that site a name by which Azure can refer to it. This collection of settings is referred to as an Azure Local Network Gateway. You can modify the prefixes that you add here, but there are specific steps to follow when doing so. You can find the information at the end of this article.

If you are doing this as an exercise, refer to these [values](#values) when adding your local site.

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## Configure your VPN device

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## Create the Site-to-Site VPN connection

Next, you'll create the Site-to-Site VPN connection between your virtual network gateway and your VPN device. Be sure to replace the values for your own. The shared key must match the value you used for your VPN device configuration.

If you are doing this as an exercise, refer to these [values](#values) when creating your connection.

[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## Verify the VPN connection

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## To modify IP address prefixes for a local site

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## Next steps

Once your connection is complete, you can add virtual machines to your virtual networks. See [Create a Virtual Machine](../virtual-machines/virtual-machines-windows-tutorial.md) for steps.
