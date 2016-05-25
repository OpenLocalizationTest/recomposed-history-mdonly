<properties
	pageTitle="What to do in the event of an Azure service disruption impacting Azure Virtual Machines | Microsoft Azure"
	description="Learn what to do in the event of an Azure service disruption impacting Azure Virtual Machines."
	services="virtual-machines"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="virtual-machines"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#What to do in the event of an Azure service disruption impacting Azure Virtual Machines

At Microsoft we work hard to make sure our services are always available to you when you need them. Sometimes forces beyond our control impact us in ways that cause unplanned services disruptions.

Microsoft provides a Service Level Agreements (SLAs) for its services as a commitment for uptime and connectivity. The SLA for individual Azure services can be found under [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure already has many built-in platform features that support highly available applications. for more on these services please read [Disaster Recovery and High Availability for Azure Applications](https://aka.ms/drtechguide).

This document covers a true disaster recovery, when a whole region experiences an outage due to major natural disaster or widespread service interruption. These are rare occurrences; but you must prepare for the possibility that there is an outage of the entire region. If an entire region experiences a service disruption, the locally redundant copies of your data would temporarily be unavailable. If you have enabled Geo-replication, there are three additional copies of your Azure Storage blobs and tables stored in a different region. In the event of complete regional outage or a disaster in which the primary region is not recoverable, Azure remaps all of the DNS entries to the geo-replicated region. 
 
>[AZURE.NOTE]Be aware that you do not have any control over this process, and it will only occur for region-wide failures. Because of this, you must also rely on other application-specific backup strategies to achieve the highest level of availability. For more information, see the section on [Data Strategies for Disaster Recovery](https://aka.ms/drtechguide#DSDR). 

To help you handle these rare occurrences, we provide you the following guidance for Azure Virtual Machine in the case of a service disruption of the entire region where your Azure Virtual Machine application is deployed. 

##Option 1: Wait for recovery 
In this case, no action on your part is required. Know that we are working diligently to restore service availability. You can see the current service status on our [Azure Service Health Dashboard](https://azure.microsoft.com/status/).

>[AZURE.NOTE]This is the best option if you have not setup Azure Site Recovery (ASR), Virtual Machine (VM) backup, Read-Access Geo-Redundant Storage (RA-GRS) or Geo-Redundant Storage (GRS) prior to the disruption. If you have set up GRS (or RA-GRS) storage for the storage account where your VM VHDs are stored, you can look at recovering the base image VHD and try to provision a new VM from it. This is not a prefered option because there is no guarantees of synchronization of data unless Azure VM backup or ASR are used and thus it is not guaranteed to work.

For customers desiring immediate access to virtual machines, the below two options are available.  

>[AZURE.NOTE]Be aware that both of the below options have the possibility of some data loss.     

##Option 2: Restore a Virtual Machine (VM) from a backup 
For customers who have a VM backup configured, you can restore the VM from its backup and recovery point. 

Follow these steps to restore a new VM from an Azure Backup see Restore virtual machines in Azure.

To help you plan for your Azure Virtual Machines backup infrastructure please read the article, [Plan your VM backup infrastructure in Azure](../backup/backup-azure-vms-introduction.md).

##Option 3: Initiate a failover using Azure Site Recovery (ASR) 
If you have configured Azure Site Recovery to work with your impacted Azure Virtual Machines, you can restore your VM(s) from their replica(s). These replicas can reside either on Azure or even on-premises. In this case, you can create a new VM(s) from its existing replica.  Follow these steps to restore your VMs from an Azure Site Recovery Replica please see [Migrate Azure IaaS virtual machines between Azure regions with Azure Site Recovery](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Although Azure Virtual Machine OS and data disks will be replicated to a secondary Virtual Hard Drive (VHD), if they are in a GRS or RA-GRS storage account, each VHD is replicated independently and this level of replication doesn’t guarantee consistency across the VHDs replicated. If your application and/or databases using these data disks have dependencies on each other, it is not guaranteed that all VHDs are replicated as one snapshot. It is also not guaranteed that the VHD replica from GRS or RA-GRS storage will result in an application consistent snapshot to boot the VM.

##References 
[Disaster Recovery and High Availability for Azure Applications](https://aka.ms/drtechguide)

[Azure Business Continuity Technical Guidance](http://aka.ms/bctechguide)

[Back up Azure virtual machines](../backup/backup-azure-vms.md)

[Azure Site Recovery](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)

If the instructions are not clear, or if you would like Microsoft to do the operations on your behalf please contact [Customer Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
