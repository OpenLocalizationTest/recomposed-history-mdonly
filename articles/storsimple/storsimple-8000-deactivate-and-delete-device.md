---
title: Deactivate and delete a StorSimple 8000 series device | Microsoft Docs
description: Describes how to remove StorSimple device from service by  first deactivating it and then deleting it.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''

ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2017
ms.author: alkohli

---
# Deactivate and delete a StorSimple device

## Overview

You may wish to take a StorSimple device out of service (for example, if you are replacing or upgrading your device or if you are no longer using StorSimple). If so, you need to deactivate the device before you can delete it. Deactivation severs the connection between the device and the corresponding StorSimple Device Manager service. This tutorial explains how to remove a StorSimple device from service by first deactivating it and then deleting it. 

When you deactivate a device, any data that was stored locally on the device is no longer accessible. Only the data associated with the device that was stored in the cloud can be recovered.  

> [!WARNING]
> Deactivation is a PERMANENT operation and cannot be undone. A deactivated device cannot be registered with the StorSimple Device Manager service unless it is first reset by the factory. 
> 
> The factory reset process deletes all the data that was stored locally on your device. Therefore, you must take a cloud snapshot of all your data before you deactivate a device. This cloud snapshot allows you to recover all the data at a later stage.
 

This tutorial explains how to:

* Deactivate a device and delete the data
* Deactivate a device and retain the data

It also explains how deactivation and deletion works on a StorSimple Cloud Appliance.

> [!NOTE]
> Before you deactivate a StorSimple physical device or cloud appliance, stop or delete clients and hosts that depend on that device.


## Deactivate and delete data

If you are interested in deleting the device completely and do not want to retain the data on the device, then complete the following steps.

#### To deactivate the device and delete the data

1. Before you deactivate a device, you must delete all the volume containers (and the volumes) associated with the device. You can delete volume containers only after you have deleted the associated backups.
2. Deactivate the device as follows:
   
   1. Go to your StorSimple Device Manager service and click **Devices**. In the **Devices** blade, select the device that you wish to deactivate, right-click, and then click **Deactivate**.

        ![Deactivate StorSimple device](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. In the **Deactivate** blade, type the device name to confirm and then click **Deactivate**. The deactivate process starts and takes a few minutes to complete.

        ![Deactivate StorSimple device](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. After deactivation, you can delete the device completely. Deleting a device removes it from the list of devices connected to the service. The service can then no longer manage the deleted device. Use the following steps to delete the device:
   
   1. Go to your StorSimple Device Manager service and click **Devices**. In the **Devices** blade, select the deactivated device that you wish to delete, right-click, and then click **Delete**.

        ![Deactivate StorSimple device](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. In the **Delete** blade, type the device name to confirm and then click **Delete**. The deletion takes a few minutes to complete.

        ![Deactivate StorSimple device](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. After the deletion is successfully complete, you are notified. The device list also updates to reflect the deletion.

## Deactivate and retain data

If you are interested in deleting the device but want to retain the data, then complete the following steps:

#### To deactivate a device and retain the data
1. Deactivate the device. All the volume containers and the snapshots of the device remain.
   
   1. Go to your StorSimple Device Manager service and click **Devices**. In the **Devices** blade, select the device that you wish to deactivate, right-click, and then click **Deactivate**.

         ![Deactivate StorSimple device](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. In the **Deactivate** blade, type the device name to confirm and then click **Deactivate**. The deactivate process starts and takes a few minutes to complete.

         ![Deactivate StorSimple device](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)   
2. You can now fail over the volume containers and the associated snapshots. For procedures, go to [Failover and disaster recovery for your StorSimple device](storsimple-8000-device-failover-disaster-recovery.md).
3. After deactivation and failover, you can delete the device completely. Deleting a device removes it from the list of devices connected to the service. The service can then no longer manage the deleted device. To delete the device, complete the following steps:
   
   1. Go to your StorSimple Device Manager service and click **Devices**. In the **Devices** blade, select the deactivated device that you wish to delete, right-click, and then click **Delete**.

       ![Deactivate StorSimple device](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. In the **Delete** blade, type the device name to confirm and then click **Delete**. The deletion takes a few minutes to complete.

       ![Deactivate StorSimple device](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. After the deletion is successfully complete, you are notified. The device list also updates to reflect the deletion.
      
     
## Deactivate and delete a cloud appliance

For a StorSimple Cloud Appliance, deactivation deallocates the virtual machine. You can then delete the virtual machine and the resources created when it was provisioned. After the cloud appliance is deactivated, it cannot be restored to its previous state. 

Deactivation results in the following actions:

* The StorSimple Cloud Appliance is removed.
* The OSDisk and Data Disks created for the StorSimple Cloud Appliance are removed.
* The hosted service and Virtual Network that were created during provisioning are retained. If you are not using these entities, you should delete them manually.
* Cloud snapshots created by the StorSimple Cloud Appliance are retained.

## Next steps

* To restore the deactivated device to factory defaults, go to [Reset the device to factory default settings](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* For technical assistance, [contact Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* To learn more about how to use the StorSimple Device Manager service, go to [Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md). 

