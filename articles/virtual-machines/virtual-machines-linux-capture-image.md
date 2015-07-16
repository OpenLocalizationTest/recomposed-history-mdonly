<properties
	pageTitle="Capture an image of a virtual machine running Linux"
	description="Learn how to capture an image of an Azure virtual machine (VM) running Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="dkshir"/>


# How to Capture a Linux Virtual Machine to Use as a Template##

This article shows you how to capture an Azure virtual machine running Linux so you can use it like a template to create other virtual machines. This template includes the OS disk and any data disks attached the virtual machine. It doesn't include networking configuration, so you'll need to configure that when you create the other virtual machines that use the template.

Azure treats this template as an image and stores it under **My Images**. This is also where any images you've uploaded are stored. For more information about images, see [About Virtual Machine Images in Azure] [].

##Before You Begin##

These steps assume that you've already created an Azure virtual machine and configured the operating system, including attaching any data disks. If you haven't done this yet, see these instructions:

- [How to Create a Custom Virtual Machine] []
- [How to Attach a Data Disk to a Virtual Machine] []

##Capture the Virtual Machine##

1. Connect to the virtual machine using an SSH client of your choice. For details, see [How to Log on to a Virtual Machine Running Linux] [].

2. In the SSH window, type the following command.  Note that the output from `waagent` may vary slightly depending on the version of this utility:

	`$ sudo waagent -deprovision+user`

	This command will attempt to clean the system and make it suitable for re-provisioning. This operation performs the following tasks:

	- Removes SSH host keys (if Provisioning.RegenerateSshHostKeyPair is 'y' in the configuration file)
	- Clears nameserver configuration in /etc/resolv.conf
	- Removes the `root` user's password from /etc/shadow (if Provisioning.DeleteRootPassword is 'y' in the configuration file)
	- Removes cached DHCP client leases
	- Resets host name to localhost.localdomain
	- Deletes the last provisioned user account (obtained from /var/lib/waagent) **and associated data**.

	![Deprovision the virtual machine](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

	>[AZURE.NOTE] Deprovisioning deletes files and data in an effort to "generalize" the image. Only run this command on virtual machines that you intend to capture as a new image template.


3. Type **y** to continue.

	![Deprovision of virtual machine successful](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

	**Note** that you can add the `-force` parameter to avoid this confirmation step.

	>[AZURE.NOTE] Deprovisioning only generalizes the Linux image, but does not guarantee that the image is cleared of all sensitive information and is suitable for redistribution to third parties.


4. Type **Exit** to close the SSH client.

>[AZURE.NOTE] All the steps below can be just as easily done in the [Management Portal](http://manage.windowsazure.com). The next steps assume you have already [installed the Azure CLI](../xplat-cli-install.md) on your client computer.

5. From your client computer, open Azure CLI and login to your Azure subscription. For details, read [Connect to an Azure subscription from the Azure CLI](../xplat-cli-connect.md).

6. Make sure you are in Service Management mode by typing

	azure config mode asm

7. Shut down the virtual machine which is already deprovisioned in the steps above with:

	azure vm shutdown <your-virtual-machine-name>

	>[AZURE.NOTE] You can find out all the virtual machines created in your subscription by using `azure vm list`

8. When the virtual machine is stopped, capture the image with the command:

	azure vm capture -t <your-virtual-machine-name> <new-image-name>

Type the image name you want in place of <new-image-name>. This is a generalized OS image. Note the `-t` subcommand which deletes the original virtual machine.

9.	The new image is now available in the list of images that can be used to configure your new virtual machine. You can view it with the command

	azure vm image list

On the [Management Portal](manage.windowsazure.com), it will appear in the **IMAGES** list.

	![New image captured](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)

##Next Steps##
The image is ready to be used as a template to create virtual machines. To do this, you'll create a custom virtual machine by using the **From Gallery** method and select the image you just created. For instructions, see [How to Create a Custom Virtual Machine] [].

**See Also:** [Azure Linux Agent User Guide](virtual-machines-linux-agent-user-guide.md)

[How to Log on to a Virtual Machine Running Linux]: virtual-machines-linux-how-to-log-on.md
[About Virtual Machine Images in Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[How to Create a Custom Virtual Machine]: virtual-machines-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: storage-windows-attach-disk.md
