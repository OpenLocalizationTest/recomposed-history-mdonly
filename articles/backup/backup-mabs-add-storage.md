---
title: Use Modern Backup Storage with Azure Backup Server version 2 | Microsoft Docs
description: Learn about the new features in Azure Backup Server version 2. This article describes how to upgrade your Backup Server installation.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''

ms.assetid:
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
---

# Add storage to Azure Backup Server version 2

Azure Backup Server version 2 comes with System Center 2016 Data Protection Manager Modern Backup Storage. Modern Backup Storage delivers 50 percent storage savings, three times faster backups, and more efficient storage. It also offers workload-aware storage. To use Modern Backup Storage, you must use Backup Server version 2 on Windows Server 2016.

If you run Backup Server version 2 on an earlier version of Windows Server, Azure Backup Server can't take advantage of Modern Backup Storage. Instead, it protects workloads as in Backup Server version 1. For more information about preparing storage to back up the legacy way, see.

Backup Server version 2 accepts storage volumes. When you add a volume, Backup Server formats the volume to ReFS, which Modern Backup Storage requires. To add a volume, and to expand it later if you need to, we suggest that you use this workflow:

1.	Set up Backup Server version 2 on a VM.
2.	Create a volume on a virtual disk in a storage pool:
    1.  Add a disk to a storage pool and create a virtual disk with simple layout.
    2.  Add any additional disks, and extend the virtual disk.
    3.  Create volumes on the virtual disk.
3.	Add the volumes to Backup Server.
4.	Configure workload-aware storage.

## Create a volume for Backup Server version 2 Modern Backup Storage

Using Azure Backup Server version 2 with volumes as disk storage helps maintain control over the storage. A volume can be a single disk. However, if you want to extend storage in the future, create a volume out of a disk created by using storage spaces. This helps if you want to expand the volume for backup storage. This section provides best practices for creating a volume with this setup.

The first step is to create a virtual disk. 

1. In Server Manager, in the **File and Storage Services** section, select **Storage Pool**. Add the available disks to it. 

![Create Storage Pools](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. In that storage pool, create a virtual disk with **Simple Layout**.

![Add Virtual Disks](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Add physical disks.

![Add Physicial Disks](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Extend the virtual disk to reflect the added disks.

![Extend the Virtual Disk](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. After creating the virtual disk with sufficient storage, create volumes on the virtual disk.

![Create New Volume](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. Select the server and disk.

![Select the Server and Disk](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## Add volumes to Backup Server disk storage

To add a volume to Backup Server, in the **Management** pane, rescan the storage, and then select **Add**. A list of all the volumes available to be added for Backup Server Storage appears. After available volumes are added to the list of selected volumes, you can give them a friendly name to help you manage them. To format these volumes to ReFS so Backup Server can use the benefits of Modern Backup Storage, select **OK**.

![Add Available Volumes](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## Configure workload-aware storage

With Workload Aware Storage, the volumes can be selected to preferentially store certain kinds of workloads. For example, you can set expensive volumes that support a high number of input/output operations per second (IOPS) to store only the workloads that require frequent, high-volume backups, such as SQL Server with transaction logs. Other workloads that are backed up less frequently, like VMs, can be backed up to other, low-cost volumes.

You can do this by using the PowerShell cmdlet Update-DPMDiskStorage, which updates the properties of a volume in the storage pool on a Data Protection Manager server.

### Update-DPMDiskStorage

Syntax:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

![The Update-DPMDiskStorage command in the PowerShell window](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

The changes you make by using PowerShell are reflected in the UI.

![Disks and volumes in the Administrator Console](./media/backup-mabs-add-storage/mabs-add-storage-9.png)
