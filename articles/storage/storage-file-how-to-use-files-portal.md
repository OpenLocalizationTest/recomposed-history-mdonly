---
title: Introduction to Azure File Storage | Microsoft Docs
description: An overview of Azure File Storage, Microsoft's cloud file system. Learn how to mount Azure File shares over SMB and lift classic on-premises workloads to the cloud without rewriting any code.
services: storage
documentationcenter: ''
author: RenaShahMSFT
manager: aungoo
editor: tysonn

ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/21/2017
ms.author: renash
---

# How to use Azure Files from the Azure Portal
The [Azure Portal](https://portal.azure.com) provides a user interface for managing Azure File Storage. You can perform the following actions from your web browser:

* Create a File Share
* Upload and download files to and from your file share.
* Monitor the actual usage of each file share.
* Adjust the file share size quota.
* Copy the `net use` command to use to mount your file share from a Windows client.

## Create file share
1. Sign in to the Azure portal.
2. On the navigation menu, click **Storage accounts** or **Storage accounts (classic)**.
    
    ![Screenshot that shows how to create file share in the portal](media/storage-file/use-files-portal-create-file-share1.png)

3. Choose your storage account.

    ![Screenshot that shows how to create file share in the portal](media/storage-file/use-files-portal-create-file-share2.png)

4. Choose "Files" service.

    ![Screenshot that shows how to create file share in the portal](media/storage-file/use-files-portal-create-file-share3.png)

5. Click "File shares" and follow the link to create your first file share.

    ![Screenshot that shows how to create file share in the portal](media/storage-file/use-files-portal-create-file-share4.png)

6. Fill in the file share name and the size of the file share (up to 5120 GB) to create your first file share. Once the file share has been created, you can mount it from any file system that supports SMB 2.1 or SMB 3.0. You could click on **Quota** on the file share to change the size of the file up to 5120 GB. Please refer to [Azure Pricing Calculator](https://azure.microsoft.com/en-us/pricing/calculator/) to estimate the storage costing of using Azure Files.

    ![Screenshot that shows how to create file share in the portal](media/storage-file/use-files-portal-create-file-share5.png)

## Upload and download files
1. Choose one file share your already created.

    ![Screenshot that shows how to upload and download files from the portal](media/storage-file/use-files-portal-upload-file1.png)

2. Click **Upload** to open the user interface for files uploading.

    ![Screenshot that shows how to upload files from the portal](media/storage-file/use-files-portal-upload-file2.png)

<a id="connect"/></a>
## Connect to file share
-  Click **Connect** to get the command line for mounting the file share from Windows and Linux. For Linux users, you could also refer to [How to use Azure File Storage with Linux](storage-how-to-use-files-linux.md) for more mounting instructions for other Linux distros.

    ![Screenshot that shows how to mount the file share](media/storage-file/use-files-portal-connect.png)

    ![Screenshot that shows the mount commands for Windows and Linux](media/storage-file/use-files-portal-show-mount-commands.png)

**Tip:**  
To find the storage account access key for mounting, click on **View access keys for this storage account** at the bottom of the connect page.

## Also See
See these links for more information about Azure File storage.

### Conceptual articles and videos
* [Azure Files Storage: a frictionless cloud SMB file system for Windows and Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [How to use Azure File Storage with Linux](storage-how-to-use-files-linux.md)

### Tooling support for File storage
* [Using Azure PowerShell with Azure Storage](storage-powershell-guide-full.md)
* [How to use AzCopy with Microsoft Azure Storage](storage-use-azcopy.md)
* [Using the Azure CLI with Azure Storage](storage-azure-cli.md#create-and-manage-file-shares)
* [Troubleshooting Azure File storage problems](https://docs.microsoft.com/en-us/azure/storage/storage-troubleshoot-file-connection-problems)

### Blog posts
* [Azure File storage is now generally available](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrating data to Azure File ](https://azure.microsoft.com/en-us/blog/migrating-data-to-microsoft-azure-files/)

### Reference
* [Storage Client Library for .NET reference](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File Service REST API reference](http://msdn.microsoft.com/library/azure/dn167006.aspx)