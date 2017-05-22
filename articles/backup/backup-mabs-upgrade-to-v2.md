---
title: Upgrade Azure Backup Server to v.2 | Microsoft Docs
description: Learn about the new features when you upgrade to Azure Backup Server v.2. This article provides instruction on upgrading your Azure Backup Server installation.
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

# Install Azure Backup Server v.2


You can install Azure Backup Server v.2 on Windows Server 2012 R2, or on Windows Server 2016. However to take advantage of Modern Backup Storage, Azure Backup Server v.2 must be installed on Windows Server 2016. Before you upgrade or install Azure Backup Server v.2, please read the [Installation prerequisites](http://docs.microsoft.com/system-center/dpm/install-dpm.md#setup-prerequisites).

> Are DPM's prerequisites followed?

> [!NOTE]
> Azure Backup Server is based on the same code base as System Center Data Protection Manager (DPM). Azure Backup Server v.1 is equivalent to DPM 2012 R2, and Azure Backup Server is equivalent to DPM 2016. This documentation occasionally references the DPM documentation.
>
>

## Upgrade Azure Backup Server to v.2
To upgrade Azure Backup Server v.1 to v.2, make sure your installation has the necessary updates:

- Update the agents on the protected servers.
> How do you update the agent before you start the upgrade process? Isn't updating the agent part of the upgrade process?

- Upgrade Windows Server 2012 R2 to Windows Server 2016.
- Upgrade Azure Backup Server Remote Administrator on all production servers.
- Backups continue without rebooting your production server.


### Upgrade steps for Azure Backup Server v.2

1. Where do they download the bits from?

2. After you have extracted the setup wizard, make sure the Execute setup.exe is selected. Click **Finish** to open the Azure Backup Server v.2 wizard.

  ![setup installer](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. On the Microsoft Azure Backup Server wizard, under **Install**, click **Microsoft Azure Backup Server**.

  ![setup installer](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. At the Welcome screen, read the warnings and click **Next**.

  ![setup installer](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. The setup wizard performs prerequisite checks to make sure your environment is able to upgrade. Click **Check** to perform the checks.

  ![setup installer](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. Your environment must be able to pass the prerequisite check. If your environment cannot pass the check, note the issues and fix them. You can then click **Check Again**. Once you can pass the prerequisite checks, click **Next** to move forward in the wizard.

  ![setup installer](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. On the SQL Setting screen, choose the appropriate option for your SQL installation and click **Check and Install**.

  ![setup installer](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  The checks may take a few minutes to complete. Once the checks have completed, click **Next**.

  ![setup installer](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. On the Installation Settings screen, make any changes to the location where Azure Backup Server is installed, or the scratch location. Once these are set, click **Next**.

  ![setup installer](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9.

  ![setup installer](./media/backup-mabs-upgrade-to-v2/run-setup.png)





## Adding Storage for Modern Backup Storage

To improve backup storage efficiency, Azure Backup Server v2 adds support for Volumes. Azure Backup Server v2 continues to support disks, just like in v1.

### Add Volumes and Disks
If you run Azure Backup Server v2 on Windows Server 2016, you can use volumes to store backup data. Volumes provide storage savings and faster backups. Because volumes are new to Azure Backup Server, you must add them. When you add the volume to Azure Backup Server, give the volume a friendly name by click the **Friendly Name** column of the desired volume. You can change the name later, if necessary. You can also use PowerShell to add or change friendly names for volumes.

To add a volume in the administrator console:

1. In the DPM Administrator console, select the **Management** feature > **Disk Storage** > **Add**.

2. In the **Add Disk Storage** dialog, select an available volume > click **Add** > type a friendly name for the volume ** > click **OK**.

      ![Add volume](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-volume.png)

If you want to add a disk, it must belong to a protection group with legacy storage. Those disks can only be used for those protection groups. If the DPM server doesn't have sources with legacy protection, the disk won't appear.
See the topic, [Adding disks to increase legacy storage](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016.md#adding-disks-to-increase-legacy-storage), for more information on adding disks. You can't give disks a friendly name.


### Assign Workloads to Volumes

DPM 2016 allows the user to specify which kinds of workloads should be assigned to which volumes. For example, expensive volumes that support high IOPS can be configured to store only the workloads that require frequent, high-volume backups like SQL with Transaction Logs.
To update the properties of a volume in the storage pool on a DPM server, use the PowerShell cmdlet, Update-DPMDiskStorage.

**Update-DPMDiskStorage**

**Syntax**

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

The changes made through PowerShell are reflected in the UI.


## Protecting Data Sources
To begin protecting data sources, create a Protection Group. The following procedure highlights changes or additions to the **New Protection Group** wizard.

To create a Protection Group:

1. In the DPM Administrator Console, select the **Protection** feature.

2. On the tool ribbon, click **New**.

    The **Create new Protection Group** wizard opens.

  ![Create protection group](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-protection-wiz.png)

3. Click **Next** to advance the wizard to the **Select Protection Group Type** screen.
4. On the **Select Protection Group Type** screen, select the type of Protection Group to be created and then click **Next**.

  ![Choose server or client](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-protection-group-screen2.png)

5. On the **Select Group Members** screen, in the **Available members** pane, DPM lists the members with protection agents. For the purposes of this example, select volume D:\ and E:\ to add them to the **Selected members** pane. Once you have chosen the members for the protection group, click **Next**.

  ![Select group members for protection group](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-protection-screen3.png)

6. On the **Select Data Protection Method** screen, type a name for the **Protection group**, select the protection method(s) and click **Next**.
    If you want short term protection, you must use Disk backup.

  ![Select data protection method](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-protection-screen4.png)

7. On the **Specify Short-Term Goals** screen specify the details for **Retention Range** and **Synchronization Frequency**, and click **Next**. If desired, click **Modify** to change the schedule when recovery points are taken.

  ![Select data protection method](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-protection-screen5.png)

8. The **Review Disk Storage Allocation** screen provides details about the selected data sources, their size, the **Space to be Provisioned**, and **Target Storage Volume**.

  ![Review Disk Storage Allocation](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-protection-screen6.png)

  The storage volumes are determined based on the workload volume allocation (set using PowerShell) and the available storage. You can change the storage volumes by selecting other volumes from the drop-down menu. If you change the **Target Storage**, the **Available disk storage** dynamically changes to reflect the **Free Space** and **Underprovisioned Space**.

  The **Underprovisioned Space** column in **Available disk storage**, reflects the amount of additional storage needed if the data sources grow as planned. Use this value to help plan your storage needs to enable smooth backups. If the value is zero, then there are no potential problems with storage in the foreseeable future. If the value is a number other than zero, then you do not have sufficient storage allocated  - based on your protection policy and the data size of your protected members.

  ![Underallocated disk storage](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-underprovision-storage.png)

The remainder of the New Protection Group wizard is unchanged from DPM 2012 R2. Continue through the wizard to complete creation of your new protection group.

## Migrating legacy storage to Modern Backup Storage
After upgrading DPM 2012 R2 to DPM 2016 and the operating system to Windows Server 2016, you can update your existing protection groups to the new DPM 2016 features. By default, protection groups are not changed, and continue to function as they were configured in DPM 2012 R2. Updating protection groups to use Modern Backup Storage is optional. To update the protection group, stop protection of all data sources with Retain Data, and add the data sources to a new protection group. DPM begins protecting these data sources the new way.

1. In the Administrator Console, select the **Protection** feature, and in the **Protection Group Member** list, right-click the member, and select **Stop protection of member...**.

  ![Stop protection](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

  The **Remove from Group** dialog opens.

2. In the **Remove from Group** dialog, review the used disk space and the available free space in the storage pool. The default is to leave the recovery points on the disk and allow them to expire per their associated retention policy. Click **OK**.

    If you want to immediately return the used disk space to the free storage pool, select **Delete replica on disk**. This will delete the backup data (and recovery points) associated with that member.

    ![Retain data](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Create a new protection group that uses Modern Backup Storage, and include the unprotected data sources.


## Adding Disks to increase legacy storage

If you want to use legacy storage with DPM 2016, it may become necessary to add disks to increase legacy storage. To add disk storage:

1. On the Administrator Console, click **Management**.

2. Select **Disk Storage**.

3. On the tool ribbon click **Add**.

    The **Add Disk Storage** dialog opens.

    ![Add disks](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. In the **Add Disk Storage** dialog, click **Add disks**.

    DPM provides a list of available disks.

5. Select the disks, click **Add** to add the disks, and click **OK**.

## New PowerShell cmdlets

For DPM 2016, two new cmdlets: [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx) and [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx) are available. Click the cmdlet name to see its reference documentation.
