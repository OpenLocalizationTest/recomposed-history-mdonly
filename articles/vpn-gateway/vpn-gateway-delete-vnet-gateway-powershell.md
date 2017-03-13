---
title: 'How to delete a virtual network gateway: PowerShell: Azure Resource Manager | Microsoft Docs'
description: Delete a virtual network gateway using PowerShell in the Resource Manager deployment model. 
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2017
ms.author: cherylmc

---
# Delete a virtual network gateway using PowerShell

There are a couple of different approaches you can take when you want to delete a virtual network gateway for a VPN gateway configuration.

- If you want to delete everything and start over, as in the case of a test environment, you can delete an entire resource group. When you delete a resource group, it deletes all the resources within the group. This is only recommended if you don't want to keep any of the resources in the resource group. You can't selectively delete only a few resources using this approach.

- If you want to keep some of the resources in your resource group, deleting a virtual network gateway becomes slightly more complicated. Before you can delete the virtual network gateway, you must first delete any resources that are dependent on the gateway. The steps you follow depend on the type of connections that you created and the dependent resources for each connection.

##Before beginning

### 1. Download the latest Azure Resource Manager PowerShell cmdlets.
Download and install the latest version of the Azure Resource Manager PowerShell cmdlets. For more information about downloading and installing PowerShell cmdlets, see [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs).

### 2. Connect to your Azure account. 
Open your PowerShell console and connect to your account. Use the following example to help you connect:

    Login-AzureRmAccount

Check the subscriptions for the account.

    Get-AzureRmSubscription

If you have more than one subscription, specify the subscription that you want to use.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"



##<a name="S2S"></a>Delete a Site-to-Site VPN gateway

To delete a virtual network gateway for a S2S configuration, you must first delete each resource that pertains to the virtual network gateway. Resources must be deleted in a certain order due to dependencies. When working with the examples below, some of the values must be specifically called out, while other values are an output result. We use the following specific values in the examples for demonstration purposes:

VNet name: VNet1<br>
Resource Group name: RG1<br>
Virtual network gateway name: GW1<br>


###1. Get the virtual network gateway that you want to delete.

	$Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###2. Check to see if the virtual network gateway has any connections.

	get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
	$Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}

###3. Delete all connections.
You may be prompted to confirm the deletion of each of the connections.

	$Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###4. Get the list of the corresponding local network gateways.

	$LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
	
###5. Delete the local network gateways.
You may be prompted to confirm the deletion of each of the local network gateway.

	$LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}

###6. Delete the virtual network gateway.
You may be prompted to confirm the deletion of the gateway.

	Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###7. Get the IP configurations of the virtual network gateway.

	$GWIpConfigs = $Gateway.IpConfigurations

###8. Get the list of Public IP addresses used for this virtual network gateway 
If the virtual network gateway was active-active, you will see two Public IP addresses.

	$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###9. Delete the Public IPs.

	$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}

###10. Delete the gateway subnet.

	$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
	Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="v2v"></a>Delete a VNet-to-VNet VPN gateway

To delete a virtual network gateway for a V2V configuration, you must first delete each resource that pertains to the virtual network gateway. Resources must be deleted in a certain order due to dependencies. When working with the examples below, some of the values must be specifically called out, while other values are an output result. We use the following specific values in the examples for demonstration purposes:

VNet name: VNet1<br>
Resource Group name: RG1<br>
Virtual network gateway name: GW1<br>


###1. Get the virtual network gateway that you want to delete.

	$Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###2. Check to see if the virtual network gateway has any connections.

	get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
There may be other connections to the virtual network gateway that are part of a different resource group. Check for additional connections in each additional resource group. In this example, we are checking for connections from RG2. Run this for each resource group that you have which may have a connection to the virtual network gateway.

	get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###3. Get the list of connections in both directions.
Because this is a VNet-to-VNet configuration, you need the list of connections in both directions.

	$ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
In this example, we are checking for connections from RG2. Run this for each resource group that you have which may have a connection to the virtual network gateway.
 
	$ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###4. Delete all connections.
You may be prompted to confirm the deletion of each of the connections.

	$ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
	$ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}


###5. Delete the virtual network gateway.
You may be prompted to confirm the deletion of the virtual network gateway.

	Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###6. Get the IP configurations of the virtual network gateway.

	$GWIpConfigs = $GW.IpConfigurations

###7. Get the list of Public IP addresses used for this virtual network gateway. 
If the virtual network gateway was active-active, you will see two Public IP addresses.

	$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###8. Delete the Public IPs.
You may be prompted to confirm the deletion of the Public IP.

	$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###9. Delete the gateway subnet.
 
	$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
	Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub


##<a name="delete"></a>Delete a VPN gateway by deleting the resource group

If you are not concerned about keeping any of your resources and you just want to start over, you can delete an entire resource group. This is a quick way to remove everything. When you delete an entire resource group, it's not selective about the resources that you delete. So make sure that this is what you want to do before running the example.


### 1. Get a list of all the resource groups in your subscription.

	Get-AzureRmResourceGroup
### 2. Locate the resource group that you want to delete.
Locate the resource group that you want to delete and view the list of resources in that resource group. In the example, the name of the resource group is RG1. Modify the example to retrieve a list of all the resources.

	Find-AzureRmResource -ResourceGroupNameContains RG1

### 3. Verify the resources in the list.
When the list is returned, review it to verify that you want to delete all the resources in the resource group, as well as the resource group itself. 


### 4. Delete the resource group and resources.
To delete the resource group and all the resource contained in the resource group, modify the example and run.

	Remove-AzureRmResourceGroup -Name RG1

### 5. Check the status.
It takes some time for Azure to delete all the resources. You can check the status of your resource group by using this cmdlet.

	Get-AzureRmResourceGroup -ResourceGroupName RG1

The result that is returned shows 'Succeeded'.

	ResourceGroupName : RG1
	Location          : eastus
	ProvisioningState : Succeeded

