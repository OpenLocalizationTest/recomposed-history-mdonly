<properties
   pageTitle="Deploy your on-premises StorSimple device"
   description="Steps and best practices for deploying the StorSimple device and service."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/06/2015"
   ms.author="alkohli" />

# Deploy your on-premises StorSimple device

[AZURE.INCLUDE [storsimple-version-selector](../../includes/storsimple-version-selector.md)]

## Overview

Welcome to Microsoft Azure StorSimple device deployment.

These deployment tutorials apply to StorSimple 8000 Series Release Version, StorSimple 8000 Series Update 0.1, StorSimple 8000 Series Update 0.2, and StorSimple 8000 Series Update 0.3.

This series of tutorials describes how to configure your StorSimple device, and includes a pre-installation checklist, configuration prerequisites, and detailed configuration steps.

> [AZURE.NOTE] The StorSimple deployment information published on the Microsoft Azure website applies to StorSimple 8000 series devices only. For complete information about the 7000 series devices, go to: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). For 7000 series deployment information, see the [StorSimple System Quick Start Guide](http://onlinehelp.storsimple.com/111_Appliance/).

The information in these tutorials assumes that you have reviewed the safety precautions, and unpacked, racked, and cabled your StorSimple device. If you still need to perform those tasks, start with reviewing the [Safety precautions](storsimple-safety.md).

Depending on your device model, you can then unpack, rackmount and cable by following the instructions in:

- [Unpack, rack mount, and cable your 8100](storsimple-8100-hardware-installation.md)
- [Unpack, rack mount, and cable your 8600](storsimple-8600-hardware-installation.md)

You will need administrator privileges to complete the setup and configuration process. We recommend that you review the pre-installation checklist before you begin. The deployment and configuration process can take some time to complete.

## Deployment steps

Perform these required steps to configure your StorSimple device and connect it to your StorSimple Manager service. In addition to the required steps, there are optional steps and procedure that you will need when you deploy your device. The step-by-step deployment instructions indicate when you should perform each of these optional steps.


| Step                                                                                   | Description                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **PREREQUISITES**                                                                      | These need to be completed in preparation for the upcoming deployment.                                                                                        |
| Pre-installation checklist                                                             | Use this checklist to gather and record information prior to and during the deployment.                                                                       |
| Deployment prerequisites                                                               | These  validate the environment is   ready for deployment                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **DEPLOYMENT STEPS**                                                                   | These steps are required to deploy your StorSimple device in production.                                                                                      |
| Step 1:   Create a new service                                                         | Set up cloud management and storage for your   StorSimple device. Skip this step if you have an existing service for other StorSimple devices.                |
| Step 2: Get the service registration key                                               | Use this key to register & connect your StorSimple device with the management service                                                                         |
| Step 3: Configure and register the device through Windows PowerShell for StorSimple    | Connect the device to your network and register it with Azure to complete   the setup using the management service                                            |
| Step 4: Complete minimum device setup</br>Optional: Update your StorSimple device      | Use the management service to complete the device setup and enable it to provide storage                                                                      |
| Step 5: Create a volume container                                                      | Create a container to provision volumes. A volume container has storage   account, bandwidth, and encryption settings for all the volumes contained in it.    |
| Step 6: Create a volume                                                                | Provision storage volume(s) on the StorSimple device for your servers.                                                                                        |
| Step 7: Mount, initialize, and format a volume</br>Optional: Configure MPIO            | Connect your servers to the iSCSI storage provided by the device                                                                                                                                                              |
| Step 8: Take a backup                                                                  | Setup your backup policy to protect your data                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **OTHER PROCEDURES**                                                                   | You may need to refer to these procedures as you deploy your solution.                                                                                     	 |
| Configure a new storage account for the   service                                      |                                                                                                                                                               |
| Use PuTTY to connect to the device serial   console                                    |                                                                                                                                                               |
| Get the IQN of a Windows Server host                                                   |                                                                                                                                                               |
| Create a manual backup                                                                 |                                                                                                                                                               


## Deployment configuration checklist

The following deployment configuration checklist describes the information that you need to collect before and as you configure the software on your StorSimple device. Preparing some of this information ahead of time will help streamline the process of deploying the StorSimple device in your environment. Use this checklist to also note down the configuration details as you deploy your device.

| Stage                                  | Parameter                                         | Details                                                                                                                                                                | Values |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| Cable your device                      | Serial access                                     | Initial device configuration                                                                  | Yes/No |
|   |   |  |  |
| Configure and register device          | Data 0 network settings                           | Data 0 IP Address:</br>Subnet mask:</br>Gateway:</br>Primary DNS server:</br>Primary NTP server:</br>Web proxy server IP/FQDN (optional):</br>Web proxy port:|        |
|                                        | Device administrator password                   	 | Password must be between 8 and 15 characters containing lowercase, uppercase, numeric and special characters. |        |
|                                        | StorSimple Snapshot Manager password              | Password must be 14 or 15 characters containing lowercase, uppercase, numeric and special characters.|        |
|                                        | Service Registration Key                          | This key is generated from the Azure Management Portal.    |        |
|                                        | Service Data Encryption Key                       | This key is created when the device is registered with the management service via the Windows PowerShell for StorSimple. Copy this key and save it in a safe location.|  |
|   |   |  |  |
| Complete minimum device setup          | Friendly name for your device                     | This is a descriptive name for the device. |        |
|                                        | Timezone                                          | Your device will use this time zone for all scheduled operations.  |        |
|                                        | Secondary DNS server                              | This is a required configuration.                                  |        |
|                                        | Network interface: Data 0 Controller Fixed IPs    							      | These IP’s should be routable to internet</br>Controller 0 Fixed IP address</br>Controller 1 Fixed address|		                                                                                                                                                                        
|   |   |  |  |
| Additional network interface settings  | Network interface: Data 1</br>If iSCSI enabled, do not configure the Gateway.      | Purpose: Cloud/iSCSI/Not used</br>IP address:</br>Subnet Mask:</br>Gateway:|                                                                                                                                                                                                                                                                                               
|                                        | Network interface: Data 2</br>If iSCSI enabled, do not configure the Gateway.      | Purpose: Cloud/iSCSI/Not used</br>IP address:</br>Subnet Mask:</br>Gateway:|                                                                                                                                      
|                                        | Network interface: Data 3</br>If iSCSI enabled, do not configure the Gateway.      | Purpose: Cloud/iSCSI/Not used</br>IP address:</br>Subnet Mask:</br>Gateway:|                                                                                                                                         
|                                        | Network interface: Data 4</br>If iSCSI enabled, do not configure the Gateway.      | Purpose: Cloud/iSCSI/Not used</br>IP address:</br>Subnet Mask:</br>Gateway:|                                                                                                                                          
|                                        | Network interface: Data 5</br>If iSCSI enabled, do not configure the Gateway.      | Purpose: Cloud/iSCSI/Not used</br>IP address:</br>Subnet Mask:</br>Gateway:|                                                                                                                                         
|   |   |  |  |
| Volume Containers                      | Volume Container Name:                            | Name for the container                                                                                                                                                 |        |
|                                        | Azure storage account name                        | Storage Account name & access key to associate with this volume container                                                                                              |        |
|                                        | Cloud Storage Encryption Key:                     | Encryption Key for storage in each container                                                                                                                           |        |
|   |   |  |  |
| Create a volume                        | Details for each volume                           | Volume Name:                                                                                                                                                           |        |
|                                        |                                                   | Size:                                                                                                                                                                  |        |
|                                        |                                                   | Usage Type:                                                                                                                                                            |        |
|                                        |                                                   | ACR Name:                                                                                                                                                              |        |
|                                        |                                                   | Default Backup policy:                                                                                                                                                 |        |
|   |   |  |  |
| Mount, initialize, and format a volume | Details for each Server connecting to the storage | Windows Server Name:                                                                                                                                                   |        |
|                                        |                                                   | Windows Server IQN:                                                                                                                                                    |        |
|                                        |                                                   | Windows Volume Name:                                                                                                                                                   |        |
|                                        |                                                   | NTFS Mount point or Drive letter:                                                                                                                                      |        |

## Deployment prerequisites

The following sections explain the configuration prerequisites for your StorSimple Manager service, your StorSimple device, and network in your datacenter.

### For the StorSimple Manager service

Before you begin, make sure that:

- You have your Microsoft account with access credentials.

- You have your Microsoft Azure storage account with access credentials.

- Your Microsoft Azure subscription is enabled for the StorSimple Manager service. Your subscription should be purchased through the [Enterprise Agreement](http://azure.microsoft.com/pricing/enterprise-agreement/).

- You have access to terminal emulation software such as PuTTY.

### For the device in the datacenter

Before configuring the device, make sure that:

- Your device is fully unpacked, mounted on a rack and fully cabled for power, network, and serial access as described in:
	-  [Unpack your 8100 device](storsimple-8100-hardware-installation.md), or
	-  [Unpack your 8600 device](storsimple-8600-hardware-installation.md).


## For the network in the datacenter

Before you begin, make sure that:

- The ports in your datacenter firewall are opened to allow for iSCSI and cloud traffic as described in [Networking requirements for your StorSimple device]().
- The device in your datacenter can connect to outside network. Run the following [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855) cmdlets to validate the connectivity to the outside network on a computer inside the datacenter network. Perform this validation in a network that has connectivity to Azure and where you will deploy your StorSimple device.  

| For this parameter…       | To check the validity…                                                                                                                                                                                | Run these commands/cmdlets.                                                                                                                                                             |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IP</br>Subnet</br>Gateway | Is this a valid IPv4 or IPv6 address?</br>Is this a valid subnet?</br>Is this a valid gateway?                                                                          | `ping ip`</br>`arp -a`                                                                                                                                                                      |
|                           | Is this a duplicate IP on network?                                                                                                                                 | The `ping` and `arp` commands should fail indicating that there is no device in the datacenter network that is using this IP.                                                           |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| DNS                       | Is this a valid DNS and can resolve Azure URLs?                                                                                                                    | `Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>An alternative command that can be used is:</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com`      |
|                           | Check if port 53 is open. This is applicable only if you are using an external DNS for your device. Internal DNS should automatically resolve the external URL’s.  | `Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[More information on this cmdlet]()|
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| NTP                       | We trigger a time sync as soon as NTP server is input. Check UDP port 123 is open when you input `time.windows.com` or public time servers). | [Download and use this script]().                                                                                                                                                           |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| Proxy (optional)          | Is this a valid proxy URI and port?                                                                                                                                | `Invoke-WebRequest   –UseBasicParsing –URI www.bing.com `                                                                                                                                 |
|                           | Is the authentication mode correct?                                                                                                                              | `wget http://bing.com % {$_.StatusCode}`</br>If 200 is returned, it indicates that authentication mode is correct.                                                                                                      |
|                           | Is traffic routable through proxy?                                                                                                                                 | Run the DNS validation, NTP check or HTTP check once after configuring proxy on your device. This will give a clear picture if traffic is getting blocked at proxy or elsewhere.                                                                                   								     |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| Registration              | Check if outbound TCP ports 443, 80, 9354 are open.                                                                                                                |  `Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[More information for Test-NetConnection cmdlet]()                                                                           |

## Step-by-step deployment

Use the following step-by-step instructions to deploy your StorSimple device in the datacenter.

## Step 1: Create a new service

A StorSimple Manager service can manage multiple StorSimple devices. For the deployment of your first StorSimple device, you will need to create a new StorSimple Manager service.

> [AZURE.IMPORTANT] Skip this step if you have an existing StorSimple Manager service and you intend to deploy your StorSimple device with that service.

Perform the following steps to create a new instance of the StorSimple Manager service.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] If you did not enable the automatic creation of a storage account with your service, you will need to create at least one storage account after you have successfully created a service. This storage account will be used when you create a volume container.
>
> * If you did not create a storage account automatically, go to [Configure a new storage account for the service](#Configure-a-new-storage-account-for-the-service) for detailed instructions.
> * If you enabled the automatic creation of a storage account, go to [Step 2: Get the service registration key](#step-2:-get-the-service-registration-key).

## Step 2: Get the service registration key

After the StorSimple Manager service is up and running, you will need to get the service registration key. This key is used to register and connect your StorSimple device with the service.

Perform the following steps in the Management Portal.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## Step 3: Configure and register the device through Windows PowerShell for StorSimple

> [AZURE.IMPORTANT] Prior to performing this configuration, unplug all the network interfaces other than DATA 0 on both (active and passive) the controllers.

Use Windows PowerShell for StorSimple to complete the initial setup of your StorSimple device as explained in the following procedure. You will need to use terminal emulation software to complete this step. For more information, see [Use PuTTY to connect to the device serial console](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## Step 4: Complete minimum device setup

For the minimum device configuration of your StorSimple device, you are required to:

- Set up the secondary DNS server.
- Enable iSCSI on at least one network interface.
- Assign fixed IP addresses to both the controllers.

Perform the following steps in the Management Portal to complete the minimum device setup.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

After the device configuration is complete, you must scan for updates and if available, install updates. The updates may take several hours to complete. Follow the instructions in [Scan for and apply updates](#scan-for-and-apply-updates).


## Step 5: Create a volume container

A volume container has storage account, bandwidth, and encryption settings for all the volumes contained in it. You will need to create a volume container before you can start provisioning volumes on your StorSimple device.

Perform the following steps in the Management Portal to create a volume container.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Step 6: Create a volume

After you create a volume container, you can provision a storage volume on the StorSimple device for your servers. Perform the following steps in the Management Portal to create a volume.

> [AZURE.IMPORTANT] StorSimple Manager can create only thinly provisioned volumes.  You cannot create fully or partially provisioned volumes.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## Step 7: Mount, initialize, and format a volume

> [AZURE.IMPORTANT]
 
> - For the high availability of your StorSimple solution, we recommend that you configure MPIO on your Windows Server host (optional) prior to configuring iSCSI on your Windows Server host. MPIO configuration on host servers will ensure that the servers can tolerate a link, network, or interface failure.

> - For MPIO and iSCSI installation and configuration instructions, go to [Configure MPIO for your StorSimple device](storsimple-configure-mpio-windows-server.md). These will also include the steps to mount, initialize and format StorSimple volumes.

If you decide not to configure MPIO, perform the following steps to mount, initialize, and format your StorSimple volumes.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Step 8: Take a backup

Backups provide point-in-time protection of volumes and improve recoverability while minimizing restore times. You can take two types of backup on your StorSimple device: local snapshots and cloud snapshots. Each of these backup types can be **Scheduled** or **Manual**.

Perform the following steps in the Management Portal to create a scheduled backup.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

You can take a manual backup at any time. For procedures, go to [Create a manual backup](#Create-a-manual-backup).

## Configure a new storage account for the service

This is an optional step that you need to perform only if you did not enable the automatic creation of a storage account with your service. A Microsoft Azure storage account is required to create a StorSimple volume container.

If you need to create an Azure storage account in a different region, see [About Azure Storage Accounts](../storage/storage-create-storage-account.md) for step-by-step instructions.

Perform the following steps in the Management Portal, on the **StorSimple Manager service** page.

[AZURE.INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]


## Use PuTTY to connect to the device serial console

To connect to Windows PowerShell for StorSimple, you need to use terminal emulation software such as PuTTY. You can use PuTTY when you access the device directly through the serial console or by opening a telnet session from a remote computer.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## Get the IQN of a Windows Server host

Perform the following steps to get the iSCSI Qualified Name (IQN) of a Windows host that is running Windows Server® 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## Create a manual backup

Perform the following steps in the Management Portal to create an on-demand manual backup for a single volume on your StorSimple device.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## Next steps

Configure a [virtual device](storsimple-virtual-device.md).

Use the [StorSimple Manager service](https://msdn.microsoft.com/library/azure/dn772396.aspx) to manage your StorSimple device.
 