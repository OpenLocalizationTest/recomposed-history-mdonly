---
title: Azure Advisor High Availability recommendations | Microsoft Docs
description: Use Azure Advisor to improve high availability of your Azure deployments.
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: ''

ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
---

# Advisor High Availability recommendations

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. You can get high availability recommendations by Advisor from the **High Availability** tab of the Advisor dashboard.

![High Availability button on the Advisor dashboard](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## Virtual machines without an availability set

Advisor identifies virtual machines that are not part of an availability set and recommends moving them into an availability set. To provide redundancy to your application, we recommend that you group two or more virtual machines in an availability set. This configuration ensures that during either a planned or unplanned maintenance event, at least one virtual machine is available and meets the Azure virtual machine SLA. You can choose either to create an availability set for the virtual machine or to add the virtual machine to an existing availability set.

> [!NOTE]
> If you choose to create an availability set, you must add at least one additional virtual machine to it. We recommend that you group two or more virtual machines in an availability set to ensure that at least one machine is available during an outage.

![Advisor recommendation: For virtual machine redundancy, use availability sets](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## Availability sets with a single virtual machine 

Advisor identifies availability sets that contain a single virtual machine and recommends adding one or more virtual machines to it. To provide redundancy to your application, we recommend that you group two or more virtual machines in an availability set. This configuration ensures that during either a planned or unplanned maintenance event, at least one virtual machine is available and meets the Azure virtual machine SLA. You can choose either to create a virtual machine or to use an existing virtual machine, and to add it to the availability set.  

![Advisor recommendation: For high availability, add one or more VMs to this availability set](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)

## Virtual machines with Standard Disks

Advisor identifies virtual machines that have Standard Disks and recommends upgrading to Premium Disks.  

Azure Premium Storage delivers high-performance, low-latency disk support for virtual machines that run I/O-intensive workloads. virtual machine disks in premium storage accounts store data on solid-state drives (SSDs). For the best performance for your application, we recommend that you migrate any virtual machine disks that require high I/O operations per second (IOPS) to premium storage. If a disk does not require high IOPS, you can limit costs by maintaining it in a standard storage account. Standard storage stores virtual machine disk data on hard-disk drives (HDDs) instead of SSDs. You can choose to migrate your virtual machine disks to Premium Disks, which are supported on most virtual machine SKUs. In some cases, however, if you want to use Premium Disks, you might need to upgrade your virtual machine SKUs as well.   

![Advisor recommendation: Upgrade to Premium Disks](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png) 

## Your application gateway is not configured for fault tolerance
To ensure the business continuity of mission-critical applications that are powered by application gateways, Advisor identifies application-gateway instances that are not configured for fault tolerance, and suggests remediation actions that you can take. Advisor identifies medium or large single-instance application gateways, and it recommends adding at least one more instance. It also identifies single- or multi-instance small application gateways and recommends migrating to a medium or large SKU. Advisor recommends these actions to ensure that your application-gateway instances are configured to satisfy the current SLA requirements for these resources.


## Improve the reliability of your availability-set disks
Advisor identifies availability sets where virtual machine disks are placed in a single storage account, and it recommends migrating to Managed Disks. Virtual machine disks that are placed in the same storage account are located on the same storage-scale unit, and they are affected by the failure of a single storage-scale unit. By automatically placing disks in different storage scale units, Managed Disks ensure that virtual machine disks in an availability set are sufficiently isolated from each other. This structure ensures data redundancy by avoiding a single point of failure during an outage.


## Virtual machine disks that are susceptible to I/O throttling
Advisor identifies virtual machines that are susceptible to disk performance degradation resulting from I/O throttling, and it recommends migrating to Managed Disks. Placing multiple virtual machine disks that run I/O intensive workloads in the same storage account causes the storage account to reach its scalability limits, which can lead to throttling. Throttling can degrade the overall performance of virtual machines and cause other potential issues. Managed Disks handle storage-account placement for disks behind the scenes, so you are no longer limited by storage-account scalability limits.


## Virtual machines that are not configured for backup
Advisor identifies virtual machines where backup is not enabled, and it recommends enabling backup. Setting up virtual machine backup ensures the availability of your business-critical data and offers protection against accidental deletion or corruption.


## Access high availability recommendations in Advisor

1. Sign in to the [Azure portal](https://portal.azure.com).

2. In the left pane, click **More services**.

3. In the service menu pane, under **Monitoring and Management**, click **Azure Advisor**.  
 The Advisor dashboard is displayed.

4. On the Advisor dashboard, click the **High Availability** tab, and then select the subscription for which you want to receive recommendations.

> [!NOTE]
> To access Advisor recommendations, you must first *register your subscription* with Advisor. A subscription is registered when a *subscription Owner* launches the Advisor dashboard and clicks the **Get recommendations** button. This is a *one-time operation*. After the subscription is registered, you can access Advisor recommendations as *Owner*, *Contributor*, or *Reader* for a subscription, a resource group, or a specific resource.

## Next steps

For more information about Advisor recommendations, see:
*  [Introduction to Azure Advisor](advisor-overview.md)
*  [Get started with Advisor](advisor-get-started.md)
*  [Advisor Security recommendations](advisor-security-recommendations.md)
*  [Advisor Performance recommendations](advisor-performance-recommendations.md)
*  [Advisor Cost recommendations](advisor-performance-recommendations.md)
