---
title: Availability sets tutorial for Linux VMs in Azure | Microsoft Docs
description: Learn about the Availability Sets for Linux VMs in Azure.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: cynthn

---

# How to use availability sets

In this tutorial, you learn about increasing the availability of your virtual machines (VMs) by putting them into a logical grouping called an availability set. When you create VMs within an availability set, the Azure platform distributes the VMs across the underlying infrastructure. If there is planned maintenance on the Azure platform or an underlying hardware fault, the use of availability sets ensures that at least one VM remains running.

The steps in this tutorial can be completed using the latest [Azure CLI 2.0](/cli/azure/install-azure-cli).

## Availability set overview

Each hardware cluster in a location is divided in to multiple update domains and fault domains. 

- **Update domain** - indicate groups of virtual machines and underlying physical hardware that can be rebooted at the same time. You can set the number of fault domains at any number between 1 - 20. 
- **Fault domain** - VMs in the same fault domain share a common power source and network switch. The number of available fault domains varies between two or three fault domains per region.

Based on the settings for the availability set, Azure automatically distributes VMs within an availability set across domains to maintain availability and fault tolerance. Depending on the size of your application and the number of VMs within an availability set, you can adjust the number of domains you wish to use. 

Availability sets provide platform availability, but the applications running in the VMs need to be designed for high availability and also take advantage of the platform features to ensure the highest availability.

## Create an availability set
You can't create VMs and add them to an availability set later, you have to create the availability set before or during the creation of the first VM in the set. This is because the hardware cluster for the availability set is selected when the first VM in the availability set is deployed. VMs need to be created within the availability set to make sure they are deployed on the right hardware cluster. 

You can create an availability set using [az vm availability-set create](/cli/azure/availability-set#create). In this example, we set both the number of update and fault domains at **2** for the availability set named **myAvailabilitySet** in the **myRGAvailabilitySet** resource group.

```azurecli
az vm availability-set create \
    --resource-group myRGAvailabilitySet \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

## Create VMs inside an availability set

When you create a VM using [az vm create](/cli/azure/vm#create) you specify the availability set using the `--availability-set` parameter to specify the name of the availability set. In this example, we are creating 3 virtual machines. Because they availability set was created with 2 update and fault domains, one domain has 2 VMs and the other has 1 VM. 

```azurecli
for i in `seq 1 3`; do
   az vm create \
     --resource-group myRGAvailabilitySet \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

## Check for available VM sizes 

You can add more VMs to the availability set later, but you need to know what VM sizes are available on the hardware cluster. Use `az vm availability-set list-sizes` to list all the available sizes on the hardware cluster for the availability set.

```azurecli
az vm availability-set list-sizes \
     --resource-group myRGAvailabilitySet \
     --name myAvailabilitySet
```

## Next steps

Tutorial - [Create a virtual machine scale set](tutorial-create-vmss.md)

Further reading - [Manage the availability of Linux virtual machines](manage-availability.md)