---
title: Understand shared IP addresses in Azure DevTest Labs | Microsoft Docs
description: Learn how Azure DevTest Labs uses shared IP addresses to minimize the public IP addresses required to access your lab VMs.
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: ''

ms.assetid: 66cfd8bb-2886-42e8-8285-87a22a5bde9e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: casoper
---

# Understand shared IP addresses in Azure DevTest Labs

Azure DevTest Labs uses shared IP addresses to minimize the number of public IP addresses required to access your individual lab VMs.  This article describes how shared IPs work and their related configuration options.

## Shared IP setting

When you create a new DevTest Lab, the lab will reside in a subnet of a virtual network.  By default, this subnet is created with **Enable shared public IP** set to *Yes*.  This configuration creates one public IP address for the entire subnet.

![New lab subnet](media/devtest-lab-shared-ip/lab-subnet.png)

Any VMs created in a lab with shared IP enabled will default to shared IP.  When creating the VM, this can be observed in the **Advanced settings** blade under **IP address configuration**.

![New VM](media/devtest-lab-shared-ip/new-vm.png)

Whenever a VM is added to the subnet with shared IP enabled for that machine, a single TCP port is assigned on that IP address to forward to the Remote Desktop port on the VM.  

## Using the shared IP

Users can connect to Remote Desktop on the VM in an RDP client by using the IP address or fully-qualified domain name, followed by a colon, followed by the port number.  For example, in the image below, the RDP address to connect to the VM is `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.  Users in the Azure Portal can click the **Connect** button in the portal to download a pre-configured RDP file.

![VM example](media/devtest-lab-shared-ip/vm-info.png)

## Next Steps

* [Define lab policies in Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configure a virtual network in Azure DevTest Labs](devtest-lab-configure-vnet.md)





