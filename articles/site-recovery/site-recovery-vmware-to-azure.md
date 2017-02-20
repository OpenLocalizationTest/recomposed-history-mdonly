---
title: Replicate VMware VMs to Azure | Microsoft Docs
description: Summarizes the steps you need for replicating workloads running on VMware VMs to Azure storage
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: raynew

---
# Replicate VMware virtual machines to Azure with Azure Site Recovery

> [!div class="op_single_selector"]
> * [Azure portal](site-recovery-vmware-to-azure.md)
> * [Azure classic](site-recovery-vmware-to-azure-classic.md)


This article describes how to replicate on-premises VMware virtual machines to Azure, using the [Azure Site Recovery](site-recovery-overview.md) service in the Azure portal.

 If only you want to migrate VMware VMs with a simple failover, without full replication (replicate, failover, failback), read [this article](site-recovery-migrate-to-azure.md).

Post comments and questions at the bottom of this article, or in the [Azure Recovery Services Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Steps

Here's what you do:

1. Verify prerequisites and limitations.
2. Set up Azure network and storage accounts.
3. Prepare the on-premises machine that you want to deploy as the configuration server.
4. Prepare VMware accounts to be used for automatic discovery of VMs, and optionally for push installation of the Mobility service.
4. Create a Recovery Services vault. The vault contains configuration settings, and orchestrates replication.
5. Specify source, target, and replication settings.
6. Deploy the Mobility service on VMs you want to replicate.
7. Enable replication for VMs.
7. Run a test failover to make sure everything's working as expected.

## Prerequisites

**Support requirement** | **Details**
--- | ---
**Azure** | Learn about [Azure requirements](site-recovery-prereq.md#azure-requirements)
**On-premises configuration server** | You need a VMware VM running Windows Server 2012 R2 or later. You set up this server during Site Recovery deployment.<br/><br/> By default the process server and master target server are also installed on this VM. When you scale up, you might need a separate process server. If you do, it has the same requirements as the configuration server.<br/><br/> [Learn more](site-recovery-components.md#replicate-vmware-vmsphysical-servers-to-azure) about these components.
**On-premises VMware servers** | One or more VMware vSphere servers, running 6.0, 5.5, 5.1 with latest updates. Servers should be located in the same network as the configuration server (or separate process server).<br/><br/> We recommend a vCenter server to manage hosts, running 6.0 or 5.5 with the latest updates. Only features that are available in 5.5 are supported when you deploy version 6.0.
**On-premises VMs** | VMs you want to replicate should be running a [supported operating system](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions), and conform with [Azure prerequisites](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). VM should have VMware tools running.
**URLs** | The configuration server needs access to these URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> If you have IP address-based firewall rules, ensure they allow communication to Azure.<br/></br> Allow the [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653), and the HTTPS (443) port.<br/></br> Allow IP address ranges for the Azure region of your subscription, and for West US (used for Access Control and Identity Management).<br/><br/> Allow this URL for the MySQL download: http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
**Mobility service** | Installed on every replicated VM.



## Limitations

**Limitation** | **Details**
--- | ---
**Azure** | Storage and network accounts must be in the same region as the vault<br/><br/> If you use a premium storage account, you also need a standard store account to store replication logs<br/><br/> You can't replicate to premium accounts in Central and South India.
**On-premises configuration server** | VMware VM adapter type should be VMXNET3. If it isn't, [install this update](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> vSphere PowerCLI 6.0 should be installed.<br/><br> The machine shouldn't be a domain controller. The machine should have a static IP address.<br/><br/> The host name should be 15 characters or less, and operating system should be in English.
**VMware** | Only 5.5 features are supported in vCenter 6.0. Site Recovery doesn't support new vCenter and vSphere 6.0 features such as cross vCenter vMotion, virtual volumes, and storage DRS.
**VMs** | Verify [Azure VM limitations](site-recovery-prereq.md#azure-requirements)<br/><br/> You can't replicate VMs with encrypted disks, or VMs with UEFI/EFI boot.<br/><br> Shared disk clusters aren't supported. If the source VM has NIC teaming, it's converted to a single NIC after failover.<br/><br/> If VMs have an iSCSI disk, Site Recovery converts it to a VHD file after failover. If the iSCSI target can be reached by the Azure VM, it connects to it, and sees both it and the VHD. If this happens, disconnect the iSCSI target.<br/><br/> If you want to enable multi-VM consistency, which enables VMs running the same workload to be recovered together to a consistent data point, open port 20004 on the VM.<br/><br/> Windows must be installed on the C drive. The OS disk should be basic, and not dynamic. The data disk can be dynamic.<br/><br/> Linux /etc/hosts files on VMs should contain entries that map the local host name to IP addresses associated with all network adapters. The host name, mount points, device name, system paths, and file names (/etc; /usr) should be in English only.<br/><br/> Specific types of [Linux storage](site-recovery-support-matrix-to-azure.md#support-for-storage) are supported.<br/><br/>Create or set **disk.enableUUID=true** in the VM settings. This provides a consistent UUID to the VMDK, so that it mounts correctly, and ensures that only delta changes are transferred back to on-premises during failback, without full replication.


## Set up Azure

1. [Set up an Azure network](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

    - Azure VMs will be placed in this network when they're created after failover.
    - You can set up a network in [Resource Manager](../resource-manager-deployment-model.md), or in classic mode.

2. Set up an [Azure storage account](../storage/storage-create-storage-account.md#create-a-storage-account) for replicated data.

    - The account can be standard or [premium](../storage/storage-premium-storage.md).
    - You can set up an account in Resource Manager, or in classic mode.

3. [Prepare an account](#prepare-for-automatic-discovery-and-push-installation) on the vCenter server or vSphere hosts, so that Site Recovery can automatically detect VMware VMs.

## Prepare the configuration server

1. Install Windows Server 2012 R2 or later, on a VMware VM.
2. Make sure the VM has access the URLs listed in [prerequisites](#prerequisites).
3. Install [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0).


## Prepare for automatic discovery and push installation

- **Prepare an account for auto-discovery**: The Site Recovery process server automatically discovers VMs. To do this, Site Recovery needs credentials that can access vCenter servers/vSphere ESXi hosts.

    1. To use a dedicated account, create a role (at the vCenter level, with these [permissions](#vmware-account-permissions). Give it a name such as **Azure_Site_Recovery**.
    2. Then, create a user on the vSphere host/vCenter server, and assign the role to the user. You specify this user account during Site Recovery deployment.

- **Prepare an account to push the Mobility service**: If you want to push the Mobility service to VMs, you need an account that can be used by the process server to access the VM. The account is only used for the push installation. You can use a domain or local account:

    - For Windows, if you're not using a domain account, you need to disable Remote User Access control on the local machine. To do this, in the register under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, add the DWORD entry **LocalAccountTokenFilterPolicy**, with a value of 1.
    - If you want to add the registry entry for Windows from a CLI, type:
        ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
    - For Linux, the account should be root on the source Linux server.




## Create a Recovery Services vault
1. Sign in to the [Azure portal](https://portal.azure.com) > **Site Recovery**
2. Click **New** > **Management** >
3. In **Name**, specify a friendly name to identify the vault. If you have more than one subscription, select one of them.
4. [Create a resource group](../azure-resource-manager/resource-group-template-deploy-portal.md), or select an existing one. Specify an Azure region. To check supported regions, see Geographic Availability in [Azure Site Recovery Pricing Details](https://azure.microsoft.com/pricing/details/site-recovery/)
5. If you want to quickly access the vault from the Dashboard, click **Pin to dashboard** and then click **Create**.

    ![New vault](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

The new vault will appear on the **Dashboard** > **All resources**, and on the main **Recovery Services vaults** blade.


## Select the protection goal

Select what you want to replicate, and where you want to replicate to.

1. Click **Recovery Services vaults** > vault.
2. In the Resource Menu > click **Site Recovery** > **Step 1: Prepare Infrastructure** > **Protection goal**.

    ![Choose goals](./media/site-recovery-vmware-to-azure/choose-goals.png)
3. In **Protection goal**, select **To Azure**, and select **Yes, with VMware vSphere Hypervisor**.

    ![Choose goals](./media/site-recovery-vmware-to-azure/choose-goals2.png)

## Set up the source environment

Set up the configuration server, register it in the vault, and discover VMs.

1. Click **Site Recovery** > **Step 1: Prepare Infrastructure** > **Source**.
2. If you don’t have a configuration server, click **+Configuration server**.

    ![Set up source](./media/site-recovery-vmware-to-azure/set-source1.png)
3. In **Add Server**, check that **Configuration Server** appears in **Server type**.
4. Download the Site Recovery Unified Setup installation file.
5. Download the vault registration key. You need this when you run Unified Setup. The key is valid for five days after you generate it.

   ![Set up source](./media/site-recovery-vmware-to-azure/set-source2.png)
6. On configuration server VM, make sure that the system clock is synchronized with a [Time Server](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service), and then run Unified Setup to install the configuration server, the process server, and the master target server.

## Run Site Recovery Unified Setup

Before you start:

- Make sure that the time on the VM is the same as the time in your local time zone. It should match. If it's 15 minutes in front or behind, setup might fail.
- Run setup as a Local Administrator on the configuration server VM.
- Make sure TLS 1.0 is enabled on the VM.

Then run the Unified Setup installation file on the configuration server.

1. In Unified Setup > **Before you begin**, select **Install the configuration server and process server**.

   ![Before you start](./media/site-recovery-vmware-to-azure/combined-wiz1.png)
2. In **Third-Party Software License**, click **I Accept the third party license agreement**, to download and install MySQL.

    ![Third=party software](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)
3. In **Registration**, browse and select the registration key you downloaded from the vault.

    ![Registration](./media/site-recovery-vmware-to-azure/combined-wiz3.png)
4. In **Internet Settings**, specify how the Provider running on the configuration server connects to Site Recovery over the internet (443).

   * If you want the Provider to connect directly, select **Connect directly to Azure Site Recovery without a proxy server**.
   * If you want to use a proxy, select **Connect to Azure Site Recovery using a proxy server**, and specify the settings.

     ![Firewall](./media/site-recovery-vmware-to-azure/combined-wiz4.png)
5. In **Prerequisites Check**, setup verifies settings.  If a time warning appears, verify that the time in **Date and Time** settings is the same as the time zone.

    ![Prerequisites](./media/site-recovery-vmware-to-azure/combined-wiz5.png)
6. In **MySQL Configuration**, specify credentials that are used to log onto the MySQL server instance that will be installed.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)
7. In **Environment Details**, select whether you're going to replicate VMware VMs. If you are, then setup checks that PowerCLI 6.0 is installed.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)
8. In **Install Location**, select where you want to install the binaries and store the cache. You can select any drive that has 5 GB of storage available, but we recommend a cache drive with at least 600 GB of free space.

    ![Install location](./media/site-recovery-vmware-to-azure/combined-wiz8.png)
9. In **Network Selection**, specify the listener (network adapter and SSL port) on which the configuration server will send and receive replication data. You can modify the default port (9443). In addition to this port, port 443 is used to orchestrate replication. Don't use 443 for replication traffic.

    ![Network selection](./media/site-recovery-vmware-to-azure/combined-wiz9.png)


10. In **Summary**, review the information and click **Install**. When installation finishes a passphrase is generated. You need it when you enable replication so copy it and keep it in a secure location. After registration finishes the server is displayed in **Servers** in the vault.

   ![Summary](./media/site-recovery-vmware-to-azure/combined-wiz10.png)




### Add the account for automatic discovery

 Add the account you created for auto-discovery of VMware VMs.

1. On the configuration server, run **CSPSConfigtool.exe**. It's available as a shortcut on the desktop, or in the **[INSTALL LOCATION]\home\svsystems\bin** folder.
2. Click **Manage Accounts** > **Add Account**.

    ![Add account](./media/site-recovery-vmware-to-azure/credentials1.png)
3. In **Account Details**, add the account that will be used for automatic discovery. It can take 15 minutes or more for the account name to appear in the portal. To update immediately, click **Configuration Servers** > server name > **Refresh Server**.

    ![Details](./media/site-recovery-vmware-to-azure/credentials2.png)

### Connect to VMware servers

Connect to vSphere ESXi hosts or vCenter servers, to discover VMware VMs.

- If you add the vCenter server or vSphere hosts with an account without administrator privileges on the server, the account needs these privileges enabled:
    - Datacenter, Datastore, Folder, Host, Network, Resource, Virtual machine, vSphere Distributed Switch.
    - The vCenter server needs the Storage views privilege.
- When you add VMware servers, it can take 15 minutes or longer for them to appear in the portal.


1. Check that the configuration server has network access to the vSphere hosts and vCenter servers.
2. Click **Prepare infrastructure** > **Source**. In **Prepare source**, select the configuration server. Click **+vCenter** to add a vSphere host or vCenter server.
3. In **Add vCenter**, specify a friendly name for the server, and the IP address or FQDN. Leave the port 443 setting, unless VMware servers are configured to listen for requests on a different port. Then, select the account you created for automatic discovery, and click **OK**.

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

Site Recovery connects to VMware servers using the specified settings, and discovers VMs.

## Set up the target


Before you set up the target environment, check you have an [Azure storage account and network](#set-up-azure)

1. Click **Prepare infrastructure** > **Target**, and select the Azure subscription you want to use.
2. Specify whether your target deployment model is Resource Manager-based, or classic.
3. Site Recovery checks that you have one or more compatible Azure storage accounts and networks.

   ![Target](./media/site-recovery-vmware-to-azure/gs-target.png)
4. If you haven't created a storage account or network, click **+Storage account** or **+Network**, to create a Resource Manager account or network inline.

## Set up replication settings

1. To create a new replication policy, click **Site Recovery infrastructure** > **Replication Policies** > **+Replication Policy**.
2. In **Create replication policy**, specify a policy name.
3. In **RPO threshold**, specify the RPO limit. This value specifies how often data recovery points are created. An alert is generated if continuous replication exceeds this limit.
4. In **Recovery point retention**, specify (in hours) how long the retention window is for each recovery point. Replicated VMs can be recovered to any point in a window. Up to 24 hours retention is supported for machines replicated to premium storage, and 72 hours for standard storage.
5. In **App-consistent snapshot frequency**, specify how often (in minutes) recovery points containing application-consistent snapshots will be created. Click **OK** to create the policy.

    ![Replication policy](./media/site-recovery-vmware-to-azure/gs-replication2.png)
8. When you create a new policy it's automatically associated with the configuration server. By default, a matching policy is automatically created for failback. For example if the replication policy is **rep-policy** then the failback policy will be **rep-policy-failback**. This policy isn't used until you initiate a failback from Azure.  


## Plan capacity

1. Now that you have your basic infrastructure set up you can think about capacity planning, and figure out whether you need additional resources. [Learn more](site-recovery-plan-capacity-vmware.md).
2. When you’re done with capacity planning, select **Yes** in **Have you completed capacity planning?**

   ![Capacity planning](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## Prepare VMs for replication

All machines that you want to replicate must have the Mobility service installed. You can install the Mobility service in a number of ways:

1. Install with a push installation from the process server. You need to prepare VMs to use this method.
2. Install using deployment tools such as System Center Configuration Manager, or Azure automation DSC.
3.  Install manually.

[Learn more](site-recovery-vmware-to-azure-install-mob-svc.md)


## Enable replication

Before you start:

- When you add or modify VMs, it can take up to 15 minutes or longer for changes to take effect, and for them to appear in the portal.
- You can check the last discovered time for VMs in **Configuration Servers** > **Last Contact At**.
- To add VMs without waiting for the scheduled discovery, highlight the configuration server (don’t click it), and click **Refresh**.
* If a VM is prepared for push installation, the process server automatically installs the Mobility service when you enable replication.


### Exclude disks from replication

By default all disks on a machine are replicated. You can exclude disks from replication. For example you might not want to replicate disks with temporary data, or data that's refreshed each time a machine or application restarts (for example pagefile.sys or SQL Server tempdb).

### Replicate VMs

1. Click **Step 2: Replicate application** > **Source**.
2. In **Source**, select the configuration server.
3. In **Machine type**, select **Virtual Machines**.
4. In **vCenter/vSphere Hypervisor**, select the vCenter server that manages the vSphere host, or select the host.
5. Select the process server. If you haven't created any additional process servers this will be the configuration server. Then click **OK**.

    ![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. In **Target**, select the subscription and the resource group in which you want to create the failed over VMs. Choose the deployment model that you want to use in Azure (classic or resource management), for the failed over VMs.


7. Select the Azure storage account you want to use for replicating data. If you don't want to use an account you've already set up, you can create a new one.

8. Select the Azure network and subnet to which Azure VMs will connect, when they're created after failover. Select **Configure now for selected machines**, to apply the network setting to all machines you select for protection. Select **Configure later** to select the Azure network per machine. If you don't want to use an existing network, you can create one.

    ![Enable replication](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. In **Virtual Machines** > **Select virtual machines**, click and select each machine you want to replicate. You can only select machines for which replication can be enabled. Then click **OK**.

    ![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. In **Properties** > **Configure properties**, select the account that will be used by the process server to automatically install the Mobility service on the machine.
11. By default all disks are replicated. Click **All Disks** and clear any disks you don't want to replicate. Then click **OK**. You can set additional VM properties later.

    ![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication6.png)
11. In **Replication settings** > **Configure replication settings**, verify that the correct replication policy is selected. If you modify a policy, changes will be applied to replicating machine, and to new machines.
12. Enable **Multi-VM consistency** if you want to gather machines into a replication group, and specify a name for the group. Then click **OK**. Note that:

    * Machines in replication groups replicate together, and have shared crash-consistent and app-consistent recovery points when they fail over.
    * We recommend that you gather VMs and physical servers together so that they mirror your workloads. Enabling multi-VM consistency can impact workload performance, and should only be used if machines are running the same workload and you need consistency.

    ![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication7.png)
13. Click **Enable Replication**. You can track progress of the **Enable Protection** job in **Settings** > **Jobs** > **Site Recovery Jobs**. After the **Finalize Protection** job runs the machine is ready for failover.

After you enable replication, the Mobility service will be installed if you set up push installation. After the Mobility service is push installed on a VM, a protection job will start and fail. After the failure you need to manually restart each machine. Then the protection job begins again, and initial replication occurs.


### View and manage VM properties

We recommend that you verify the VM properties, and make any changes you need to.

1. Click **Replicated items** >, and select the machine. The **Essentials** blade shows information about machines settings and status.
2. In **Properties**, you can view replication and failover information for the VM.
3. In **Compute and Network** > **Compute properties**, you can specify the Azure VM name and target size. Modify the name to comply with [Azure requirements](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) if you need to.
4. Modify settings for the target network, subnet, and IP address that will be assigned to the Azure VM:

   - You can set the target IP address.

    - If you don't provide an address, the failed over machine will use DHCP.
    - If you set an address that isn't available at failover, failover won't work.
    - The same target IP address can be used for test failover, if the address is available in the test failover network.

   - The number of network adapters is dictated by the size you specify for the target virtual machine:

     - If the number of network adapters on the source machine is the same as, or less than, the number of adapters allowed for the target machine size, then the target will have the same number of adapters as the source.
     - If the number of adapters for the source virtual machine exceeds the number allowed for the target size, then the target size maximum will be used.
     - For example, if a source machine has two network adapters and the target machine size supports four, the target machine will have two adapters. If the source machine has two adapters but the supported target size only supports one then the target machine will have only one adapter.     
   - If the virtual machine has multiple network adapters they will all connect to the same network.
   - If the virtual machine has multiple network adapters then the first one shown in the list becomes the *Default* network adapter in the Azure virtual machine.
5. In **Disks**, you can see the VM operating system, and the data disks that will be replicated.

## Run a test failover

After you've set everything up, run a test failover to make sure everything's working as expected.

1. Select **Recovery Plans** > *recoveryplan_name*, > **Test Failover**.
2. Select the recovery point to which you want to failover. You can use one of the following options:
	- **Latest processed**: This options fails over all VMs to the latest recovery point that was processed by the Site Recovery service.
        - If you fail over a VM, the time stamp of the latest processed recovery point is also shown.
        - If you fail over a recovery plan, you can go to individual VM > **Latest Recovery Points** to view.
        - The latest recovery point provides a low RTO (Recovery Time Objective) option.
	- **Latest app-consistent**: This options fails over all VMs to the latest application consistent recovery point.
	- **Latest**: This option first processes all the data that has been sent to Site Recovery service, to create a recovery point for each VM, before failing them over to it. This option provides the lowest RPO (Recovery Point Objective). The VM created after failover will have all the data replicated to Site Recovery when the failover was triggered.
	- **Custom**: For failover of a VM you can use this option to fail over to a particular recovery point.
3. Select an **Azure virtual network**. This is the network in which the test VMs are created.
    - Site Recovery attempts to create test VM in a subnet of same name, and using the same IP addressed specified in the **Compute and Network** settings of the VM.
    - If a subnet of same name isn't available in the Azure virtual network used for test failover, then the test VM is created in the first subnet, in alphabetical order.
    - If the same IP address isn't available in the subnet, then the VM gets another IP address from the subnet.
4. In  **Encryption Key**, select the certificate that was issued when you enabled data encryption during Provider installation. If you didn't enable encryption, ignore this step.
5. Track failover progress on the **Jobs** tab. You should be able to see the test replica machine in the Azure portal.
6. After failover is complete, click **Cleanup test failover**, in the recovery plan. In **Notes**, record and save any observations associated with the test failover. Cleanup deletes the VMs that were created during test failover.

[Learn more]((site-recovery-test-failover-to-azure.md) about preparing for test failover, and connecting to replica Azure VMs.


## VMware account permissions

Site Recovery needs access to VMware for the process server to automatically discover VMs, and for failover and failback of VMs.

- **Migrate**: If you only want to migrate VMware VMs to Azure, without ever failing them back, you can use a VMware account with a read-only role. Such a role can run failover, but can't shut down protected source machines. This isn't necessary for migration.
- **Replicate/Recover**: If you want to deploy full replication (replicate, failover, failback) the account must be able to run operations such as creating and removing disks, powering on VMs etc.
- **Automatic discovery**: At least a read-only account is required.


**Task** | **Required account/role** | **Permissions** | **Details**
--- | --- | --- | ---
**Process server automatically discovers VMware VMs** | You need at least a read-only user | Data Center object –> Propagate to Child Object, role=Read-only | User assigned at datacenter level, and has access to all the objects in the datacenter.<br/><br/> To restrict access, assign the **No access** role with the **Propagate to child** object, to the child objects (vSphere hosts, datastores, VMs and networks).
**Failover** | You need at least a read-only user | Data Center object –> Propagate to Child Object, role=Read-only | User assigned at datacenter level, and has access to all the objects in the datacenter.<br/><br/> To restrict access, assign the **No access** role with the **Propagate to child** object to the child objects (vSphere hosts, datastores, VMs and networks).<br/><br/> Useful for migration purposes, but not full replication, failover, failback.
**Failover and failback** | We suggest you create a role (Azure_Site_Recovery) with the required permissions, and then assign the role to a VMware user or group | Data Center object –> Propagate to Child Object, role=Azure_Site_Recovery<br/><br/> Datastore -> Allocate space, browse datastore, low-level file operations, remove file, update virtual machine files<br/><br/> Network -> Network assign<br/><br/> Resource -> Assign VM to resource pool, migrate powered off VM, migrate powered on VM<br/><br/> Tasks -> Create task, update task<br/><br/> Virtual machine -> Configuration<br/><br/> Virtual machine -> Interact -> answer question, device connection, configure CD media, configure floppy media, power off, power on, VMware tools install<br/><br/> Virtual machine -> Inventory -> Create, register, unregister<br/><br/> Virtual machine -> Provisioning -> Allow virtual machine download, allow virtual machine files upload<br/><br/> Virtual machine -> Snapshots -> Remove snapshots | User assigned at datacenter level, and has access to all the objects in the datacenter.<br/><br/> To restrict access, assign the **No access** role with the **Propagate to child** object, to the child objects (vSphere hosts, datastores, VMs and networks).


## Next steps

After you get replication up and running, when an outage occurs you fail over to Azure, and Azure VMs are created from the replicated data. You can then access workloads and apps in Azure, until you fail back to your primary location when it returns to normal operations.

- [Learn more](site-recovery-failover.md) about different types of failovers, and how to run them.
- If you're migrating machines rather than replicating and failing back, [read more](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- [Read about failback](site-recovery-failback-azure-to-vmware.md), to fail back and replicate Azure VMs from Azure, back to the primary on-premises site.

## Third-party software notices and information
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.
