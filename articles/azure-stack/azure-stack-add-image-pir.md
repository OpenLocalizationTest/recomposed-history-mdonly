﻿<properties
	pageTitle="Add an image to the Platform Image Repository (PIR) in Azure Stack | Microsoft Azure"
	description="Learn how to prepare a virtual hard disk image before you add an image to the PIR in Azure Stack."
	services="azure-stack"
	documentationCenter=""
	authors="ErikjeMS"
	manager="v-kiwhit"
	editor=""/>

<tags
	ms.service="azure-stack"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="erikje"/>

# Add an image to the Platform Image Repository (PIR) in Azure Stack

Before a virtual machine can be added to the Marketplace, its image must be added to the Platform Image Repository. This repository  contains all the images for virtual machines offered in the Marketplace and referenced by Azure Resource Manager (ARM) templates.

Before you can add an image to the PIR, you must prepare a virtual hard disk image with either a Windows or Linux operating system. This virtual hard disk must be in the VHD format (not VHDX). 

-   To prepare a Windows VHD, follow Step 1 from the [Create and upload a Windows Server VHD to Azure](../virtual-machines/virtual-machines-create-upload-vhd-windows-server.md) article.

-   To prepare a Linux VHD, follow Step 1 from the [Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System](../virtual-machines/virtual-machines-linux-create-upload-vhd/) article.

## To add an image to the PIR, follow these steps:

1.  Get the drive letter of the DATAIMAGE disk from “This PC”. For this example, we will use X:\\

2.  Open a PowerShell command window.

3.  Run the command `X:\CRP\CM\Microsoft.AzureStack.Compute.Installer\content\Scripts\CopyImageToPlatformImageRepository.ps1`

4.  Follow the prompts to enter the following values:

    1.  **PlatformImageRepositoryPath**: Enter `\\SOFS\Share\CRP\PlatformImages\`

    2.  **ImagePath**: Enter the path to the PIR image.

    3.  **Publisher**: Enter a publisher name, such as *Microsoft*.

    4.  **Offer**: Enter any value, such as *Server*.

    5.  **Sku**: Enter a SKU for the image, such as *Datacenter*.

    6.  **Version**: Enter a version, which MUST have three segments, such as *1.0.0*. A version number of 1.0, or 1.0.0.0 will not work.

    7.  **OsType**: Enter the OS Type. This MUST be either *Windows* or *Linux*.

5.  Wait for the command to complete. You may need to close any browser windows in the ClientVM and reopen them to see the changes in the Marketplace.

The image will automatically appear in the Microsoft Azure Stack Marketplace.  The image is also available for you to reference in your virtual machine deployment templates.  

>[AZURE.NOTE] The marketplace UI may error after you remove a previously added image from the PIR. To fix this, click **Settings** in the portal. Then, click **Discard modifications** under **Portal customization**.

## Next Steps

[Add an image to the Platform Image Repository](azure-stack-add-image-pir.md)
