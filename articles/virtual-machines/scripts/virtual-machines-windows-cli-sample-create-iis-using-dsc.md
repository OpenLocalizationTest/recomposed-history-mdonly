---
title: Azure CLI Script Sample - Create a Windows Server 2016 VM with IIS using DSC | Microsoft Docs
description: Azure CLI Script Sample - Create a Windows Server 2016 VM with IIS using DSC
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 

ms.assetid:
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
---

# Create a VM with IIS using DSC

This script creates a virtual machine, and then uses the Azure Virtual Machine DSC custom script extension to install and configure IIS. 

Before running this script, ensure that a connection with Azure has been created using the `az login` command. Also, you should change the $AdminPassword variable at the start of the script to unique and meeting password complexity requirements.

This sample works in a Bash shell. For options on running Azure CLI scripts on Windows client, see [Running the Azure CLI in Windows](../virtual-machines-windows-cli-options.md).

## Create VM sample

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-windows-iis-using-dsc/create-windows-iis-using-dsc.sh "Quick Create VM")]

## Clean up deployment 

After the script sample has been run, the following command can be used to remove the Resource Group, VM, and all related resources.

```azurecli
az group delete --name myResourceGroup --yes
```

## Script explanation

This script uses the following commands to create a resource group, virtual machine, and all related resources. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Creates a resource group in which all resources are stored. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Creates the virtual machine and connects it to the network card, virtual network, subnet, and NSG. This command also specifies the virtual machine image to be used, and administrative credentials.  |
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm#create) | Add the Custom Script Extension to the virtual machine which invokes a script to install IIS. |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | Creates a network security group rule to allow inbound traffic. In this sample, port 80 is opened for HTTP traffic. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Deletes a resource group including all nested resources. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](https://docs.microsoft.com/cli/azure/overview).

Additional virtual machine CLI script samples can be found in the [Azure Windows VM documentation](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).