<properties
	pageTitle="Move a VM to another subscription | Microsoft Azure"
	description="Move a VM to another Azure subscription in the Resource Manager deployment model."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2016"
	ms.author="cynthn"/>

	


# Move a VM to another Azure subscription using a template

This article walks you through how to move a simple VM between subscriptions. This can be handy if you originally created a VM in a personal subscription and now want to move it to your company's subscription to continue your work.

> [AZURE.NOTE] New resource IDs will be created as part of the move. Once the VM has been moved you will need to update your tools and scripts to use the new resource IDs. 

1. Open the [Azure portal](https://portal.azure.com).
2. Click **Browse** > **Virtual machines** and select the VM you would like to move from the list.
3. In the Essentials section, click on the ![pencil icon](./media/virtual-machines-windows-move-vm/pencil.png) **Change subscription** pencil icon next to the subscription name. The **Move resources** blade will open.
	![Screenshot of the Essentials section where you click the pencil icon to open the Move resources blade.](./media/virtual-machines-windows-move-vm/move-button.png)
4. Select each of the resources to move. In most cases, you should move all of the listed optional resources.
5. Select the **Subscription** where you want the VM to be moved.
6. Select an existing Resource group or type the a name to have  a new resource group created.
7. When you are done, select that you understand that new resource ids will be created and those need to be used with the VM once it is moved, then click **OK**.

	![Screenshot of the Move resources blade.](./media/virtual-machines-windows-move-vm/move.png)
	
