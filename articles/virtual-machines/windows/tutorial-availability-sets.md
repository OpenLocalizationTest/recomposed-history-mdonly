---
title: Availability sets tutorial for Windows VMs in Azure | Microsoft Docs
description: Learn about the Availability Sets for Windows VMs in Azure.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: cynthn

---

# How to use availability sets

In this tutorial, you learn about increasing the availability of your virtual machines (VMs) by putting them into a logical grouping called an availability set. When you create VMs within an availability set, the Azure platform distributes the VMs across the underlying infrastructure. If there is a hardware fault or planned maintenance on the platform, the use of availability sets ensures that at least one VM remains running.

The steps in this tutorial can be completed using the latest [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) module.

## Availability set overview

Virtual machines can be created across logical groupings of hardware in the underlying Azure datacenter. When you create two or more VMs, your compute and storage resources are distributed across hardware, such as servers, network switches and storage. This distribution maintains the availability of your app should a hardware component undergo maintenance. Availability sets let you define this logical grouping.

Availability sets provide high availability to the VMs. You should also ensure your applications are also designed to tolerate outages or maintenance events.

## Create an availability set

You can create an availability set using [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). In this example, we set both the number of update and fault domains at *2* for the availability set named *myAvailabilitySet* in the *myResourceGroupAvailability* resource group.


```powershell
New-AzureRmAvailabilitySet `
   -Location westus `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## Create VMs inside an availability set

VMs need to be created within the availability set to make sure they are correctly distributed across the hardware. You can't add an existing VM to an availability set after it is created. 

The hardware in a location is divided in to multiple update domains and fault domains. An **update domain** is a group of VMs and underlying physical hardware that can be rebooted at the same time. VMs in the same **fault domain** share common storage as well as a common power source and network switch. 

When you create a VM using configuration using [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) you specify the availability set using the `-AvailabilitySetId` parameter to specify the ID of the availability set.

Create 2 VMs with [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) in the availability set.

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location westus `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location westus `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location westus `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location westus `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location westus `
        -VM $vm
}

```

It takes a few minutes to create and configure both VMs. When finished, you will have 2 virtual machines distributed across the underlying hardware. 

## Check for available VM sizes 

You can add more VMs to the availability set later, but you need to know what VM sizes are available on the hardware. Use [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) to list all the available sizes on the hardware cluster for the availability set.

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## Next steps

In this tutorial, you learned how to create a virtual machine scale set. Advance to the next tutorial to learn about virtual machine scale sets.

[Create a VM scale set](tutorial-create-vmss.md)


