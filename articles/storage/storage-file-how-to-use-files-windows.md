---
title: Use Azure File Storage with Windows | Microsoft Docs
description: Learn how to mount an Azure File share over SMB on Windows.
services: storage
documentationcenter: na
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

# Use Azure Files with Windows
[Azure Files](storage-file-storage.md) is Microsoft's easy to use cloud file system. Azure File shares can be mounted in Windows and Windows Server. This article shows three different ways to mount an Azure File share on Windows: with the File Explorer UI, via PowerShell, and via the Command Prompt. 

In order to mount an Azure File share outside of the Azure Region it is hosted in, such as on-premises or in a different Azure Region, the OS must support SMB 3.x. The following table shows the SMB version of recent Windows releases:

| Windows version | SMB version | Supports mounting from Azure VM | Supports mounting from on-premises | Minimum Recommended KB |
|----|----|----|----|----|
| Windows 10 (versions 1507, 1511, 1607, 1703) | SMB 3.1.1 | Yes | Yes | |
| Windows Server 2016 | SMB 3.1.1 | Yes | Yes | |
| Windows 8.1 | SMB 3.0.2 | Yes | Yes | |
| Windows Server 2012 R2 | SMB 3.0.2 | Yes | Yes | |
| Windows 8 | SMB 3.0 | Yes | Yes | |
| Windows Server 2012 | SMB 3.0 | Yes | Yes | |
| Windows 7 | SMB 2.1 | Yes | No | |
| Windows Server 2008 R2 | SMB 2.1 | Yes | No | |

## <a id="prereq"/></a>Prerequisites for Mounting Azure File Share with Windows 
* **Storage Account Name**: To mount an Azure File share, you will need the name of the storage account.

* **Storage Account Key**: To mount an Azure File share, you will need the primary (or secondary) storage key. SAS keys are not currently supported for mounting.

* **Ensure port 445 is open**: SMB communicates over TCP port 445 - check to see if your firewall is not blocking TCP ports 445 from client machine.

## <a id="explorer"></a>Mount the Azure File share with File Explorer
> [!Note]  
> Note that the following instructions are shown on Windows 10 and may differ slightly on older releases. 

1. **Open File Explorer**: This can be done by opening from the Start Menu, or by pressing Win+E shortcut.

2. **Navigate to the "This PC" item on the left-hand side of the window. This will change the menus available in the ribbon. Under the Computer menu, select "Map Network Drive"**.
    
    ![A screenshot of the "Map network drive" drop down menu](media/storage-file/1_MountOnWindows10.png)

3. **Copy the UNC path from the "Connect" pane in the Azure Portal**: A detailed description of how to find this information can be found [here](storage-file-how-to-use-files-portal.md#connect).

    ![The UNC path from the Azure Files Connect pane](media/storage-file/portal_netuse_connect.png)

4. **Select the Drive letter and enter the UNC path.** 
    
    ![A screenshot of the "Map Network Drive" dialog](media/storage-file/2_MountOnWindows10.png)

5. **Use the Storage Account Name prepended with `Azure\` as the username and a Storage Account Key as the password.**
    
    ![A screenshot of the network credential dialog](media/storage-file/3_MountOnWindows10.png)

6. **Use Azure File share as desired**.
    
    ![Azure File share is now mounted](media/storage-file/4_MountOnWindows10.png)

7. **When you are ready to dismount (or disconnect) the Azure File share, you can do so by right clicking on the entry for the share under the "Network locations" in File Explorer and selecting "Disconnect"**.

## <a id="powershell"></a>Mount the Azure File share with PowerShell
1. **Use the following command to mount the Azure File share**: Remember to replace `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, `<desired-drive-letter>` with the proper information.

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Use the Azure File share as desired**.

3. **When you are finished, dismount the Azure File share using the following command**.

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> You may use the `-Persist` parameter on `New-PSDrive` to make the Azure File share visible to the rest of the OS while mounted.

## <a id="netuse"></a>Mount the Azure File share with Command Prompt
1. **Use the following command to mount the Azure File share**: Remember to replace `<storage-account-name>`, `<share-name>`, `<storage-account-key>`, `<desired-drive-letter>` with the proper information.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Use the Azure File share as desired**.

3. **When you are finished, dismount the Azure File share using the following command**.

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> You can configure the Azure File share to automatically reconnect on reboot by persisting the credentials in Windows. The following command will persist the credentials:
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## See Also
See these links for more information about Azure File storage.

* [FAQ](storage-files-faq.md)
* [Troubleshooting](storage-troubleshoot-file-connection-problems.md)

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