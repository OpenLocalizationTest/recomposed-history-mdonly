<properties
	pageTitle="Deploy an App on Virtual Machine Scale Sets| Microsoft Azure"
	description="Deploy an app on Virtual Machine Scale Sets"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="guybo"/>


# Upgrade a Virtual Machine Scale Set

This article describes how you can roll out an OS update to an Azure VM Scale Set without any downtime. In this context an OS update is either changing the version/sku of the OS, or changing the URI of a custom image. Updating without downtime means updating VMs one at a time, or in groups (such as one fault domain at a time), rather than all at once, so any VMs which are not being upgraded can keep running.

To avoid ambiguity let’s distinguish 3 types of OS update you might want to do:

1. Changing the version or sku of a platform image. E.g. changing Ubuntu 14.04.2-LTS version from 14.04.201506100 to 14.04.201507060, or changing the Ubuntu 15.10/latest sku to 16.04.0-LTS/latest etc. Covered in this article.

2. You built a new version of a custom image and want to change the URI which points to the image (properties->virtualMachineProfile->storageProfile->osDisk->image->uri). Covered in this article.

3. Patching the OS from within a VM e.g. installing a security patch, using Windows Update etc. Supported but not covered in this article.

The first 2 are supported requirements. For the third one, at least for now, you’d need to create a new scale set to do that. This article covers options 1. and 2.
Note: VM Scale Sets which are deployed as part of a [Service Fabric](https://azure.microsoft.com/services/service-fabric/) cluster are not covered here.

The basic sequence for changing the OS version/sku of a platform image or the URI of a custom image looks like this:

* Get the VMSS model.

* Change the version, sku or URI value in the model.

* Update the model.

* Do a manualUpgrade call on the VMs in the scale set. This is only relevant if the upgradePolicy property of your Scale Set is set to “Manual”. If it is set to “Automatic”, all the VMs will upgraded at once and there will be downtime.


With this all this in mind, let’s review how you could update the version of a scale set in PowerShell, and using the REST API. These examples cover the case of a platform image, but hopefully we have provided enough information for you to adapt this process to a custom image.

## PowerShell

This example updates a Windows VM Scale Set to a new version “4.0.20160229”. After updating the model, it does an update one VM instance at a time.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the VMSS model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

If you were updating the URI for a custom image instead of changing a platform image version, you’d replace the “set the new version” line with something like this:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## Using the REST API

Here are a couple of Python examples which use the Azure REST API to roll out an OS version update. In both cases they make use of the lightweight [azurerm](https://pypi.python.org/pypi/azurerm) library of Azure REST API wrapper functions to do a GET on the scale set to get the model, and then a PUT with an updated model. They also look at VM instances views to identify the VMs by update domain.

### vmssupgrade

vmssupgrade is Python script to roll out an OS upgrade to a running VM Scale Set, one update domain at a time. You can find it here: https://github.com/gbowerman/vmsstools

![vmssupgrade screenshot](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

This script lets you choose specific VMs to update, or specify an update domain, and supports changing a platform image version OR changing the URI of a custom image.

### vmsseditor

This is a general purpose editor for VM Scale Sets, which shows VM status as a heatmap where one row represents one update domain. Among other things you can update the model for a VMSS with a new version, sku or custom image URI, and then pick Fault Domains to upgrade (i.e. all the VMs in that update domain are then upgraded to the new model), or a rolling upgrade based on the batch size of your choice. vmsseditor can be found in the following github repo: https://github.com/gbowerman/vmssdashboard

For example, here I’ve just updated the model of a scale set to Ubuntu 14.04-2LTS version 14.04.201507060 (note this is an old screenshot, many more options have since been added to this tool).

![vmsseditor screenshot 1](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

After clicking Upgrade and then Get Details again, VMs in UD 0 are starting to update.

![vmsseditor screenshot 2](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)
