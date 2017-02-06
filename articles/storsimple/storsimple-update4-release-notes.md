---
title: StorSimple 8000 Series Update 4 release notes | Microsoft Docs
description: Describes the new features, issues, and workarounds for StorSimple 8000 Series Update 4.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''

ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/06/2017
ms.author: alkohli

---
# StorSimple 8000 Series Update 4 release notes

## Overview

The following release notes describe the new features and identify the critical open issues for StorSimple 8000 Series Update 4. They also contain a list of the StorSimple software updates included in this release. 

Update 4 can be applied to any StorSimple device running Release (GA) or Update 0.1 through Update 3.1. The device version associated with Update 4 is 6.3.9600.17818.

Please review the information contained in the release notes before you deploy the update in your StorSimple solution.

> [!IMPORTANT]
> * Update 4 has device software, USM firmware, LSI driver and firmware, Storport and Spaceport, security, and other OS updates. It takes approximately 1.5-2 hours to install this update. 
> * For new releases, you may not see updates immediately because we do a phased rollout of the updates. Wait a few days, and then scan for updates again as these will become available soon.

## What's new in Update 4

The following key improvements and bug fixes have been made in Update 4.

* **Automated space reclamation changes** – Starting Update 4, more intelligence has been built into the space reclamation algorithms thereby resulting in faster execution.
* **Performance enhancements** – Update 4 has improved locally pinned volume ingestion performance.
* **Standalone diagnostics tool** – In Update 4, a StorSimple Diagnostics tool is being released that allows for easy diagnosing and troubleshooting of issues related to system, network, and hardware health. To download this tool, go to [StorSimple Diagnostics tool]().
* **Migration tool** - In this release, a StorSimple Migration tool is made available for Support and customers to facilitate the consolidation of recovery buckets during the migration workflow. To download this tool, go to [StorSimple Migration tool](). 
* **Monitoring related fixes** - In this release, bugs related to monitoring charts, service dashboard, and device dashboard were fixed.
* **MPIO support for StorSimple Snapshot Manager** - From this release onwards, StorSimple Snapshot Manager has support built-in for MPIO.
* **FIPS-related changes** - In this release, FIPS will be enabled by default on all the StorSimple 8000 series devices. This implies that cryptographic validations will occur for all the devices.
* **Failback optimizations** - In Update 4, several failback optimizations have been implemented.


## Issues fixed in Update 4
The following table provides a summary of issues that were fixed in Update 4.    

| No | Feature | Issue | Applies to physical device | Applies to virtual device |
| --- | --- | --- | --- | --- |
| 1 |Host-side data migration |In the earlier release, the StorSimple Cloud Appliance was going offline during a host-side data migration. This issue is fixed in this release. |No |Yes |
| 2 |Locally pinned volumes |In the previous release, there were issues related to I/O failures, volume conversion failures, and datapath failures for locally pinned volumes. These issues were root-caused and fixed in this release. |Yes |No |
| 3 |Monitoring |There were multiple issues related to reporting units and monitoring as well as device dashboard charts where incorrect information was displayed for locally pinned volumes. These issues are fixed in this release. |Yes |No |
| 4 |Heavy writes I/O |When using StorSimple for workloads involving heavy writes, the user would run into an infrequent bug where the working set was being tiered into the cloud. This bug is fixed in this release. |Yes |Yes |
| 5 |Backup |In certain rare instances, in the previous versions of software, when user took a backup of a remote clone, they would run into cloud errors and the operation would error out. In this release, the issue is fixed and the operation completes successfully. |Yes |Yes |
| 6 |Backup policy |In certain rare instances, in the earlier releases of software, there was a bug related to the deletion of backup policy. This issue is fixed in this release. |Yes |Yes |

## Known issues in Update 4

The following table provides a summary of known issues in this release.

