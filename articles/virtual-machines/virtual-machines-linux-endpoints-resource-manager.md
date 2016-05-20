<properties
   pageTitle="Allow external access to Azure VM | Microsoft Azure"
   description="Understand to open a port to allow external access to your VM using the resource manager deployment model"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/20/2016"
   ms.author="iainfou"/>

## Allow external access to your VM
To allow external traffic to reach your virtual machine (VM) using the Resource Manager model, you create an Access Control List (ACL) rule in a Network Security Group that is associated with your virtual machine. To create a Network Security Group and ACL rules you will need [the Azure CLI](../articles/xplat-cli-install.md) in resource manager mode (azure config mode arm).

## Quick Commands
In the following example you will create a Network Security Group, create a rule to allow HTTP traffic to your webserver, then assign this rule to your VM.

Create your Network Security Group as follows, entering your own names and location appropriately:

```
azure network nsg create --resource-group TestRG --name TestNSG --location westus
```

Add your rule to allow HTTP traffic to your webserver:

```
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow --resourcegroup TestRG --nsg-name TestNSG --name AllowHTTP
```

Associate the NSG with your VM's network interface:

```
azure network nic set --resource-group TestRG --name TestNIC --network-security-group-name TestNSG
```

Alternatively, you can associate your Network Security Group with a virtual network subnet rather than directly to the network inface on a single VM:

```
azure network vnet subnet set --resource-group TestRG --name TestSubnet --network-security-group-name TestNSG
```

## More Information on Network Security Groups
The quick commands here allow you to get up and running and traffic flowing to your VM. Network Security Groups provide a lot of great features and granularity for controlling access to the your resources. You can read more about [creating a Network Security Group and ACL rules here](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Network Security Groups and ACL rules can also be defined as part of Azure Resouce Manager templates. Read more about [creating Network Security Groups with templates](../virtual-network/virtual-networks-create-nsg-arm-template.md).

If you need to use port-forwarding to map a unique external port to an internal port on your VM, you need to place an Azure load balancer in front of your VM. Network Address Translation (NAT) rules will work alongside Network Security Group ACL rules. You can learn about [creating an Internet-facing load balancer](../virtual-network/load-balancer-get-started-internet-arm-cli.md).

## Next Steps

- [Azure Resource Manager overview](../resource-group-overview.md)
- [What is a Network Security Group (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Azure Resource Manager Overview for Load Balancers](../virtual-network/load-balancer-arm.md)