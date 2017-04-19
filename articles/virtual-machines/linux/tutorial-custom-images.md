---
title: Create custom VM iamges with the Azure CLI | Microsoft Docs
description: Tutorial - Create a custom VM image using the Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: cynthn
---

# Create a custom image of an Azure VM using the CLI

In this tutorial, you learn how to create your own custom image of an Azure virtual machine. Custom images are like marketplace images, but you create them yourself. Custom images can be used to boot strap configurations such as preloading applications, application configurations, and other OS configurations. When creating a custom image, the VM plus all attached disks are included in the image. 

If you would like to walk through the steps of the tutorial, a customized VM is needed. If you do not have one, the [Quickstart with Azure CLI](quick-create-cli.md) can help.

The steps in this tutorial can be completed using the latest [Azure CLI 2.0](/cli/azure/install-azure-cli).

## Create an image

To create an image of a virtual machine, you need to prepare the VM by deprovisioning, deallocating, and then marking the source VM as generalized.

### Deprovision the VM 

Deprovisioning generalizes the VM by removing machine-specific information. This generalization makes it possible to deploy many VMs from a single image. During deprovisioning, the host name is reset to `localhost.localdomain`. SSH host keys, nameserver configurations, root password, and cached DHCP leases are also deleted.

To deprovision the VM, use the Azure VM agent (waagent). The Azure VM agent is installed on the VM and manages provisioning and interacting with the Azure Fabric Controller. For more information, see the [Azure Linux Agent user guide](agent-user-guide.md).

Connect to your VM using SSH. Replace the example IP address with the public IP address of your VM.

```bash
ssh 52.174.34.95
```

Run the following command to deprovision the VM. With the `+user` argument, the last provisioned user account and any associated data are also deleted.
   
```bash
sudo waagent -deprovision+user -force
```

Close the SSH session.

```bash
exit
```

### Deallocate and mark the VM as generalized

To create an image, the VM needs to be deallocated. Deallocate the VM using [az vm deallocate](/cli//azure/vm#deallocate). 
   
```azurecli
az vm deallocate --resource-group myRGCaptureImage --name myVM
```

Finally, set the state of the VM as generalized with [az vm generalize](/cli//azure/vm#generalize).
   
```azurecli
az vm generalize --resource-group myRGCaptureImage --name myVM
```

### Capture the image

Now you can create an image of the VM by using [az image create](/cli//azure/image#create). The following example creates an image named `myImage` from a VM named `myVM`.
   
```azurecli
az image create --resource-group myRGCaptureImage --name myImage --source myVM
```
 
## Create a VM from an image

You can create a VM using an image with [az vm create](/cli/azure/vm#create). The following example creates a VM named `myVMfromImage` from the image named `myImage`.

```azurecli
az vm create --resource-group myRGCaptureImage --name myVMfromImage --image myImage --admin-username azureuser --generate-ssh-keys
```

## Next steps

Tutorial - [Create highly available VMs](tutorial-availability-sets.md)

Further reading - [Images](../../storage/storage-managed-disks-overview.md#images)