| No. | Feature | Issue | Comments / workaround | Applies to physical device | Applies to virtual device |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk quorum |In rare instances, if the majority of disks in the EBOD enclosure of an 8600 device are disconnected resulting in no disk quorum, then the storage pool will go offline. It will stay offline even if the disks are reconnected. |You will need to reboot the device. If the issue persists, please contact Microsoft Support for next steps. |Yes |No |
| 2 |Incorrect controller ID |When a controller replacement is performed, controller 0 may show up as controller 1. During controller replacement, when the image is loaded from the peer node, the controller ID can show up initially as the peer controller’s ID. In rare instances, this behavior may also be seen after a system reboot. |No user action is required. This situation will resolve itself after the controller replacement is complete. |Yes |No |
| 3 |Storage accounts |Using the Storage service to delete the storage account is an unsupported scenario. This will lead to a situation in which user data cannot be retrieved. | |Yes |Yes |
| 4 |Device failover |Multiple failovers of a volume container from the same source device to different target devices is not supported. Failover from a single dead device to multiple devices will make the volume containers on the first failed over device lose data ownership. After such a failover, these volume containers will appear or behave differently when you view them in the Azure classic portal. | |Yes |No |
| 5 |Installation |During StorSimple Adapter for SharePoint installation, you need to provide a device IP in order for the install to finish successfully. | |Yes |No |
| 6 |Web proxy |If your web proxy configuration has HTTPS as the specified protocol, then your device-to-service communication will be affected and the device will go offline. Support packages will also be generated in the process, consuming significant resources on your device. |Make sure that the web proxy URL has HTTP as the specified protocol. For more information, go to [Configure web proxy for your device](storsimple-configure-web-proxy.md). |Yes |No |
| 7 |Web proxy |If you configure and enable web proxy on a registered device, then you will need to restart the active controller on your device. | |Yes |No |
| 8 |High cloud latency and high I/O workload |When your StorSimple device encounters a combination of very high cloud latencies (order of seconds) and high I/O workload, the device volumes go into a degraded state and the I/Os may fail with a "device not ready" error. |You will need to manually reboot the device controllers or perform a device failover to recover from this situation. |Yes |No |
| 9 |Azure PowerShell |When you use the StorSimple cmdlet **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** to select the first object so that you can create a new **VolumeContainer** object, the cmdlet returns all the objects. |Wrap the cmdlet in parentheses as follows: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Yes |Yes |
| 10 |Migration |When multiple volume containers are passed for migration, the ETA for latest backup is accurate only for the first volume container. Additionally, parallel migration will start after the first 4 backups in the first volume container are migrated. |We recommend that you migrate one volume container at a time. |Yes |No |
| 11 |Migration |After the restore, volumes are not added to the backup policy or the virtual disk group. |You will need to add these volumes to a backup policy in order to create backups. |Yes |Yes |
| 12 |Migration |After the migration is complete, the 5000/7000 series device must not access the migrated data containers. |We recommend that you delete the migrated data containers after the migration is complete and committed. |Yes |No |
| 13 |Clone and DR |A StorSimple device running Update 1 cannot clone or perform disaster recovery to a device running pre-update 1 software. |You will need to update the target device to Update 1 to allow these operations |Yes |Yes |
| 14 |Migration |Configuration backup for migration may fail on a 5000-7000 series device when there are volume groups with no associated volumes. |Delete all the empty volume groups with no associated volumes and then retry the configuration backup. |Yes |No |
| 15 |Azure PowerShell cmdlets and locally pinned volumes |You cannot create a locally pinned volume via Azure PowerShell cmdlets. (Any volume you create via Azure PowerShell will be tiered.) |Always use the StorSimple Manager service to configure locally pinned volumes. |Yes |No |
| 16 |Space available for locally pinned volumes |If you delete a locally pinned volume, the space available for new volumes may not be updated immediately. The StorSimple Manager service updates the local space available approximately every hour. |Wait for an hour before you try to create the new volume. |Yes |No |
| 17 |Locally pinned volumes |Your restore job exposes the temporary snapshot backup in the Backup Catalog, but only for the duration of the restore job. Additionally, it exposes a virtual disk group with prefix **tmpCollection** on the **Backup Policies** page, but only for the duration of the restore job. |This behavior can occur if your restore job has only locally pinned volumes or a mix of locally pinned and tiered volumes. If the restore job includes only tiered volumes, then this behavior will not occur. No user intervention is required. |Yes |No |
| 18 |Locally pinned volumes |If you cancel a restore job and a controller failover occurs immediately afterwards, the restore job will show **Failed** instead of **Canceled**. If a restore job fails and a controller failover occurs immediately afterwards, the restore job will show **Canceled** instead of **Failed**. |This behavior can occur if your restore job has only locally pinned volumes or a mix of locally pinned and tiered volumes. If the restore job includes only tiered volumes, then this behavior will not occur. No user intervention is required. |Yes |No |
| 19 |Locally pinned volumes |If you cancel a restore job or if a restore fails and then a controller failover occurs, an additional restore job appears on the **Jobs** page. |This behavior can occur if your restore job has only locally pinned volumes or a mix of locally pinned and tiered volumes. If the restore job includes only tiered volumes, then this behavior will not occur. No user intervention is required. |Yes |No |
| 20 |Locally pinned volumes |If you try to convert a tiered volume (created and cloned with Update 1.2 or earlier) to a locally pinned volume and your device is running out of space or there is a cloud outage, then the clone(s) can be corrupted. |This problem occurs only with volumes that were created and cloned with pre-Update 2.1 software. This should be an infrequent scenario. | | |
| 21 |Volume conversion |Do not update the ACRs attached to a volume while a volume conversion is in progress (tiered to locally pinned or vice versa). Updating the ACRs could result in data corruption. |If needed, update the ACRs prior to the volume conversion and do not make any further ACR updates while the conversion is in progress. | | |
| 22 |Updates |When applying Update 3, the **Maintenance** page in the Azure classic portal will display the following message related to Update 2 - "StorSimple 8000 series Update 2 includes the ability for Microsoft to proactively collect log information from your device when we detect potential problems". This is misleading as it indicates that the device is being updated to Update 2. After the device is successfully updated to Update 4, this message will disappear. |This behavior will be fixed in a future release. |Yes |No |

## Controller and firmware updates in Update 4

This release has LSI driver and firmware updates. For more information on how to install the LSI driver and firmware updates, see [install Update 4](storsimple-install-update-4.md) on your StorSimple device.

## Virtual device updates in Update 4

This update cannot be applied to the StorSimple Cloud Appliance (also known as the virtual device). New virtual devices will need to be created. 

## Next step

Learn how to [install Update 4](storsimple-install-update-4.md) on your StorSimple device.

