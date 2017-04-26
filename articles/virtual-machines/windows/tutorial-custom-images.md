---
title: Create custom VM images with the Azure PowerShell | Microsoft Docs
description: Tutorial - Create a custom VM image using the Azure PowerShell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: cynthn
---

# Create a custom image of an Azure VM using PowerShell

In this tutorial, you learn how to create your own custom image of an Azure virtual machine. Custom images are like marketplace images, but you create them yourself. Custom images can be used to boot strap configurations such as preloading applications, application configurations, and other OS configurations. When creating a custom image, the VM plus all attached disks are included in the image. 

The steps in this tutorial can be completed using the latest [Azure CLI 2.0](/cli/azure/install-azure-cli).

To complete the example in this tutorial, you must have an existing virtual machine. If needed, this [script sample](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) can create one for you. When working through the tutorial, replace the resource group and VM names where needed.

## Create an image

To create an image of a virtual machine, you need to prepare the VM by generalizing the VM, deallocating, and then marking the source VM as generalized in Azure.

### Generalize the Windows VM using Sysprep

Sysprep removes all your personal account information, among other things, and prepares the machine to be used as an image. For details about Sysprep, see [How to Use Sysprep: An Introduction](http://technet.microsoft.com/library/bb457073.aspx).


1. Connect to the virtual machine.
2. Open the Command Prompt window as an administrator. Change the directory to **%windir%\system32\sysprep**, and then run `sysprep.exe`.
3. In the **System Preparation Tool** dialog box, select **Enter System Out-of-Box Experience (OOBE)**, and make sure that the **Generalize** check box is selected.
4. In **Shutdown Options**, select **Shutdown** and then click **OK**.
5. When Sysprep completes, it shuts down the virtual machine. **Do not restart the VM**.

### Deallocate and mark the VM as generalized

To create an image, the VM needs to be deallocated and marked as generalized in Azure.

Deallocated the VM using [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Force
```

Set the status of the virtual machine to **Generalized** using [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Generalized
```


### Create the image

Now you can create an image of the VM by using [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermiamgeconfig) and [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). The following example creates an image named `myImage` from a VM named `myVM`.

Get the virtual machine. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroupImages
```

Create the image configuration.

```powershell
$image = New-AzureRmImageConfig -Location westus -SourceVirtualMachineId $vm.ID 
```

Create the image.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroupImages
```	

 
## Create a VM from a custom image

Creating a VM from a custom image is very similar to creating a VM using a Marketplace image. When you use a Marketplace image, you have to information about the image, image provider, offer, SKU and version. With a custom image, you just need to provide the ID of the custom image resource. 

In the following script, we create a variable `$image` to store information about the our custom image using [Get-AzureRmImage] (/powershell/module/azurerm.compute/get-azurermimage) and then we use [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) to supply ID using the `$image` variable we just created. The script creates a new VM named `myVMfromImage` from our custom image in a new resource group named `myResourceGroupFromImage` in the `West US` location.


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location westus

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupFromImage -Location westus `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupFromImage -Location westus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroupFromImage -Location westus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroupFromImage -Location westus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig -VMName myVMfromImage -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows -ComputerName myComputer -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage -ImageName myImage -ResourceGroupName myResourceGroupImages

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM -ResourceGroupName myResourceGroupFromImage -Location westus -VM $vmConfig
```

## Verify that the VM was created
When complete, you can see the newly created VM by using the following PowerShell commands:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName myResourceGroupFromImage 
$vmList.Name
```
<1--
## Next steps

In this tutorial, you have learned about creating custom VM images. Advance to the next tutorial to learn about how highly available virtual machines.

[Create highly available VMs](tutorial-availability-sets.md).

-->

