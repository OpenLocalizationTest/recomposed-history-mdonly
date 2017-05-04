---
title: Backup Azure Windows VMs | Microsoft Docs'
description: Protect your Windows VMs by backing them up using Azure Backup.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: cynthn
---
# Back up Windows virtual machines in Azure

## Backup overview

Protect your data by taking snapshots of your data at defined intervals. These snapshots are known as recovery points, and they are stored in recovery services vaults. If or when it is necessary to repair or rebuild a VM, you can restore the VM from any of the saved recovery points. When you restore a recovery point, you can create a new VM which is a point-in-time representation of your backed-up VM, or restore disks and use the template that comes along with it to customize the restored VM or do an individual file recovery. This article explains how to restore a VM to a new VM or restore all backed-up disks. For individual file recovery, refer to [Recover files from Azure VM backup](backup-azure-restore-files-from-vm.md)

### How does Azure back up virtual machines?
When the Azure Backup service initiates a backup job at the scheduled time, it triggers the backup extension to take a point-in-time snapshot. The Azure Backup service uses the _VMSnapshot_ extension. The extension is installed during the first VM backup if the VM is running. If the VM is not running, the Backup service takes a snapshot of the underlying storage (since no application writes occur while the VM is stopped).

When taking a snapshot of Windows VMs, the Backup service coordinates with the Volume Shadow Copy Service (VSS) to get a consistent snapshot of the virtual machine's disks. Once the Azure Backup service takes the snapshot, the data is transferred to the vault. To maximize efficiency, the service identifies and transfers only the blocks of data that have changed since the previous backup.

