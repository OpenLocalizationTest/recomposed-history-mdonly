---
title: Azure Quick Start - Create VM PowerShell| Microsoft Docs
description: Quickly learn to create a Linux virtual machines with PowerShell
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: nepeters
---

# Create a Linux virtual machine with PowerShell

The Azure PowerShell module is used to create and manage Azure resources from the PowerShell command line or in scripts. This guide details using PowerShell to create and Azure virtual machine running Ubuntu 14.04 LTS.

## Create a virtual machine

Log in to your Azure subscription with the Login-AzureRmAccount command and follow the on-screen directions.

```powershell
Login-AzureRmAccount
```

Create a resource group with New-AzureRmResourceGroup. An Azure resource group is a logical container into which Azure resources are deployed and managed. 

The following example creates a resource group named `myResourceGroup` in the `westeurope` location.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

Create a virtual network, subnet, and a public IP address. These resources are used to provide network connectivity to the virtual machine and connect it to the internet.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location westeurope `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location westeurope `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
```

Create a network security group and a network security group rule. The network security group secures the virtual machine using inbound and outbound rules. In this case an inbound rule is created for port 22, which will allow incoming SSH connections.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 22 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location westeurope `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH
```

Create a network card for the virtual machine and associate it with the network security group. The network card will connection the virtual machine to the virtual network and network security group.

```powershell
# Get subnet object
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Location westeurope -Name myNic `
  -Subnet $subnet -NetworkSecurityGroup $nsg -PublicIpAddress $pip
```

Create a virtual machine configuration. This configuration includes the settings that will be used when deploying the VM such as VM image, VM size, and credentials.

```powershell
# Definer user name and blank password
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName myVM -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName Canonical -Offer UbuntuServer -Skus 14.04.2-LTS -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Create the virtual machine. During the creation process, the virtual machine configuration is used to apply and configure the virtual machine operating system.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vmConfig
```

## Connect to virtual machine

Use the following command to create an SSH session. Replace the IP address with the public IP address of your virtual machine.

```bash 
ssh <Public IP Address>
```

You have now created a virtual machine and created an SSH connection with it. To continue learning about using Azure virtual machines, see the Next steps section of this article.

## Delete virtual machine

When no longer needed, the following command can be used to remove the Resource Group, VM, and all related resources.

```azurecli
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## Next Steps

[Create highly available virtual machines tutorial](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Explore VM deployment CLI samples](./virtual-machines-linux-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
