<properties
   pageTitle="How to tag a Linux virtual machine | Microsoft Azure"
   description="Learn about tagging a Linux virtual machine created in Azure using the Resource Manager deployment model."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# How to tag a Linux virtual machine in Azure

This article describes different ways to tag a Linux virtual machine in Azure through the Azure Resource Manager. Tags are user-defined key/value pairs which can be placed directly on a resource or a resource group. Azure currently supports up to 15 tags per resource and resource group. Tags may be placed on a resource at the time of creation or added to an existing resource. Please note, tags are supported for resources created via the Azure Resource Manager only. If you want to tag a Windows virtual machine, see [How to tag a Windows virtual machine in Azure](virtual-machines-windows-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Tagging with Azure CLI

Tagging is also supported for resources that are already created through the Azure CLI. To begin, set up your [Azure CLI environment][]. Log in to your subscription through the Azure CLI and switch to ARM mode. You can view all properties for a given Virtual Machine, including the tags, using this command:

        azure vm show -g MyResourceGroup -n MyVM

Unlike PowerShell, if you are adding tags to a resource that already contains tags, you do not need to specify all tags (old and new) before using the `azure vm set` command. Instead, this command allows you to append a tag to your resource. To add a new VM tag through the Azure CLI, you can use the `azure vm set` command along with the tag parameter **-t**:

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

To remove all tags, you can use the **–T** parameter in the `azure vm set` command.

        azure vm set – g MyResourceGroup –n MyVM -T


Now that we have applied tags to our resources via PowerShell, Azure CLI, and the Portal, let’s take a look at the usage details to see the tags in the billing portal.


## Viewing your tags in the usage details

Tags placed on Compute, Network, and Storage resources through the Azure Resource Manager will be populated in your usage details in the [billing portal](https://account.windowsazure.com/).

Click on **Download usage details** to view the usage details in your subscription.

![Usage details in Azure portal](./media/virtual-machines-common-tag/azure-portal-tags-usage-details.png)

Select your billing statement and the **Version 2** usage details:

![Version 2 Preview Usage Details in Azure portal](./media/virtual-machines-common-tag/azure-portal-version2-usage-details.png)

From the usage details, you can see all of the tags in the **Tags** column:

![Tags column in Azure portal](./media/virtual-machines-common-tag/azure-portal-tags-column.png)

By analyzing these tags along with usage, organizations will be able to gain new insights into their consumption data.


## Additional resources

* [Azure Resource Manager Overview][]
* [Using Tags to organize your Azure Resources][]
* [Understanding your Azure Bill][]
* [Gain insights into your Microsoft Azure resource consumption][]





[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../resource-group-using-tags.md
[Understanding your Azure Bill]: ../billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../billing-usage-rate-card-overview.md
