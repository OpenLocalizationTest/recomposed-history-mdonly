<properties
	pageTitle="FAQ for Linux VMs | Microsoft Azure"
	description="Provides answers to some of the common questions about Linux virtual machines created with the Resource Manager model."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Frequently asked question about Linux Virtual Machines 

This article addresses some common questions about Linux virtual machines created in Azure using the Resource Manager deployment model. For the Windows version of this topic, see [Frequently asked question about Windows Virtual Machines](virtual-machines-windows-faq.md)

## What can I run on an Azure VM?

All subscribers can run server software on an Azure virtual machine. For more information, see [Linux on Azure-Endorsed Distributions](virtual-machines-linux-endorsed-distros.md)


## How much storage can I use with a virtual machine?

Each data disk can be up to 1 TB. The number of data disks you can use depends on the size of the virtual machine. For details, see [Sizes for Virtual Machines](virtual-machines-linux-sizes.md).

An Azure storage account provides storage for the operating system disk and any data disks. Each disk is a .vhd file stored as a page blob. For pricing details, see [Storage Pricing Details](https://azure.microsoft.com/pricing/details/storage/).


## How can I access my virtual machine?

Establish a remote connection to log on to the virtual machine, using Secure Shell (SSH). See the instructions on how to connect [from Windows](virtual-machines-linux-ssh-from-windows.md) or 
[from Linux and Mac](virtual-machines-linux-mac-create-ssh-keys.md). By default, SSH allows a maximum of 10 concurrent connections. You can increase this number by editing the configuration file.


If you’re having problems, check out [Troubleshoot Secure Shell (SSH) connections](virtual-machines-linux-troubleshoot-ssh-connection.md).


## Can I use the temporary disk (/dev/sdb1) to store data?

Don't use the temporary disk (/dev/sdb1) to store data. It is only there for temporary storage. You risk losing data that can’t be recovered. 


## Can I copy or clone an existing Azure VM?

Yes. For instructions, see [How to create a copy of a Linux virtual machine in the Resource Manager deployment model](virtual-machines-linux-copy-vm.md).


## Why am I not seeing Canada Central and Canada East regions through Azure Resource Manager?

The two new regions of Canada Central and Canada East are not automatically registered for virtual machine creation for existing Azure subscriptions. This registration is done automatically when a virtual machine is deployed through the Azure portal to any other region using Azure Resource Manager. After a virtual machine is deployed to any other Azure region, the new regions should be available for subsequent virtual machines.


## Can I add a NIC to my VM after it's created?

No. Adding a NIC can only be done at creation time.


## Are there any computer name requirements?

Yes. The computer name can be a maximum of 64 characters in length. See [Infrastructure naming guidelines](virtual-machines-linux-infrastructure-naming-guidelines.md) for more information around naming your resources.


## What are the username requirements when creating a VM?

Usernames must be 1 - 64 characters in length. 

The following usernames are not allowed:

- "administrator", "admin", "user", "user1", "test", "user2", "test1", "user3", "admin1", "1", "123", "a", "actuser", "adm", "admin2", "aspnet", "backup", "console", "david", "guest", "john", "owner", "root", "server", "sql", "support", "support_388945a0", "sys", "test2", "test3", "user4", "user5"


## What are the password requirements when creating a VM?

Passwords must be 6 - 72 characters in length and meet 3 out of 4 of the following complexity requirements:

- Have lower characters
- Have upper characters
- Have a digit
- Have a special character (Regex match [\W_])

The following passwords are not allowed:

- "abc@123", "P@$$w0rd", "P@ssw0rd", "P@ssword123", "Pa$$word", "pass@word1", "Password!", "Password1", "Password22", "iloveyou!"
