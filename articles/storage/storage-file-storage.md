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
# Introduction to Azure File Storage
Azure File Storage is Microsoft's easy to use cloud file system. Azure File Storage offers network file shares in the cloud using the industry standard [Server Message Block (SMB) Protocol](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) and [Samba/Common Internet File System (CIFS)](https://technet.microsoft.com/en-us/library/cc939973.aspx). Azure File shares can be mounted concurrently by clients such as on-premises deployments of Windows, macOS, or Linux, or by Azure Virtual Machines. 

Azure File shares can be used to:

* **“Lift and Shift” applications**
    Azure Files makes it easier to “lift and shift” applications to the cloud that use on-premise file shares to share data between parts of the application. To make this happen, each VM connects to the file share and then it can read and write files just like it would against an on-premise file share.
* **Shared Application Settings**
    A common pattern for distributed applications is to have configuration files in a centralized location where they can be accessed from many different virtual machines. Such configuration files can now be stored in an Azure File share, and read by all application instances. These settings can also be managed via the REST interface, which allows worldwide access to the configuration files.
* **Diagnostic Share**
    An Azure File share can also be used to save diagnostic files like logs, metrics, and crash dumps. Having these available through both the SMB and REST interface allows applications to build or leverage a variety of analysis tools for processing and analyzing the diagnostic data.
* **Dev/Test/Debug**
    When developers or administrators are working on virtual machines in the cloud, they often need a set of tools or utilities. Installing and distributing these utilities on each virtual machine where they are needed can be a time consuming exercise. With Azure Files, a developer or administrator can store their favorite tools on a file share, which can be easily connected to from any virtual machine.

## Videos
| Introducing Azure File Storage (27m) | Azure File Storage Tutorial (5 minutes)  |
|-|-|
| [![Screencap of the Introducing Azure File Storage video - click to play!](media/storage-file/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Screencap of the Azure File Storage Tutorial - click to play!](media/storage-file/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-Storage-with-Windows/) |

## Why is Azure File Storage useful?
Azure File Storage allows you to replace Windows Server, Linux, or NAS based file servers hosted on-premises or in the cloud, with an OS-free cloud file share. This has the following benefits:

* **Shared access:**. Azure File shares support the industry standard SMB protocol, meaning you can seamlessly replace your on-premises file shares with Azure File shares without worrying about application compatibility. Being able to share a file system across multiple machines, applications/instances is a significant advantage with Azure File Storage for applications that need shareability. 
* **Fully Managed**. Azure File shares can be created without the need to manage hardware or an OS. This means you don't have to deal with patching the server OS with critical security upgrades or replacing faulty hard disks.
* **Scripting and Tooling**. PowerShell cmdlets and Azure CLI  can be used to create, mount, and manage File storage shares as part of the administration of Azure applications.You can create and manage Azure file shares using Azure Portal and Azure Storage Explorer. 
* **Resiliency**. Azure File Storage has been built from the ground up to be always available. Replacing on-premises file shares with Azure File Storage means you no longer have to wake up to deal with local power outages or network issues. 
* **Familiar Programmability**. Applications running in Azure can access data in the share via file [system I/O APIs](https://msdn.microsoft.com/en-us/library/system.io.file(v=vs.110).aspx). Developers can therefore leverage their existing code and skills to migrate existing applications. In addition to System IO APIs, one can use Azure Storage Client Libraries or the Azure Storage REST API.

## How does it work?
Managing Azure File shares is a lot simpler than managing file shares on-premises. The following diagram illustrates the Azure File Storage management constructs:


![File Structure](../../includes/media/storage-file-concepts-include/files-concepts.png)

* **Storage Account**: All access to Azure Storage is done through a storage account. See Azure Storage Scalability and Performance Targets for details about storage account capacity.
* **Share**: A File storage share is an SMB file share in Azure. All directories and files must be created in a parent share. An account can contain an unlimited number of shares, and a share can store an unlimited number of files, up to the 5 TB total capacity of the file share.
* **Directory**: An optional hierarchy of directories.
* **File**: A file in the share. A file may be up to 1 TB in size.
* **URL format**: Files are addressable using the following URL format:  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```
## Next Steps if you are a first-time user of Azure File Storage
* [Create File Share](storage-file-how-to-create-file-share.md)
* [Connect and Mount File Share](storage-file-how-to-connect-and-mount.md)
* [Manage Azure File Share using tools and scripts](storage-file-how-to-tooling-and-scripting.md)

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