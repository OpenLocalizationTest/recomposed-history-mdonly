---
title: Create and Manage Windows VMs with the Azure PowerShell module | Microsoft Docs
description: Tutorial - Create and Manage Windows VMs with the Azure PowerShell module
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management

ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: nepeters
---

# Create and Manage Windows VMs with the Azure PowerShell module

In this tutorial, you will learn about basic Azure Virtual Machine creation operations such as selecting a VM size, selecting an image for the VM, and how to deploy the virtual machine. The tutorial will cover basic management operations such and stopping, starting, deleting and resizing a VM 

The steps in this tutorial can be completed using the latest [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) module.

## Create resource group

Create a resource group with the [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-3.8.0) command. 

An Azure resource group is a logical container into which Azure resources are deployed and managed. A resource group must be created before a virtual machine. In this example, a resource group named `myRGManageVM ` is created in the `westus` region. 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGManageVM -Location westeurope
```
## Create virtual machine

A virtual machine must be connected to a virtual network. You communicate with the virtual machine using a public IP address through a network interface card.

### Create virtual network

Create a subnet with [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermvirtualnetworksubnetconfig):

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
```

Create a virtual network with [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermvirtualnetwork):

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGManageVM `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 ` 
  -Subnet $subnetConfig
```
### Create public IP address

Create a public IP address with [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermpublicipaddress):

```powershell
$pip = New-AzureRmPublicIpAddress ` 
  -ResourceGroupName myRGManageVM `
  -Location westeurope ` 
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### Create network interface card

Create a network interface card with [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermnetworkinterface):

```powershell
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGManageVM  `
  -Location westeurope `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### Create network security group

An Azure [network security group](../../virtual-network/virtual-networks-nsg.md) (NSG) controls inbound and outbound traffic for one or many virtual machines. Network security group rules allow or deny network traffic on a specific port or port range. These rules can also include a source address prefix so that only traffic originating at a predefined source can communicate with a virtual machine. To access the IIS webserver that you are installing, you must add an inbound NSG rule.

To create an inbound NSG rule, use [Add-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/add-azurermnetworksecurityruleconfig). The following example creates an NSG rule named `myNSGRule` that opens port `3389` for the virtual machine:

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

Create the NSG using `myNSGRule` with [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermnetworksecuritygroup):

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myRGManageVM  -Location westeurope -Name myNetworkSecurityGroup -SecurityRules $nsgRule
```

Add the NSG to the subnet in the virtual network with [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/set-azurermvirtualnetworksubnetconfig):

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -VirtualNetwork $vnet -NetworkSecurityGroup $nsg -AddressPrefix 192.168.1.0/24
```

Update the virtual network with [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/set-azurermvirtualnetwork):

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### Create virtual machine

When creating a virtual machine, several options are available such as operating system image, disk sizing, and administrative credentials. In this example, a virtual machine is created with a name of `myVM` running the latest version of Windows Server 2016 Datacenter.