![Azure virtual machine backup architecture](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

When the data transfer is complete, the snapshot is removed and a recovery point is created.

This table explains the types of consistency and the conditions that they occur under during Azure VM backup and restore procedures.

| Consistency | VSS-based | Explanation and details |
| --- | --- | --- |
| Application consistency |Yes for Windows|Application consistency is ideal for workloads as it ensures that:<ol><li> The VM *boots up*. <li>There is *no corruption*. <li>There is *no data loss*.<li> The data is consistent to the application that uses the data, by involving the application at the time of backup--using VSS or pre/post script.</ol> <li>*Windows VMs*- Most Microsoft workloads have VSS writers that do workload-specific actions related to data consistency. For example, Microsoft SQL Server has a VSS writer that ensures that the writes to the transaction log file and the database are done correctly. For Azure Windows VM backups, to create an application-consistent recovery point, the backup extension must invoke the VSS workflow and complete it before taking the VM snapshot. For the Azure VM snapshot to be accurate, the VSS writers of all Azure VM applications must complete as well. (Learn the [basics of VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) and dive deep into the details of [how it works](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Linux VMs*- Customers can execute [custom pre-script and post-script to ensure application consistency](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| File-system consistency |Yes - for Windows-based computers |There are two scenarios where the recovery point can be *file-system consistent*:<ul><li>Backups of Linux VMs in Azure, without pre-script/post-script or if pre-script/post-script failed. <li>VSS failure during backup for Windows VMs in Azure.</li></ul> In both these cases, the best that can be done is to ensure that: <ol><li> The VM *boots up*. <li>There is *no corruption*.<li>There is *no data loss*.</ol> Applications need to implement their own "fix-up" mechanism on the restored data. |
| Crash consistency |No |This situation is equivalent to a virtual machine experiencing a "crash" (through either a soft or hard reset). Crash consistency typically happens when the Azure virtual machine is shut down at the time of backup. A crash-consistent recovery point provides no guarantees around the consistency of the data on the storage medium--either from the perspective of the operating system or the application. Only the data that already exists on the disk at the time of backup is captured and backed up. <br/> <br/> While there are no guarantees, usually, the operating system boots, followed by disk-checking procedure, like chkdsk, to fix any corruption errors. Any in-memory data or writes that have not been transferred to the disk are lost. The application typically follows with its own verification mechanism in case data rollback needs to be done. <br><br>As an example, if the transaction log has entries that are not present in the database, then the database software does a rollback until the data is consistent. When data is spread across multiple virtual disks (like spanned volumes), a crash-consistent recovery point provides no guarantees for the correctness of the data. |


###Pricing
The cost of backing up Azure VMs is based on the number of protected instances. For a definition of a protected instance, see [What is a protected instance](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). For an example of calculating the cost of backing up a virtual machine, see [How are protected instances calculated](backup-azure-vms-introduction.md#calculating-the-cost-of-protected-instances). See the Azure Backup Pricing page for information about [Backup Pricing](https://azure.microsoft.com/pricing/details/backup/).


## Create a backup
Create a simple daily backup schedule to a Recovery Services Vault. The Recovery Services Vault by deafult is geo-redundant storage.

1. Sign in to the [Azure portal](https://portal.azure.com/).
2. In the menu on the left, select **Virtual machines**. 
3. From the list, select a VM to back up.
4. On the VM blade, in the **Settings** section, click **Backup**. The **Enable backup** blade opens.
5. For the [Recovery Services vault](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm), click **Create new** and provide the name for the new vault. A new vault is created in the same Resource Group and same location as the virtual machine. Make a note of the name of the vault so that we can use it later.
6. Click **Backup policy**. The default policy is to create a backup daily at 8:30 PM and to retain the backup for 180 days. Keep the defaults for this example and click **OK**.

    ![Select backup policy](./media/tutorial-backup-vms/policy.png)

7. On the Enable backup blade, click **Enable Backup**. 
9. Once the configuration has completed, on the VM management blade, click **Backup** to open the Backup Item blade and view the details.

  ![VM Backup Item View](../../backup/media/backup-azure-vms-first-look-arm/backup-item-view.png)

  Until the initial backup has completed, **Last backup status** shows as **Warning(Initial backup pending)**. To see when the next scheduled backup job occurs, under **Backup policy** click the name of the policy. The Backup Policy blade opens and shows the time of the scheduled backup.

10. To create an initial recovery point, on the Backup vault blade click **Backup now**.

11. On the **Backup Now** blade, click the calendar icon, use the calendar control to select the last day this recovery point is retained, and click **Backup**.

Deployment notifications let you know the backup job has been triggered, and that you can monitor the progress of the job on the Backup jobs page.

## Recover a file

If you accidentally delete or make changes to a file, you can use File Recovery to recover the file from your backup vault. File Recovery uses a script that runs on the VM, to mount the a recovery point as local drive. These drives will remain mounted for 12 hours so that you can copy files from the recovery point and restore them to the VM.  

For this example, we are showing a Windows VM with IIS installed. We will delete the image file that is used in the default web page for IIS. 

1. Open a browser and connect to the IP address of the VM to show the default IIS page.
2. Connect to the VM.
3. On the VM, open **File Explorer** and navigate to \inetpub\wwwroot and delete the file **iisstart.png**.
4. On your local computer, refresh the browser to see that the image is gone.
5. On your local computer, open a new tab and go the the [Azure portal](portal.azure.com).
6. In the menu on the left, select **Virtual machines**. 
7. From the list, select the VM.
8. On the VM blade, in the **Settings** section, click **Backup**. The **Backup** blade opens. 
9. In the menu at the top of the blade, select **File Recovery (Preview)**. The **File Recovery (Preview) blade opens.
10. In **Step 1: Select recovery point**, select a recovery point from the drop-down.
11. In **Step 2: Download script to browse and recover files**, click the **Download Executable** button. Save the downloaded file to your **Downloads** folder.
12. On your local machine, open **File Explorer** and navigate to your **Downloads** folder and copy the downloaded .exe file. The filename will be prefixed by your VM name. 
13. On your VM (over the RDP connection) paste the .exe file to the Desktop of your VM. 
14. Navigate to the desktop of your VM and double-click on the .exe. This will launch a command prompt and then mount the recovery point as a file share that you can access. When it is finished creating the share, type **q** to close the command prompt.
15. Open file explorer and navigate to the drive letter that was used for the file share.
16. Navigate to \inetpub\wwwroot and copy **iisstart.png** from the file share and paste it into \inetpub\wwwroot. For example, copy F:\inetpub\wwwroot\iisstart.png and paste it into c:\inetpub\wwwroot to recover the file.
17. On your local machine, open the browser tab where you are connected to the VM and the IIS default page. Press CTRL + F5 to refersh the browser page and you should now see that the image has been restored.
18. When you are done recovering files, got back to the browser and in **Step 3: Unmount the disks after recovery** click the **Unmount Disks** button.








