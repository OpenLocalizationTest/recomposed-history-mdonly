<properties
	pageTitle="Different ways to create a Linux VM | Microsoft Azure"
	description="Lists the different ways to create a Linux virtual machine on Azure and gives links to further instructions."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/20/2016"
	ms.author="dkshir"/>

# Different Ways to Create a Linux Virtual Machine

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure offers different ways to create a VM, to suit different users and purposes. This article summarizes these differences and the choices you can make for creating your Linux virtual machines.

Azure Resource Manager templates can also be used to create and manage a virtual machine and its different resources as one logical deployment unit. Instructions for this approach are included where applicable. To learn more about Azure Resource Manager and how to manage resources as one unit, see the [overview][].

## Tool choices

### GUI: The Azure classic portal or Azure portal

The graphical user interface of the portal is an easy way to try out a virtual machine, especially if you're just starting out with Azure. Use either the [Azure classic portal](https://manage.windowsazure.com) or the [Azure portal](https://portal.azure.com) to create the VM. For general instructions, see [Create a Custom Virtual Machine][] and select any Linux image from the **Gallery**. Note that the [Azure classic portal](https://manage.windowsazure.com) creates virtual machines using only the classic deployment model.

### Command Shell: Azure CLI or Azure PowerShell

If you prefer working in a command shell, choose between the Azure command-line interface (CLI) for Mac, Linux and Windows users, or the Azure PowerShell, which has Windows PowerShell cmdlets for Azure and a custom console.

To create virtual machines in Resource Manager deployment model using the Azure CLI, see [Create a Virtual Machine Running Linux][]. Follow the tabs or article selectors on this article to read instructions using Azure PowerShell and Templates.  

For classic deployment model, see [Create a custom Linux virtual machine using Azure CLI](virtual-machines-linux-classic-create-custom.md) and
[Use Azure PowerShell to create and preconfigure Linux-based Virtual Machines][].


### Development Environment: Visual Studio

Visual Studio also supports creating Azure virtual machines. For classic deployment model, read [Creating a virtual machine for a website with Visual Studio][]. For Resource Manager deployment model, read [Deploy Azure Resources Using the Compute, Network, and Storage .NET Libraries][].


## Operating System and Image Choices

Choose an image based on the operating system you want to run. Azure and its partners offer many images, some of which include applications and tools. Or, use one of your own images.


### Azure Images

In all of the above articles, you can easily use an existing Azure image to create a virtual machine and customize it for networking, load balancing, and more. The portals provide a gallery or image list for Azure supplied images. You can get similar lists using the command line. For example, in Azure CLI, run `azure vm image list` to get a list of all available images, by location and publisher.


### Use Your Own Image

Use an image based on an existing Azure virtual machine by *capturing* that VM, or upload an image of your own, stored in a virtual hard disk (VHD). For classic deployment model, see [How to Capture a Linux Virtual Machine to Use as a Template with the CLI][] and [Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System][]. For Resource Manager deployment model, see [How to capture a Linux virtual machine as a Resource Manager template](virtual-machines-linux-capture-image.md).

## Next Steps

[Log On to the Virtual Machine][]

[Attach a Data Disk][]

## Additional resources

[Base Configuration Test Environment][]

[Azure hybrid cloud test environments][]

[Equivalent Resource Manager and Service Management Commands for VM Operations with the Azure CLI for Mac, Linux, and Windows][]

<!-- LINKS -->
[overview]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-hero-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-cli-create.md


[Equivalent Resource Manager and Service Management Commands for VM Operations with the Azure CLI for Mac, Linux, and Windows]:virtual-machines-linux-cli-manage.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-linux-cli-deploy-templates.md

[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]:  virtual-machines-windows-ps-manage.md
[Use Azure PowerShell to create and preconfigure Linux-based Virtual Machines]: virtual-machines-linux-classic-createpowershell.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-classic-create-custom.md
[How to Capture a Linux Virtual Machine to Use as a Template with the CLI]: virtual-machines-linux-classic-capture-image.md

[Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System]: virtual-machines-linux-classic-create-upload-vhd.md

[Creating a virtual machine for a website with Visual Studio]: virtual-machines-windows-classic-web-app-visual-studio.md
[Deploy Azure Resources Using the Compute, Network, and Storage .NET Libraries]: virtual-machines-windows-csharp.md

[Log On to the Virtual Machine]: virtual-machines-linux-classic-log-on.md

[Attach a Data Disk]: virtual-machines-linux-classic-attach-disk.md

[Base Configuration Test Environment]: virtual-machines-windows-classic-test-config-env.md
[Azure hybrid cloud test environments]: virtual-machines-windows-classic-hybrid-test-env.md

[Create a Virtual Machine Running Linux]: virtual-machines-linux-cli-create.md
[Create a Custom Virtual Machine]: virtual-machines-windows-classic-createportal.md
