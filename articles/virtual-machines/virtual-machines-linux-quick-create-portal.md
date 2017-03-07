---
title: Azure Quick Start - Create VM Portal | Microsoft Docs
description: Azure Quick Start - Create VM Portal
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: nepeters
---

# Create a Linux virtual machine with the Azure portal

Azure virtual machines (VMs) can be created through the Azure portal. This method provides a browser-based user interface for creating and configuring VMs, and all related Azure resources.

Before you start, you need both a private and public SSH key. For detailed information on creating SSH key pairs for Azure, see [Create SSH keys](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## Create virtual machine

1. Log in to the [Azure portal](http://portal.azure.com), and click the **New** button.

2. Select **Compute** from the Marketplace screen, select **Ubuntu Server 16.04 LTS** from the featured apps screen, and then click the **Create** button.

3. Fill out the virtual machine **basics** form. The SSH public key value is found in the public key file that you have created for SSH authentication. Click **OK** once complete.

    ![Enter basic information about your VM in the portal blade](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

4. Choose a size for the VM and click **Select**. 

    ![Select a size for your VM in the portal blade](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

5. Select **Yes** under **Use managed disks**, and then click **OK**.

6. Click **Ok** to start the virtual machine deployment.

## Connect to virtual machine

After the VM deployment has finished, select the VM icon on the Azure dashboard and click the **Connect** button. The connect button displays an SSH connection string that can be used to connect to the virtual machine.

![Portal 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

Use the following command to create an SSH session. Replace the connection string with the one you copied from the Azure portal.

```bash 
ssh <replace with IP address>
```

## Delete virtual machine

When no longer needed, delete the resource group, virtual machine, and all related resources. To do so, select the resource group from the virtual machine blade and click **Delete**.

## Next Steps

[Create highly available virtual machines tutorial](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Explore VM deployment CLI samples](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
