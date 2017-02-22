---
title: Azure CLI Samples | Microsoft Docs
description: Azure CLI Samples
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management

ms.assetid:
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/07/2017
ms.author: nepeters

---
# Azure CLI Samples

The following table includes links to bash scripts built using the Azure CLI.


| Sample | Description  |
|---|---|
|**Create virtual machines**||
| [Quick Create a VM with the Azure CLI](./scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Creates a Linux virtual machine with minimal configuration. |
| [Create a VM with the Azure CLI](./scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Creates a resource group, virtual machine, and all related resources.|
|**Highly available virtual machines**||
| [Load balance traffic between highly avaliable VMs](./scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Creates several Linux virtual machines, all related resources, and a network load balancer to balance incoming traffic across the VM set. |
|**Monitor virtual machines**||
| [Monitor VMs with Operations Management Suite](./scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Creates a virtual machine, all related resources, installs the Operations Management Suite agent, and enrolls the VM in an OMS Workspace.  |
|**Secure virtual machines**||
| [Secure network traffic between VMs](./scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Creates two virtual machines, all related resources, and an internal and external network security groups (NSG). |
|**Docker in Azure virtual machines**||
| [Create a VM with Docker enabled](./scripts/virtual-machines-linux-cli-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Creates a virtual machine, configures this VM as a Docker host, and runs an NGINX container. |
|**Troubleshoot virtual machines**||
| [Troubleshoot OS disk issues](./scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Mounts the operating system disk from one VM as the data disk of a second VM. This can be useful when troubleshooting or recovering data. |
