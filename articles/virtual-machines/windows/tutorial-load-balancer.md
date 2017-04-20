---
title: How to load balance Windows virtual machines in Azure | Microsoft Docs
description: Learn how to use the Azure load balancer to create a highly available and secure application across three Windows VMs
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/17/2017
ms.author: iainfou
---

# How to load balance Windows virtual machines in Azure to create a highly available application
In this tutorial, you learn about the different components of the Azure load balancer that distribute traffic and provide high availability. To see the load balancer in action, you can build a simple IIS web site that runs on three Windows virtual machines (VMs).

The steps in this tutorial can be completed using the latest [Azure PowerShell](/powershell/azureps-cmdlets-docs/) module.


## Azure load balancer overview
An Azure load balancer is a Layer-4 (TCP, UDP) load balancer that provides high availability by distributing incoming traffic among healthy VMs. A load balancer health probe monitors a given port on each VM and only distributes traffic to an operational VM.

You define a front-end IP configuration that contains one or more public IP addresses. This front-end IP configuration allows your load balancer and applications to be accessible over the Internet. 

Virtual machines connect to a load balancer using their virtual network interface card (NIC). To distribute traffic to the VMs, a back-end address pool contains the IP addresses of the virtual (NICs) connected to the load balancer.

To control the flow of traffic, you define load balancer rules for specific ports and protocols that map to your VMs.


## Create Azure load balancer
This section details how you can create and configure each component of the load balancer. Before you can create your load balancer, create a resource group with [New-AzureRmResourceGroup](/powershell/resourcemanager/AzureRM.Resources/new-azurermresourcegroup). The following example creates a resource group named `myRGLoadBalancer` in the `westus` location:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGLoadBalancer -Location westus
```

### Create a public IP address
To access your app on the Internet, you need a public IP address for the load balancer. Create a public IP address with [New-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/new-azurermpublicipaddress). The following example creates a public IP address named `myPublicIP` in the `myRGLoadBalancer` resource group:

```powershell
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGLoadBalancer `
  -Location westus `
  -AllocationMethod Static `
  -Name myPublicIP
```

### Create a load balancer
Create a frontend IP address with [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/resourcemanager/azurerm.network/new-azurermloadbalancerfrontendipconfig). The following example creates a frontend IP address named `myFrontEndPool`: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name myFrontEndPool -PublicIpAddress $publicIP
```

Create a backend address pool with [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/resourcemanager/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). The following example creates a backend address pool named `myBackEndPool`:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Now, create the load balancer with [New-AzureRmLoadBalancer](/powershell/resourcemanager/azurerm.network/new-azurermloadbalancer). The following example creates a load balancer named `myLoadBalancer` using the `myPublicIP` address:

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myRGLoadBalancer `
  -Name myLoadBalancer `
  -Location westus `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### Create a health probe
To allow the load balancer to monitor the status of your app, you use a health probe. The health probe dynamically adds or removes VMs from the load balancer rotation based on their response to health checks. By default, a VM is removed from the load balancer distribution after two consecutive failures at 15-second intervals. You create a health probe based on a protocol or a specific health check page for your app. 

The following example creates a TCP probe. You can also create custom HTTP probes for more fine grained health checks. When using a custom HTTP probe, you must create the health check page, such as `healthcheck.aspx`. The probe must return an **HTTP 200 OK** response for the load balancer to keep the host in rotation.

To create a TCP health probe, you use [Add-AzureRmLoadBalancerProbeConfig](/powershell/resourcemanager/azurerm.network/add-azurermloadbalancerprobeconfig). The following example creates a health probe named `myHealthProbe` that monitors each VM:

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### Create a load balancer rule
A load balancer rule is used to define how traffic is distributed to the VMs. You define the front-end IP configuration for the incoming traffic and the back-end IP pool to receive the traffic, along with the required source and destination port. To make sure only healthy VMs receive traffic, you also define the health probe to use.

Create a load balancer rule with [Add-AzureRmLoadBalancerRuleConfig](/powershell/resourcemanager/AzureRM.Network/add-azurermloadbalancerruleconfig). The following example creates a load balancer rule named `myLoadBalancerRule` and balances traffic on port `80`:

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

