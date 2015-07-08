<properties 
   pageTitle="Use StorSimple Manager service to administer your StorSimple device"
   description="Manage your StorSimple device by using the StorSimple Manager service in Azure Management Portal."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2015"
   ms.author="alkohli" />

# Use StorSimple Manager service to administer your StorSimple device

## Overview

This article describes the StorSimple Manager service interface, including how to connect to it, the various options available, and links out to the specific workflows that can be performed via this UI. This guidance is applicable to both; the StorSimple physical and the virtual device.

After reading this article, you will be able to understand:

- How to connect to StorSimple Manager service
- How to navigate the StorSimple Manager UI
- How to perform day-to-day administration of your StorSimple device via StorSimple Manager service


## Connecting to the StorSimple Manager service

The StorSimple Manager service runs in Microsoft Azure and connects to multiple StorSimple devices. You use a central Microsoft Azure Management Portal running in a browser to manage these devices.

#### To connect to the service

1. Navigate to [http://azure.microsoft.com/](http://azure.microsoft.com/)

1. Using your Microsoft account credentials, log on to the Microsoft Azure Management Portal (located at the top-right of the pane).

1. Scroll down the left navigation pane to access the StorSimple Manager service.

## Navigate StorSimple Manager service UI

The navigational hierarchy for the StorSimple Manager service UI is shown in the following table.

- Click **StorSimple Manager** landing page to go to the UI service-level pages applicable to all devices within a service.

- Click **Devices** page to go to device–level UI pages applicable to a specific device.

- Click **Volume Containers** to see all the volumes associated with a device.

For more information about each page in the hierarchy, click the appropriate entry in the table.

### StorSimple Manager service navigational hierarchy

|Main landing page|Service-level pages|Device-level pages|
|---|---|---|
|StorSimple Manager|Service dashboard|Device dashboard|
|Devices →|Monitor|
|Backup catalog|Volume containers→|Volumes|
|Configure|Backup policies||
|Jobs|Configure|
|Alerts|Maintenance|


## Administer StorSimple device using your StorSimple Manager service

The following table shows a summary of all the common management tasks and complex workflows that can be performed within the StorSimple Manager service UI. These tasks are organized based on the UI pages on which they are initiated.

For more information about each workflow, click the appropriate entry in the table.

### StorSimple Manager workflows

|If you want to do this ...|Go to this UI page ...|Use this procedure.|
|---|---|---|
|Create a service</br>Delete a service</br>Get the service registration key</br>Regenerate the service registration key|StorSimple Manager service|[Deploy a StorSimple Manager service](storsimple-manage-service)
|Change the service data encryption key</br>View the operation logs|StorSimple Manager service → Dashboard|[Use StorSimple Manager service dashboard](https://azure.microsoft.com/en-us/documentation/articles/storsimple-service-dashboard/)|
|Deactivate a device</br>Delete a device</br>Disaster recovery and device failover|StorSimple Manager service → Devices|[Deactivate or delete a device]()</br></br>[Failover and disaster recovery for your StorSimple device](storsimple-device-failover-disaster-recovery)
|List backups for a volume</br>Select a backup set</br>Delete a backup set</br>Clone a volume</br>Restore a backup set|StorSimple Manager service → Backup Catalog|[Manage backups](storsimple-manage-backups)</br></br></br>[Clone a volume](storsimple-clone-volume)</br>[Restore a backup set](storsimple-restore-from-backup-set)
|About  storage accounts</br>Add a storage account</br>Edit a storage account</br>Delete a storage account</br>Key rotation of storage accounts</br></br>About bandwidth templates</br>Add a bandwidth template</br>Edit a bandwidth template</br>Delete a bandwidth template</br>Use a default bandwidth template</br>Create an all-day bandwidth template that starts at a specified time</br></br>About access control records</br>Create an access control record</br>Edit an access control record</br>Delete an access control record|StorSimple Manager service → Configure|[Manage storage accounts](storsimple-manage-storage-accounts)</br></br></br></br>[Manage bandwidth templates](storsimple-manage-bandwidth-templates)</br></br></br></br></br></br></br>[Manage access control records](storsimple-manage-acrs)|
|View job details</br>Cancel a job|StorSimple Manager service → Jobs|[Manage jobs](storsimple-manage-jobs)
|Receive alert notifications</br>Manage alerts</br>Review alerts|StorSimple Manager service → Alerts|[Manage alerts on your StorSimple device](storsimple-manage-alerts)
|View connected initiators</br>Find the device serial number</br>Find the target IQN|StorSimple Manager service → Devices → Dashboard|
|Monitor|StorSimple Manager service → Devices → Monitor|
|Add a volume container</br>Modify a volume container</br>Delete a volume container|StorSimple Manager service → Devices → Volume Containers|[Manage volume containers](storsimple-manage-volume-containers)|
|Add a volume</br>Modify a volume</br>Take a volume offline</br>Delete a volume</br>Monitor a volume|StorSimple Manager service → Devices → Volume Containers → Volumes|[Manage volumes](storsimple-manage-volumes)|
|Configure device settings</br>Configure time settings</br>Configure DNS settings</br>View web proxy settings</br>Configure network interfaces</br>Configure Snapshot Manager</br>Configure device administrator password</br>Configure remote management</br>Configure alert settings</br></br>Configure CHAP for your StorSimple device|Management Portal → Devices → Configure|[Configure CHAP for your StorSimple device](https://msdn.microsoft.com/library/dn772351.aspx)
|Add a backup policy</br>Add or modify a schedule</br>Delete a backup policy</br>Take a manual backup</br>Create a custom backup policy with multiple volumes and schedules|Management Portal → Devices → Backup policies|[Manage backup policies](storsimple-manage-backup-policies)|
|Manage device controllers</br>Monitor hardware status (on-premises device only)</br>Create a support package</br>Install software updates|Management Portal → Devices → Maintenance|

##Next steps
If you experience any issues with the day-to-day operation of your StorSimple device or any of its hardware components, refer to:

- [Troubleshoot an operational device](https://azure.microsoft.com/en-us/documentation/articles/storsimple-troubleshoot-operational-device/)
- [Use StorSimple monitoring indicator LEDs](https://azure.microsoft.com/en-us/documentation/articles/storsimple-monitoring-indicators/)
- [Use alerts and errors quick reference](https://msdn.microsoft.com/library/azure/dn905365.aspx)


If you continue to see issues, you want to:

-  [Contact Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx)