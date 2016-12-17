---
  title: Migrating to Azure Premium Storage using Azure Site Recovery | Microsoft Docs
  description: Migrate your existing virtual machines to Azure Premium Storage using Site Recovery. Premium Storage offers high-performance, low-latency disk support for I/O-intensive workloads running on Azure Virtual Machines.
  services: storage
  cloud: Azure
  documentationcenter: na
  author: luywang
  manager: kavithag

  ms.assetid: 
  ms.service: <service per approved list>
  ms.workload: storage
  ms.tgt_pltfrm: na
  ms.devlang: na
  ms.topic: article
  ms.date: 12/16/2016
  ms.author: luywang

---
# Migrating to premium storage using Azure Site Recovery
## Overview

[Azure Premium Storage](storage-premium-storage.md) delivers high-performance, low-latency disk support for virtual machines that are running I/O-intensive workloads. The purpose of this guide is to help users migrate their VM disks from standard storage accounts to a premium storage account by using Azure Site Recovery.

Site Recovery is an Azure service that contributes to your business continuity and disaster recovery strategy by orchestrating the replication of on-premises physical servers and virtual machines to the cloud (Azure) or to a secondary datacenter. When outages occur in your primary location, you fail over to the secondary location to keep apps and workloads available. You fail back to your primary location when it returns to normal operations. In the scenario of migrating to Premium Storage, you can use Azure Site Recovery to failover/migrate target disks to a premium storage account.

Site Recovery provides test failovers to support disaster recovery drills without affecting production environments. You can run unplanned failovers with minimal data loss (depending on replication frequency) for unexpected disasters. Learn more in [What is Site Recovery?](../site-recovery/site-recovery-overview.md).

We recommend migrating to Premium Storage by using Site Recovery because this option provides minimal downtime and avoids the manual execution of copying disks and creating new VMs. Site Recovery will systematically copy your disks and create new VMs during failover. Site Recovery supports a number of types of failover with minimal or no downtime. To plan your downtime and estimate data loss, see the table in [Failover in Site Recovery](../site-recovery/site-recovery-failover.md#types-of-failover). You may need to manage your virtual network to ensure that replicated VMs are discoverable in a new location, so be sure to plan for external work before and after this migration.

![][1]

## Scenario components

**Configuration server**: An Azure VM that coordinates communication and manages data replication and recovery processes. On this machine you will run a single setup file to install the configuration server and an additional component, called a process server, as a replication gateway. Read about [configuration server prerequisites](../site-recovery/site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).

**Process server**: A replication gateway that receives replication data from protected source machines, optimizes the data with caching, compression, and encryption, and sends it to a storage account. It also handles push installation of the mobility service to protected machines and performs automatic discovery of protected VMs. The default process server is installed on the configuration server. You can deploy additional standalone process servers to scale your deployment. Read about [best practices for process server deployment](https://azure.microsoft.com/en-us/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) and [deploying additional process servers](../site-recovery/site-recovery-vmware-to-azure.md#deploy-additional-process-servers).

**Mobility service**: A component that is deployed on every machine you want to replicate. It captures data writes on the machine and forwards them to the process server. Read about [protected machine prerequisites](../site-recovery/site-recovery-vmware-to-azure.md#replicated-machine-prerequisites).

**Azure essentials**: An Azure subscription, an Azure premium storage account to store replicated data, and an Azure virtual network that VMs will connect to when they’re created at failover. The Azure virtual network must be in the same region as the Recovery Services vault. Besides a premium storage account to store replicated data, you'll need a standard storage account to store replication logs.

> [!NOTE]
> Site Recovery does not support the migration of Storage Spaces disks.

## Prerequisites

* Read and understand the scenario components in the preceding section. 
* Plan your downtime by learning about types of failover in [Failover in Site Recovery](../site-recovery/site-recovery-failover.md#types-of-failover).
* Migrate Azure IaaS virtual machines (the VMs you want to migrate) by treating them as physical machines.

## Setup and migration steps

You can use Site Recovery to migrate Azure IaaS VMs between regions or within same region. The migration instructions in this article are based on the instructions for replicating a physical machine to Azure. It includes links to the steps in [Replicate VMware VMs or physical servers to Azure](../site-recovery/site-recovery-vmware-to-azure.md).

1. [Create a Site Recovery vault](../site-recovery/site-recovery-vmware-to-azure.md#create-a-recovery-services-vault). Machines will be replicated to the region you specified in this step. For the purpose of migration in the same region, select the region where your source machines and source storage accounts are. Note that migration to Premium Storage accounts isn’t currently supported in the Azure classic portal. Create and manage the Site Recovery vault through the [Azure portal](https://portal.azure.com).

2. [Choose your protection goals](../site-recovery/site-recovery-vmware-to-azure.md#step-1-choose-your-protection-goals). On the VM where you want to install the configuration server, open the [Azure portal](https://portal.azure.com). Go to **ASR** > **Settings**. Under **Settings**, select **Site Recovery**. Under **Site Recovery**, select **Step 1: Prepare Infrastructure**. Under **Prepare infrastructure**, select **Protection goal**.
  
  ![][2]
  
  Under **Protection goal**, in the first drop-down list, select **To Azure**. In the second drop-down list, select **Not virtualized / Other**, and then click **OK**.
  
  ![][3]
  
3. [Set up the source environment (configuration server)](../site-recovery/site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment). Download the Azure Site Recovery unified setup and the vault registration key by going to the **Prepare infrastructure** > **Prepare source** > **Add Server** blade. You will need the vault registration key to run the unified setup.
  
  ![][4]
  
  ![][5]
  
  You can walk through the unified setup by referring to the following screenshots:
  
  ![][6]
  
  ![][7]
  
  ![][8]
  
  After the installation is complete, you will see the **Microsoft Azure Site Recovery Configuration Server** window. 
Use the **Manage Accounts** tab to create the account that Site Recovery can use for automatic discovery. (In the scenario about protecting physical machines, setting up the account isn’t relevant, but you need at least one account to enable one of the following steps. In this case, you can name the account and password as any.) 
Use the **Vault Registration** tab to upload the vault credential file.
  
  ![][9]

4. [Set up the target environment](../site-recovery/site-recovery-vmware-to-azure.md#step-3-set-up-the-target-environment). Specify the deployment model you want to use for VMs after failover. You can choose **Classic** or **Resource Manager**, depending on your scenario.
  
  ![][10]

5. [Set up replication settings](../site-recovery/site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Verify that your configuration server is successfully associated with the replication policy you create.

6. [Capacity planning](../site-recovery/site-recovery-vmware-to-azure.md#step-5-capacity-planning). Use the capacity planner to accurately estimate network bandwidth, storage, and other requirements to meet your replication needs.
  
  ![][11]

7. [Install mobility service and enable replication](../site-recovery/site-recovery-vmware-to-azure.md#step-6-replicate-applications). You can choose to push installation to your protected machines or to manually install mobility service on your protected machines. You can find the requirement of pushing installation and the path of the manual installer in the link provided. If you are doing a manual installation, you might need to use an internal IP address to find the configuration server. 
  
  ![][12]
  
  The failed-over VM will have two temporary disks: one from the primary VM and the other created during the provisioning of VM in the recovery region. To exclude the temporary disk before replication, install the mobility service before you enable replication. If you install the mobility service before you enable replication, you can exclude the temp disk as part of enabling replication. To learn more about how to exclude the temp disk, refer to [Replicate VMware virtual machines and physical machines to Azure with Azure Site Recovery using the Azure portal](../site-recovery/site-recovery-vmware-to-azure.md#exclude-disks-from-replication).
In the **Enable replication** column, follow steps 1–5: 
  * In step 1, set up Source as your process server. 
  * In step 2, specify the post-failover deployment model, a premium storage account to migrate to, a standard storage account to save logs, and a virtual network to fail to. 
  * In step 3, add protected machines by IP address (you might need an internal IP address to find them). 
  * In step 4, configure the properties by selecting the accounts you set up previously on the process server. 
  * In step 5, choose the replication policy you created previously, Set up replication settings. 
  Click **OK** and enable replication.
  
  > [!NOTE]
  > When an Azure VM is deallocated and started again, there is no guarantee that it will get the same IP address. If the IP address of the configuration server/process server or the protected Azure VMs change, the replication in this scenario might not work correctly.
  
  ![][13]
  
  When you design your Azure Storage environment, we recommend that you use separate storage accounts for each VM in availability set. We recommend that you follow the best practice in storage layer for [Windows](../virtual-machines/virtual-machines-windows-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set) and [Linux](../virtual-machines/virtual-machines-linux-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set) virtual machines. Distributing VMs disks to multiple storage accounts helps to improve storage availability and distributes the I/O across the Azure storage infrastructure. If your VMs are in an availability set, instead of replicating disks of all VMs into one storage account, we highly recommend migrating multiple VMs multiple times, so that the VMs in the same availability set do not share a single storage account. Use the **Enable Replication** blade to set up a destination storage account for each VM, one at a time. 
You can choose a post-failover deployment model according to your need. If you choose Resource Manager as your post-failover deployment model, you can fail over a Resource Manager VM deployment model to a Resource Manager deployment model, or you can fail over a virtual machine (classic) to a Resource Manager deployment model .

8. [Run a test failover](../site-recovery/site-recovery-vmware-to-azure.md#run-a-test-failover). To check whether your replication is complete, click your Site Recovery and then click **Settings** > **Replicated Items**. You will see the status and percentage of your replication process. After initial replication is complete, run Test Failover to validate your replication strategy. You can see the status of test failover in **Settings** > **Jobs** > **YOUR_FAILOVER_PLAN_NAME**. On the blade, you will see a breakdown of the steps and success/failure results. If the test failover fails at any step, click the step to check the error message. Make sure your VMs and replication strategy meet the requirements before you run an unplanned failover.

9. [Run an unplanned failover](../site-recovery/site-recovery-failover.md#run-an-unplanned-failover). After the test failover is completed, run an unplanned failover to migrate your disks to Premium Storage and replicate the VM instances. Site Recovery will create a VM instance whose type is the same as or similar to a Premium Storage–capable VM. You can check the performance and price of various VM instances by going to [Windows Virtual Machines Pricing](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/windows/) or [Linux Virtual Machines Pricing](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/).

## Post-migration steps

1. Configure replicated VMs to the availability set. Currently, because Site Recovery does not support migrating VMs along with the availability set, you need to configure the newly created VMs to the availability set. Depending on the deployment of your replicated VM, do either of the following:
  * For a virtual machine created using the classic deployment model: Add the VM to the availability set in the Azure portal. For detailed steps, go to [How to configure an availability set for Windows virtual machines in the classic deployment model](../virtual-machines/virtual-machines-windows-classic-configure-availability.md#a-idaddmachine-aoption-2-add-an-existing-virtual-machine-to-an-availability-set).
  * For the Resource Manager VM deployment model: Save your configuration of the VM and then delete and re-create the VMs in the availability set. To do so, use the script at [Set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).  Check the limitation of this script and plan downtime before running the script.

2. Delete old VMs and disks. To delete disks along with the VMs, find your old VMs in the classic portal or the Azure portal and delete the VMs that have attached disks. If you need to delete only old disks, in the classic deployment model, see [How to detach a disk from a Windows virtual machine](../virtual-machines/virtual-machines-windows-classic-detach-disk.md#detach-the-disk). In the Resource Manager deployment model, delete the VHD file from your old storage accounts in the Azure portal.

3. Clean the Azure Site Recovery infrastructure. If Site Recovery is no longer needed, you can clean its infrastructure by deleting replicated items, the configuration server, and the Recovery Policy, and then deleting the Azure Site Recovery vault.

## Toubleshooting

* [Monitor and troubleshoot protection for virtual machines and physical servers](../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft Azure Site Recovery forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)



[1]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
