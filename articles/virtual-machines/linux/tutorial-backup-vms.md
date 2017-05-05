---
title: Backup Azure Linux VMs | Microsoft Docs'
description: Protect your Linux VMs by backing them up using Azure Backup.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: cynthn
---
# Back up Linux  virtual machines in Azure

You can protect your data by taking backups at regular intervals. Azure Backup creates recovery points that are stored in geo-redundant recovery vaults. When you restore from a recovery pointy you can restore the whole VM or just specific files. This article explains how to restore a single file to a VM.

## Backup overview

When the Azure Backup service initiates a backup, it triggers the backup extension to take a point-in-time snapshot. The Azure Backup service uses the _VMSnapshotLinux_ extension in Linux. The extension is installed during the first VM backup if the VM is running. If the VM is not running, the Backup service takes a snapshot of the underlying storage (since no application writes occur while the VM is stopped).

Once the Azure Backup service takes the snapshot, the data is transferred to the vault. To maximize efficiency, the service identifies and transfers only the blocks of data that have changed since the previous backup.

![Azure virtual machine backup architecture](../../backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)

When the data transfer is complete, the snapshot is removed and a recovery point is created.


## Create a backup
Create a simple scheduled daily backup to a Recovery Services Vault. 

1. Sign in to the [Azure portal](https://portal.azure.com/).
2. In the menu on the left, select **Virtual machines**. 
3. From the list, select a VM to back up.
4. On the VM blade, in the **Settings** section, click **Backup**. The **Enable backup** blade opens.
5. In **Recovery Services vault**, click **Create new** and provide the name for the new vault. A new vault is created in the same Resource Group and location as the virtual machine.
6. Click **Backup policy**. For this example, keep the defaults and click **OK**.
7. On the **Enable backup** blade, click **Enable Backup**. This will create a daily backup based on the default schedule.
10. To create an initial recovery point, on the **Backup** blade click **Backup now**.
11. On the **Backup Now** blade, click the calendar icon, use the calendar control to select the last day this recovery point is retained, and click **Backup**.


The first backup takes about 20 minutes. Proceed to the next part of this tutorial after you backup is finished.

## Restore a file

If you accidentally delete or make changes to a file, you can use File Recovery to recover the file from your backup vault. File Recovery uses a script that runs on the VM, to mount the recovery point as local drive. These drives will remain mounted for 12 hours so that you can copy files from the recovery point and restore them to the VM.  

In this example, we are using a Linux VM running Ubuntu and nginx to show how to recover the default nginx web page /var/www/html/index.nginx-debian.html. The public IP address of our VM in this example is *52.233.179.229*.

1. On your local computer, open a browser and type in the public IP address of your VM to see the default nginx web page.

	![Default nginx web page](./media/tutorial-backup-vms/nginx-working.png)

1. SSH into your VM.

    ```bash
	ssh 52.233.179.229
	```
2. Delete /var/www/html/index.nginx-debian.html.

    ```bash
	sudo rm /var/www/html/index.nginx-debian.html
	```
	
4. On your local computer, refresh the browser by hitting CTRL + F5 to see that default nginx page is gone.

	![Default nginx web page](./media/tutorial-backup-vms/nginx-broken.png)
	
1. On your local computer, sign in to the [Azure portal](https://portal.azure.com/).
6. In the menu on the left, select **Virtual machines**. 
7. From the list, select the VM.
8. On the VM blade, in the **Settings** section, click **Backup**. The **Backup** blade opens. 
9. In the menu at the top of the blade, select **File Recovery (Preview)**. The **File Recovery (Preview) blade opens.
10. In **Step 1: Select recovery point**, select a recovery point from the drop-down.
11. In **Step 2: Download script to browse and recover files**, click the **Download Executable** button. Save the downloaded file to your local computer.
7. Click **Download script** to download the script file locally.
8. Open a Bash prompt and type the following, replacing *Linux_myVM_05-02-2017.sh* with the correct path and filename for the script that you downloaded, *azureuser* with the username for the VM and *52.233.179.229* with the public IP address for your VM.
    ```bash
	scp Linux_myVM_05-02-2017.sh azureuser@52.233.179.229:
	```
9. Open an SSH connection to the VM.
    ```bash
	ssh 52.233.179.229
	```
10. Add execute permissions to the script file.
    ```bash
	chmod +x Linux_myVM_05-02-2017.sh
	```
11. Run the script to mount the recovery point as a filesystem.
12. Copy the nginx default web page from the mount point back to where you deleted the file.

    ```bash
	sudo cp ~/Linux_myVM_05-02-2017.sh/Volume1/var/www/index.nginx-debian.html /var/www/
	```
	
17. On your local machine, open the browser tab where you are connected to the IP address of the VM showing the nginx default page. Press CTRL + F5 to refresh the browser page. You should now see that the default page is working again.

18. On your local computer, go back to the browser tab for the Azure portal and in **Step 3: Unmount the disks after recovery** click the **Unmount Disks** button.


## Next steps

In this tutorial, you have learned about backing up VMs. Advance to the next tutorial to learn about Azure security center.

[Manage VM security](tutorial-azure-security.md)

