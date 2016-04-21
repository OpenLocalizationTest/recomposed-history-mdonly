<properties
   pageTitle="Create a Linux VM on Azure using the CLI | Microsoft Azure"
   description="Create a Linux VM on Azure using the CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/08/2016"
   ms.author="v-livech"/>


# Create a Linux VM on Azure using the CLI

This article shows how to quickly create a Linux Virtual Machine on Azure using the Azure CLI's `azure vm quick-create` command. The `quick-create` command creates a VM with a basic infrastructure that you can use to prototype or test a concept very rapidly. Think of it as the quickest way to a Linux bash shell.  The article requires an Azure account ([get a free trial](https://azure.microsoft.com/pricing/free-trial/)] and [the Azure CLI](../xplat-cli-install.md) in resource manager mode (`azure config mode arm`).  You can also quickly create a Linux VM using the [Azure Portal](virtual-machines-linux-quick-create-portal.md).

## Quick Command Summary

```
# One command to quickly the VM that prompts for arguments
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## Create the Linux VM

In the following command, you can use any image you want, but this example uses `canonical:ubuntuserver:14.04.2-LTS:latest` to create a VM quickly. (To locate an image in the marketplace, [search for an image](virtual-machines-linux-cli-ps-findimage.md) or you can [upload your own custom image](virtual-machines-linux-create-upload-generic.md).) It will look something like the following.

In the following command walk through, please replace the prompts with values from your own environment.  We are using "example" values for this article

```bash
# Create the Linux VM using prompts
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
info:    Executing command vm quick-create
Resource group name: exampleRGname
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: Linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ahmet
Password: ************************************************
Confirm password: ************************************************
+ Looking up the VM "exampleVMname"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/azure_id_rsa.pub
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli38948918364134011018
info:    Could not find the storage account "cli38948918364134011018", trying to create new one
+ Creating storage account "cli38948918364134011018" in "westus"
+ Looking up the storage account cli38948918364134011018
+ Looking up the NIC "examp-westu-3894891836-nic"
info:    An nic with given name "examp-westu-3894891836-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-3894891836-vnet"
info:    Preparing to create new virtual network and subnet
| Creating a new virtual network "examp-westu-3894891836-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-3894891836-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-3894891836-vnet"
+ Looking up the subnet "examp-westu-3894891836-snet" under the virtual network "examp-westu-3894891836-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-3894891836-pip"
info:    PublicIP with given name "examp-westu-3894891836-pip" not found, creating a new one
+ Creating public ip "examp-westu-3894891836-pip"
+ Looking up the public ip "examp-westu-3894891836-pip"
+ Creating NIC "examp-westu-3894891836-nic"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Creating VM "exampleVMname"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Looking up the public ip "examp-westu-3894891836-pip"
data:    Id                              :/subscriptions/<**subscriptionsID**>/resourceGroups/exampleRGname/providers/Microsoft.Compute/virtualMachines/exampleVMname
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMname
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clife36db80ae0539d2-os-1460152163612
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://<**subscriptionsID**>.blob.core.windows.net/vhds/clife36db80ae0539d2-os-1460152163612.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ahmet
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-4C-B2
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-3894891836-nic
data:          Location                  :westus
data:            Public IP address       :13.88.22.244
data:            FQDN                    :examp-westu-3894891836-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://<**subscriptionsID**>.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

You can now SSH into your VM on the default SSH port 22 and the Public IP address listed in the output above.

```
ahmet@fedora$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

The `azure vm quick-create` is the way to quickly create a VM so you can log in  to a bash shell and get working. Using `vm quick-create` does not give you the additional benefits of a complex environment, however, so if you want to customize your environment you can [use an Azure resource manager template to create a specific deployment quickly](virtual-machines-linux-cli-deploy-templates.md), or you can [create your own custom environment for a Linux VM using Azure CLI commands directly](virtual-machines-linux-cli-deploy-templates.md).

The example above creates:

- an Azure Resource Group to deploy the VM into
- an Azure Storage account to hold the .vhd file that is the VM image
- an Azure Virtual Network and subnet to provide connectivity to the VM
- a virtual Network Interface Card (NIC) to associate the VM with the network
- a public IP address and subdomain prefix to provide an internet address for external use and then creates the Linux VM inside that environment.

## Next Steps

Now you've created a Linux VM quickly to use for testing or demonstration purposes. To create a Linux VM customized for your infrastructure you can follow any of the articles below.

- [Create a Linux VM on Azure using Templates](virtual-machines-linux-cli-deploy-templates.md)
- [Create a SSH Secured Linux VM on Azure using Templates](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Create a Linux VM using the Azure CLI](virtual-machines-linux-create-cli-complete.md)

Those articles will get you started in building an Azure infrastructure as well as any number of proprietary and open-source infrastructure deployment, configuration, and orchestration tools.