Update the load balancer with [Set-AzureRmLoadBalancer](/powershell/resourcemanager/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```


## Configure virtual network
Before you deploy some VMs and can test your balancer, create the supporting virtual network resources. For more information about virtual networks, see the [Manage Azure Virtual Networks](tutorial-virtual-network.md) tutorial.

### Create network resources
Create a virtual network with [New-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/new-azurermvirtualnetwork). The following example creates a virtual network named `myVnet` with `mySubnet`:

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGLoadBalancer `
  -Location westus `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Create a network security group rule with [New-AzureRmNetworkSecurityRuleConfig](/powershell/resourcemanager/azurerm.network/new-azurermnetworksecurityruleconfig), then create a network security group with [New-AzureRmNetworkSecurityGroup](/powershell/resourcemanager/azurerm.network/new-azurermnetworksecuritygroup). Add the network security group to the subnet with [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/resourcemanager/azurerm.network/set-azurermvirtualnetworksubnetconfig) and then update the virtual network with [Set-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/set-azurermvirtualnetwork). 

The following example creates a network security group rule named `myNetworkSecurityGroup` and applies it to `mySubnet`:

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGLoadBalancer `
  -Location westus `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

Virtual NICs are created with [New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/new-azurermnetworkinterface). The following example creates three virtual NICs. (One virtual NIC for each VM you create for your app in the following steps). You can create additional virtual NICs and VMs at any time and add them to the load balancer:

```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myRGLoadBalancer `
     -Name myNic$i `
     -Location westus `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```

## Create virtual machines
To improve the high availability of your app, place your VMs in an availability set. For more information about availability sets, see the previous [How to create highly available virtual machines](tutorial-availability-sets.md) tutorial.

Create an availability set with [New-AzureRmAvailabilitySet](/powershell/resourcemanager/azurerm.compute/new-azurermavailabilityset). The following example creates an availability set named `myAvailabilitySet`:

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myRGLoadBalancer `
  -Name myAvailabilitySet `
  -Location westus `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

Set an administrator username and password for the VMs with [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Now you can create the VMs with [New-AzureRmVMConfig](/powershell/resourcemanager/azurerm.compute/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](/powershell/resourcemanager/azurerm.compute/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](/powershell/resourcemanager/azurerm.compute/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](/powershell/resourcemanager/azurerm.compute/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.computeadd-azurermvmnetworkinterface), and [New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/new-azurermvm).

The following example creates three VMs:

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myRGLoadBalancer -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myRGLoadBalancer -Location westus -VM $vm
}
```

It takes a few minutes to create and configure all three VMs.

### Install the app 
In a previous tutorial on [How to customize a Windows virtual machine on first boot](tutorial-automate-vm-deployment.md), you learned how to automate VM customization with the custom script extension for Windows. You can use the same approach to install and configure IIS on your VMs.

Use [Set-AzureRmVMExtension](/powershell/resourcemanager/azurerm.compute/set-azurermvmextension) to install the custom script extension. The extension runs `powershell Add-WindowsFeature Web-Server` to install the IIS webserver and then updates the `Default.htm` page to show the hostname of the VM:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension -ResourceGroupName myRGLoadBalancer `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location westus
}
```

## Test load balancer
Obtain the public IP address of your load balancer with [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). The following example obtains the IP address for `myPublicIP` created earlier:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myRGLoadBalancer -Name myPublicIP | select IpAddress
```

You can then enter the public IP address in to a web browser. The website is displayed, including the hostname of the VM that the load balancer distributed traffic to as in the following example:

![Running IIS website](./media/tutorial-load-balancer/running-iis-website.png)

To see the load balancer distribute traffic across all three VMs running your app, you can force-refresh your web browser.


## Add and remove VMs
You may need to perform maintenance on the VMs running your app, such as installing OS updates. To deal with increased traffic to your app, you may need to add additional VMs. This section shows you how to remove or add a VM from the load balancer.

### Remove a VM from the load balancer
Get the network interface card with [Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/get-azurermnetworkinterface), then set the `LoadBalancerBackendAddressPools` property of the virtual NIC to `$null`. Finally, update the virtual NIC.:

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myRGLoadBalancer -Name myNic2
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

To see the load balancer distribute traffic across the remaining two VMs running your app you can force-refresh your web browser. You can now perform maintenance on the VM, such as installing OS updates or performing a VM reboot.

### Add a VM to the load balancer
After performing VM maintenance, or if you need to expand capacity, set the `LoadBalancerBackendAddressPools` property of the virtual NIC to the `BackendAddressPool` from [Get-AzureRMLoadBalancer]():

Get the load balancer:

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myRGLoadBalancer -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## Next steps

Further reading:

- [Azure Load Balancer overview](../../load-balancer/load-balancer-overview.md)
- [Control network traffic flow with network security groups](../../virtual-network/virtual-networks-nsg.md)
- [Azure CLI sample scripts](../windows/cli-samples.md)
