---
title: Azure Site Recovery troubleshooting from Azure to Azure | Microsoft Docs
description: Troubleshooting errors when replicating Azure virtual machines
services: site-recovery
documentationcenter: ''
author: sujayt
manager: rochakm
editor: ''

ms.assetid:
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/13/2017
ms.author: sujayt

---
# Troubleshoot Azure VM replication issues

This article details the common issues in Azure Site Recovery (ASR) when replicating and recovering Azure virtual machines from one region to another region and how to troubleshoot them. Refer to [support matrix for replicating Azure VMs](site-recovery-support-matrix-to-azure.md) for more details about supported configurations

## Azure resource quota issues (Error code - 150097)
Your subscription should be enabled to create Azure VMs in the target region which you plan to use as DR region. Also, your subscription should have sufficient quota enabled to create VMs of specific size. By default, ASR picks the same size as source VM for the target VM. If the matching size is not available, the closest possible size is auto picked. If there is no matching size that supports source VM configuration, the below error message will be seen.

**Error code** | **Possible causes** | **Recommendations**
--- | --- | ---
150097<br></br>***Message -***  Replication couldn't be enabled for the virtual machine 'VmName'. | 1. Your subscription 'Subscription Id' might not have been enabled to create any VMs in 'Target region' location.</br>2. Your subscription '%SubscriptionId;' might not have been enabled or do not have sufficient quota to create specific VM sizes in 'Target region' location.</br>3. A suitable target VM size that matches source VM NIC count ('2') is not found for subscription 'Subscription Id' in location 'Target region'.| Contact support to enable VM creation for the required VM sizes in target location for your subscription. Once enabled, retry the failed operation.

#### How to fix it?
You can contact Azure support to enable your subscription to create VMs of required sizes in the target location.

If there is a capacity constraint and you cannot get your subscription enabled in the target location, you can disable replication and enable replication to a different location where your subscription has sufficient quota to create VMs of required sizes.

## Trusted Root certificates (Error code - 151066)

Your 'enable replication' job could fail if the all the latest trusted root certificates are not present on the VM. Without the certificates, the authentication and authorization of Site Recovery service calls from the VM would fail. You would see the below error details in the failed 'Enable replication' Site Recovery job.

**Error code** | **Possible causes** | **Recommendations**
--- | --- | ---
151066<br></br>***Message -***  Site recovery configuration failed. | The required trusted root certificates used for authorization and authentication are not present on the machine. | 1. For a VM running Windows OS, ensure the trusted root certificates are present on the machine following the guidance in the below article.<br></br>https://technet.microsoft.com/library/dn265983.aspx <br></br>2. For a VM running Linux OS, follow the guidance for trusted root certificates published by the Linux OS version distributor."

#### How to fix it?
***Windows***

Ensure you install all the latest Windows updates on the VM. This will ensure all the trusted root certificates are present on the machine. If you are in a disconnected environment, ensure you follow the standard Windows update process in your organization to get the certificates. Due to security reasons, the calls to Site Recovery service will fail in case the required certificates are not present on the VM.

You can follow the typical Windows update management or certificate update management process in your organization to get all the latest root certificates and updated certificate revocation list on the VMs.

To verify that the issue is resolved, you can try opening login.microsoftonline.com from a browser in your VM.

***Linux***

Follow the guidance provided by your Linux distributor to get the latest trusted root certificates and the latest certificate revocation list on the VM.

Follow the below specific steps for **SuSE Linux** as SuSE Linux uses symlinks to maintain certificate list.

1.	Login as root user.

2.	Run the below command.

      ``# cd /etc/ssl/certs``

3.	Check if the Symantec root CA cert is present or not.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``
4.	If the file is not found, run the below commands.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

      ``# c_rehash``

5.	Create a symlink with b204d74a.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

      ``# ln -s  VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

6.	Check if the below command has the following output. If not, you have to create a symlink.

      ``# ls -l | grep Baltimore
      -rw-r--r-- 1 root root   1303 Apr  7  2016 Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 04:47 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 05:01 653b494a.0 -> Baltimore_CyberTrust_Root.pem``

7. If symlink 653b494a.0 is not present, use the below command to create symlink.

      ``# ln -s Baltimore_CyberTrust_Root.pem 653b494a.0``


## Outbound connectivity for Azure Site Recovery URLs or IP ranges (Error code - 151037 or 151072)

For Site recovery replication to work, outbound connectivity to specific URLs or IP-ranges is required from the VM. If your VM is behind a firewall or using NSG rules to control outbound connectivity, you might see the below error.

**Error code** | **Possible causes** | **Recommendations**
--- | --- | ---
151037<br></br>***Message -***  Failed to register Azure virtual machine with Site recovery. | 1. You are using NSG to control outbound access on the VM and the required IP ranges are not whitelisted for outbound access.</br>2. You are using third party firewall tools and the required IP ranges/URLs are not whitelisted.</br>3. The required trusted root certificates used for authorization and authentication are not present on the machine. | 1. If you are using NSG to control outbound access on the VM, ensure you whitelist the Azure datacenter IP ranges of the location in which the VM is running and the target location. For example, if your VM is running in 'East US' and target region is 'Central US', you need to whitelist all IP ranges of 'East US' and 'Central US'. You can check the latest Azure datacenter IP ranges in this link - https://www.microsoft.com/download/details.aspx?id=41653.</br>2. If you are using third party firewall tools, ensure you whitelist the URLs mentioned in the documentation or whitelist the Azure datacenter IP ranges of the location in which the VM is running and the target location.</br>3. For a VM running Windows OS, ensure the trusted root certificates are present on the machine following the guidance in the article: https://technet.microsoft.com/library/dn265983.aspx.</br>For a VM running Linux OS, follow the guidance for trusted root certificates published by the Linux OS version distributor.
151072<br></br>***Message -*** Site recovery configuration failed. | Connection cannot be established to Azure Site Recovery service endpoints. | 1. If you are using firewall proxy to control outbound network connectivity on the VM, ensure the prerequisite URLs or datacenter IP ranges are whitelisted. Refer to https://aka.ms/a2a-firewall-proxy-guidance </br>2. If you are using Azure Network security group (NSG) rules to control outbound network connectivity on the VM, ensure the prerequisite datacenter IP ranges are whitelisted. Refer to https://aka.ms/a2a-nsg-guidance

#### How to fix it?
Ensure you follow the [networking guidance document](site-recovery-azure-to-azure-networking-guidance.md) to whitelist [the required URLs](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-urls) and/or the [required IP-ranges](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-ip-ranges).


## Next steps
- [Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
