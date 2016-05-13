<properties
	pageTitle="FAQ for Windows VMs | Microsoft Azure"
	description="Provides answers to some of the common questions about Windows virtual machines created with the Resource Manager model."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/13/2016"
	ms.author="cynthn"/>

# Frequently asked question about Azure Virtual Machines created with the resource manager deployment model


This article addresses some common questions users ask about Azure virtual machines created with the Resource Manager deployment model.

## What can I run on an Azure VM?

All subscribers can run server software on an Azure virtual machine. For information about the support policy for running Microsoft server software in Azure, see [Microsoft server software support for Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=393550)

For Windows client images, certain versions of Windows 7 and Windows 8.1 are available to MSDN Azure benefit subscribers and MSDN Dev and Test Pay-As-You-Go subscribers, for development and test tasks. For details, including instructions and limitations, see [Windows Client images for MSDN subscribers](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). For mor


## How much storage can I use with a virtual machine?

Each data disk can be up to 1 TB. The number of data disks you can use depends on the size of the virtual machine. For details, see [Sizes for Virtual Machines](virtual-machines-windows-sizes).

An Azure storage account provides storage for the operating system disk and any data disks. Each disk is a .vhd file stored as a page blob. For pricing details, see [Storage Pricing Details](https://azure.microsoft.com/en-us/pricing/details/storage/).


## How can I access my virtual machine?

You need to establish a remote connection using Remote Desktop Connection (RDP) for a Windows VM. For instructions, see [How to connect and log on to an Azure virtual machine running Windows](virtual-machines-windows-connect-logon). A maximum of 2 concurrent connections are supported, unless the server is configured as a Remote Desktop Services session host.  


If you’re having problems with Remote Desktop, see [Troubleshoot Remote Desktop connections to a Windows-based Azure Virtual Machine](virtual-machines-windows-troubleshoot-rdp-connection). 

If you’re familiar with Hyper-V, you might be looking for a tool similar to VMConnect. Azure doesn’t offer a similar tool because console access to a virtual machine isn’t supported.

## Can I use the temporary disk (the D: drive by default) to store data?

You shouldn’t use the temporary disk to store data. They are only temporary storage, so you would risk losing data that can’t be recovered. This can occur when the virtual machine moves to a different host. Resizing a virtual machine, updating the host, or a hardware failure on the host are some of the reasons a virtual machine might move.

## How can I change the drive letter of the temporary disk?

On a Windows virtual machine, you can change the drive letter by moving the page file and reassigning drive letters, but you’ll need to make sure you do the steps in a specific order. For instructions, see [Change the drive letter of the Windows temporary disk](virtual-machines-windows-classic-change-drive-letter.md).

## Can I add an existing VM to an availability group?

No. If you want your VM to be part of an availability set, you need to create the VM within the set. There currently isn't a way to add a VM to an availability set after it has been created.

## Can I upload a virtual machine to Azure?

Yes. For instructions, see [Upload a Windows VM image to Azure ](virtual-machines-windows-upload-image.md)

## Can I resize the OS disk?

Yes. For instructions, see [How to expand the OS drive of a Virtual Machine in an Azure Resource Group](virtual-machines-windows-expand-os-disk.md).

## Why am I not seeing Canada Central and Canada East regions as available locations for virtual machines through Azure Resource Manager?

A: The two new regions of Canada Central and Canada East are not automatically registered for virtual machine creation for existing Azure subscriptions. This registration will be done automatically when a virtual machine is deployed through the Azure portal to any other region using Azure Resource Manager. After a virtual machine is deployed to any other Azure region the new regions should be available for subsequent virtual machines.

## Does Azure support Linux VMs?

Yes. To quickly create a Linux VM to try out, see [Create a Linux VM on Azure using the Portal](virtual-machines-linux-quick-create-portal.md).