Set the username and password needed for the administrator account on the virtual machine with [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Create the initial configuration for the virtual machine with [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig):

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Add the operating system information to the virtual machine configuration with [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem):

```powershell
$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Add the image information to the virtual machine configuration with [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage):

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
```

Add the operating system disk settings to the virtual machine configuration with [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk):

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
```

Add the network interface card that you previously created to the virtual machine configuration with [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface):

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Create the virtual machine with [New-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvm).

```powershell
New-AzureRmVM -ResourceGroupName myRGManageVM  -Location westeurope -VM $vm
```

## Connect to VM

After the deployment has completed, create a remote desktop connection with the virtual machine.

Run the following commands to return the public IP address of the virtual machine. Take note of this IP Address so you can connect to it with your browser to test web connectivity in a future step.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myRGManageVM  | Select IpAddress
```

Use the following command to create a remote desktop session with the virtual machine. Replace the IP address with the `publicIPAddress` of your virtual machine. When prompted, enter the credentials used when creating the virtual machine.

```powershell
mstsc /v:<publicIpAddress>
```

## Understand VM images

The Azure marketplace includes many virtual machine images that can be used to create a new virtual machine. In the previous steps, a virtual machine was created using the Windows Server 2016-Datacenter image. In this step, the PowerShell module is used to search the marketplace for other Windows images, which can also as a base for new VMs. This process consists of finding the publisher, offer, and the image name (Sku). 

Use the [Get-AzureRmVMImagePublisher]( https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmimagepublisher?view=azurermps-3.8.0) command to return a list of image publishers.  

```powersehll
Get-AzureRmVMImagePublisher -Location "westus"
```

Use the [Get-AzureRmVMImageOffer](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmimageoffer?view=azurermps-3.8.0) to return a list of image offers. With this command, the returned list is filtered on the specified publisher. 

```powershell
Get-AzureRmVMImageOffer -Location "westus" -PublisherName "MicrosoftWindowsServer"
```

```powershell
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer westus 
WindowsServer     MicrosoftWindowsServer westus   
WindowsServer-HUB MicrosoftWindowsServer westus   
```

The [Get-AzureRmVMImageSku](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmimagesku?view=azurermps-3.8.0) command will then filter on the publisher and offer name to return a list of image names.

```powershell
Get-AzureRmVMImageSku -Location "westus" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```powershell
Skus                            Offer         PublisherName          Location
----                            -----         -------------          --------
2008-R2-SP1                     WindowsServer MicrosoftWindowsServer westus  
2008-R2-SP1-BYOL                WindowsServer MicrosoftWindowsServer westus  
2012-Datacenter                 WindowsServer MicrosoftWindowsServer westus  
2012-Datacenter-BYOL            WindowsServer MicrosoftWindowsServer westus  
2012-R2-Datacenter              WindowsServer MicrosoftWindowsServer westus  
2012-R2-Datacenter-BYOL         WindowsServer MicrosoftWindowsServer westus  
2016-Datacenter                 WindowsServer MicrosoftWindowsServer westus  
2016-Datacenter-Server-Core     WindowsServer MicrosoftWindowsServer westus  
2016-Datacenter-with-Containers WindowsServer MicrosoftWindowsServer westus  
2016-Nano-Server                WindowsServer MicrosoftWindowsServer westus
```

This information can be used to deploy a VM with a specific image. This example sets the image name on the VM object. Refer to the pervious examples in this tutorial for complete deployment steps.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter-with-Containers -Version latest
```

## Understand VM sizes

A virtual machine size determines the amount of compute resources such as CPU, GPU, and memory that are made available to the virtual machine. Virtual machines need to be created with a size appropriate for the expect work load. If workload increases, an existing virtual machine can be resized.

### VM Sizes

The following table categorizes sizes into use cases.  

| Type                     | Sizes           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| General purpose         |DSv2, Dv2, DS, D, Av2, A0-7| Balanced CPU-to-memory. Ideal for dev / test and small to medium applications and data solutions.  |
| Compute optimized      | Fs, F             | High CPU-to-memory. Good for medium traffic applications, network appliances, and batch processes.        |
| Memory optimized       | GS, G, DSv2, DS, Dv2, D   | High memory-to-core. Great for relational databases, medium to large caches, and in-memory analytics.                 |
| Storage optimized       | Ls                | High disk throughput and IO. Ideal for Big Data, SQL, and NoSQL databases.                                                         |
| GPU           | NV, NC            | Specialized VMs targeted for heavy graphic rendering and video editing.       |
| High performance | H, A8-11          | Our most powerful CPU VMs with optional high-throughput network interfaces (RDMA). 


### Find available VM sizes

To see a list of VM sizes available in a particular region, use the [Get-AzureRmVMSize](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmsize?view=azurermps-3.8.0) command.

```powershell
Get-AzureRmVMSize -Location westus
```

## Resize a VM

After a VM has been deployed, it can be resized to increase or decrease resource allocation.

Before resizing a VM, check if the desired size is available on the current VM cluster. The [Get-AzureRmVMSize](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmsize?view=azurermps-3.8.0) command will return the list of sizes. 

```powershell
Get-AzureRmVMSize -ResourceGroupName myRGManageVM -VMName myVM 
```

If the desired size is available, the VM can be resized from a powered-on state, however it will be rebooted during the operation.

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myRGManageVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myRGManageVM 
```

If the desired size is not on the current cluster, the VM will need to be deallocated before the resize operation can occur. Note, when the VM is powered back on, any data on the temp disk will be removed, and the public IP address will change unless a static IP address is being used. 

```powershell
Stop-AzureRmVM -ResourceGroupName myRGManageVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myRGManageVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myRGManageVM 
Start-AzureRmVM -ResourceGroupName myRGManageVM  -Name $vm.name
```

## Management tasks

During the lifecycle of a virtual machine, you may want to run management tasks such as starting, stopping, or deleting a virtual machine. Additionally, you may want to create scripts to automate repetitive or complex tasks. Using Azure PowerShell, many common management tasks can be run from the command line or in scripts.

### Stop virtual machine

Stop and deallocate a virtual machine with [Stop-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/stop-azurermvm):

```powershell
Stop-AzureRmVM -ResourceGroupName myRGManageVM -Name "myVM" -Force
```

If you want to keep the virtual machine in a provisioned state, use the -StayProvisioned parameter.

### Start virtual machine

```powershell
Start-AzureRmVM -ResourceGroupName myRGManageVM  -Name myVM
```

### Delete resource group

Deleting a resource group also deletes all resources contained within.

```powershell
Remove-AzureRmResourceGroup -Name myRGManageVM  -Force
```

## Next steps

Tutorial - [Create and Manage VM disks](./tutorial-manage-data-disk.md)

Further reading - [VM sizes](./sizes.md)