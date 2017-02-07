---
title: How to Reprotect from Azure to On-premises | Microsoft Docs
description: After failover of VMs to Azure, you can initiate a failback to bring VMs back to on-premises. Learn the steps how to do a reprotect before a failback.
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: gauravd
editor: ''

ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 12/15/2016
ms.author: ruturajd

---
# Reprotect from Azure to On-premises

## Overview
This article describes how to reprotect Azure virtual machines from Azure to the on-premises site. Follow the instructions in this article when you're ready to fail back your VMware virtual machines or Windows/Linux physical servers after they've failed over from the on-premises site to Azure using this [tutorial](site-recovery-vmware-to-azure-classic.md).

After reprotect completes and the protected virtual machines are replicating, you can initiate a failback on the VM to bring them to on-premises.

Post any comments or questions at the bottom of this article, or on the [Azure Recovery Services Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Pre-requisites
Following are the few pre-requisite steps you need to take or consider when preparing for reprotect.

### Reprotect needs a S2S VPN or an express route to replicate data back to on-premises
Where replication from on-premises to Azure can happen over internet or an Express route with public peering, reprotect and failback requires a S2S VPN set up to replicate data. The network should be provided such that the failed over VMs in Azure can reach the on-premises configuration server. You may be also deploying a process server in the Azure network of the failed over VM - this process server should also be able to communicate with the on-premises configuration server.

### Process server is needed to replicate the data from source VMs to on-premises
<!-- Read more about a process server here.!todo -->

Th Azure VMs that you want to reprotect, send the replication data to a Process server. Your network should be set up such that the process server is reachable from the Azure VM.

You can deploy a process server in Azure or use the existing process server that you used during failover. The important point to consider is the latency to send the data from Azure VMs to the process server. 

* If you have an express route set up, an on-premises PS can be used to send the data. This is because the latency between the VM and the PS would be low.
    
    ![Architecture Diagram for Expressroute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* However, if you only have a S2S VPN, then we recommend deploying the process server in Azure.

    ![Architecture Diagram for VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Remember, that the replication will only happen over S2S VPN, or the private peering of your Express route network. Ensure that enough bandwidth is available over that network channel.

### Ports to be opened on different machines so that all the components can communicate

![Failover-Failback all ports](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### Master target needs to be available on-premises to receive data from process server
A master target server is required on-premises to receive the data from the process server and then write to the on-premises VM's VMDK. If you are protecting Windows VMs, you need a Windows master target server, and here you can reuse the on-premises PS+MT <!-- !todo component -->. For Linux VMs, you will need to setup an additional Linux master target on-premises.

Click on the following links to reads the steps on How to install a Master Target server.

* [How to install Windows Master Target server](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [How to install Linux Master Target server](site-recovery-how-to-install-linux-master-target.md)


#### Common things to check after completing installation of Master Target

* If the VM is present on-premises on the vCenter server, master target server needs access to the on-premises VM's VMDK. This is to write the replicated data to the VM's disks. For this you need to ensure that **the on-premises VM's datastore should be mounted on the MT's host with read write access**.

* If the VM is not present on-premises on the vCenter server, a new VM will need to be created during reprotect. This VM will be created on the ESX host on which you create the MT. Hence, choose the ESX host carefully, so that the failback VM is created on the host you want.
    
* **Master target server cannot be storage vMotioned**. This can cause the failback to fail. The VM will not come up since the disks will not be made available to it.

* You need a new drive added onto your existing Master target server. This drive is called a retention drive. Add a new disk and format the drive. Retention Drive is used for stopping the points in time when the VM replicated back to on-premises. Some of the criteria of a retention drive are as below, without which the drive will not be listed for the master target server.
   
   a. Volume shouldn't be in use for any other purpose (target of replication etc.)

   b. Volume shouldn't be in lock mode.

   c. Volume shouldn't be cache volume. (MT installation shouldn't exist on that volume. PS+MT custom installation volume is not eligible for retention volume. Here installed PS+MT volume is cache volume of MT.)

   d. The Volume File system type shouldn't be FAT and FAT32.

   e. The volume capacity should be non-zero.

   e. Default retention volume for Windows is R volume.

   f. Default retention volume for Linux is /mnt/retention.

* A linux failed over VM, needs a Linux Master target server. A Windows failed over VM, requires a Windows master target server.

* Install VMware tools on the master target server. Without the VMware tools, the datastores on the MT's ESXi host cannot be detected.

* Enable the disk.EnableUUID = True parameter on the MT VM via the vCenter properties. <!-- !todo Needs link. -->



### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)



## Steps to reprotect

1. In the Vault > replicated items > select the VM that's been failed over and right click to **Re-Protect**. You can also click the machine and select the reprotect from the command buttons.
2. In the blade, you can see that the direction of protection "Azure to On-premises" is already selected.
3. In **Master Target Server** and **Process Server** select the on-premises master target server, and the process server.
4. Select the **Datastore** to which you want to recover the disks on-premises. This option is used when the on-premises VM is deleted and new disks needs to be created. This option is ignored if the disks already exists, but you still need to specify a value.
5. Choose the retention drive. 
6. The failback policy will be auto selected.
7. After you click **OK** to begin reprotection a job begins to replicate the VM from Azure to the on-premises site. You can track the progress on the **Jobs** tab.

If you want to recover to an alternate location (when the on-premises VM is deleted), select the retention drive and datastore configured for the master target server. When you fail back to the on-premises site the VMware VMs in the failback protection plan will use the same datastore as the master target server and a new VM will be created in vCenter. 
If you want to recover the Azure VM to an existing on-premises VM then the on-premises VM's datastores should be mounted with read/write access on the master target server's ESXi host.
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

You can also reprotect at a recovery plan level. If you have a replication group, it can only be reprotected using a recovery plan. While reprotecting via a recovery plan, you will need to give the above values for every protected machine.

> [!NOTE]
> A replication group should be protected back using the same Master target. If they are protected back using different Master target server, a common point in time cannot be taken for it.
> 
> 

After the reprotect succeed, the VM will enter into a protected state.

## Next steps

Once the VM has entered into protected state, you can initiate a failback. The failback will shutdown the VM in Azure and boot the VM on-premises. Hence there is a small downtime for the application. So choose the time for failback when your application can face a downtime.

[Steps to initiate failback of the VM](site-recovery-how-to-failback-v2a.md#steps-to-failback)

## Common issues 
