<properties
	pageTitle="Capture a Linux VM to use as a template | Microsoft Azure"
	description="Learn how to capture an image of a Linux-based Azure virtual machine (VM) created with the Azure Resource Manager deployment model."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="danlep"/>


# How to capture a Linux virtual machine to use as a Resource Manager template

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-linux-capture-image.md).


This article shows you how to capture an Azure virtual machine running Linux so you can use it as an Azure Resource Manager template to create other virtual machines. This template specifies the OS disk and data disks attached to the virtual machine. It doesn't include the virtual networking configuration you'll need to create an Azure Resource Manager VM, so you'll need to set up a virtual network, subnet, and network adapter (NIC) in Azure separately before you create another virtual machine that uses the template.

## Before you begin

These steps assume that you've already created an Azure virtual machine in the Azure Resource Manager deployment model and configured the operating system, including attaching any data disks and making other customizations like installing applications. If you haven't done this yet, see these instructions for using the Azure CLI in Azure Resource Manager mode:

- [Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

For example, you might create a resource group named *MyResourceGroup* in the West US region. Then use an **azure vm quick-create** command similar to the following to deploy an Ubuntu 14.04 LTS VM in the resource group.

 	azure vm quick-create -g MyResourceGroup -n <your-virtual-machine-name> "WestUS" -y Linux -Q canonical:ubuntuserver:14.04.2-LTS:14.04.201507060 -u <your-user-name> -p <your-password>

After the VM is provisioned and running, you might want to attach and mount a data disk. See instructions [here](virtual-machines-linux-tutorial.md#attach-and-mount-a-disk).

To perform other customizatons, you'll need to connect to the VM using an SSH client of your choice. For details, see [Connect to your Azure Linux VM using ssh](virtual-machines-linux-tutorial-portal-rm.md#connect-to-your-azure-linux-vm-using-strongsshstrong).


## Capture the VM

1. When you are ready to capture the VM, connect to it using your SSH client.

2. In the SSH window, type the following command.  Note that the output from **waagent** may vary slightly depending on the version of this utility:

	`sudo waagent -deprovision+user`

	This command will attempt to clean the system and make it suitable for re-provisioning. This operation performs the following tasks:

	- Removes SSH host keys (if Provisioning.RegenerateSshHostKeyPair is 'y' in the configuration file)
	- Clears nameserver configuration in /etc/resolv.conf
	- Removes the `root` user's password from /etc/shadow (if Provisioning.DeleteRootPassword is 'y' in the configuration file)
	- Removes cached DHCP client leases
	- Resets host name to localhost.localdomain
	- Deletes the last provisioned user account (obtained from /var/lib/waagent) and associated data.

	>[AZURE.NOTE] Deprovisioning deletes files and data in an effort to "generalize" the image. Only run this command on a VM that you intend to capture as a new image template. It does not guarantee that the image is cleared of all sensitive information or is suitable for redistribution to third parties.

3. Type **y** to continue. You can add the **-force** parameter to avoid this confirmation step.

4. Type **exit** to close the SSH client.

	>[AZURE.NOTE] The next steps assume you have already [installed the Azure CLI](../xplat-cli-install.md) on your client computer.

5. From your client computer, open the Azure CLI and login to your Azure subscription. For details, read [Connect to an Azure subscription from the Azure CLI](../xplat-cli-connect.md).

6. Make sure you are in Resource Manager mode:

	`azure config mode arm`

7. Stop the VM which you already deprovisioned by using the following command:

	`azure vm stop –g <your-resource-group-name> -n <your-virtual-machine-name>`

	>[AZURE.NOTE] You can find all the virtual machines created in your resource group by using `azure vm list <your-resource-group-name>`.

8. Generalize the VM with the following command:

	`azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>`

9. Now capture the image as a local file template with the following command:

	`azure vm capture –g <your-resource-group-name> -n <your-virtual-machine-name> -p <your-vhd-name-prefix> -t <your-template-file-name.json>`

	This command creates a generalized OS image, using the VHD name prefix you specify for the VM disks. The image VHD files get created by default in the same storage account that the original VM used. The **-t** option creates a local JSON file template you can use to create a new VM from the image.



## Deploy a new VM from the captured image
The image is ready to be used with a template to create a new Linux VM. These steps show you how to use the Azure CLI and the JSON file template you created with the `azure vm capture` command to create the VM in a new virtual network.

## Prepare network settings

To use the template, you first need to set up a virtual network and NIC for your new VM. We recommend you create a new resource group for these resources. Run commands similar to the following, substituting names for your resources and an appropriate Azure location ("centralus" in these commands):

	azure group create <your-new-resource-group-name> -l "centralus"

	azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"

	azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>

	azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"

	azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

To deploy a VM from the image, you will need the Id of the NIC you created. Obtain it by running the following command.

	azure network nic show <your-new-resource-group-name> <your-nic-name>

The **Id** in the output is a string similar to

	/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<your-new-resource-group-name>/providers/Microsoft.Network/networkInterfaces/<your-nic-name>



### Create a new deployment
Now run the following command to create your VM from the captured VM image and the template JSON file you saved.

	azure group deployment create –g <your-new-resource-group-name> -n <your-new-deployment-name> -f <your-template-file-name.json>

You are prompted to supply a new VM name, admin user name and password, and the Id of the NIC you created previously.

	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	vmName: mynewvm
	adminUserName: myadminuser
	adminPassword: ********
	networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

You will see output similar to the following for a successful deployment.

	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "dlnewdeploy"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mynewdeploy
	data:    ResourceGroupName  : mynewrg
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-29T16:35:47.3419991Z
	data:    Mode               : Incremental
	data:    Name                Type          Value


	data:    ------------------  ------------  -------------------------------------
	
	data:    vmName              String        mynewvm


	data:    vmSize              String        Standard_D1


	data:    adminUserName       String        myadminuser


	data:    adminPassword       SecureString  undefined


	data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
	info:    group deployment create command OK

### Verify the deployment

Now SSH to the virtual machine you created to verify the deployment. To connect via SSH, find the IP address of the VM you created by running the following command:

	azure network public-ip show <your-new-resource-group-name> <your-ip-name>

The public IP address is listed in the command output.

## Next steps

Use the captured image and template to deploy additional VMs using the steps in the preceding section. The following are the basic steps:

* Copy the template JSON file and enter a unique value in the **URI** to each VHD in the VM
* Prepare settings for a new NIC in either the same or a different virtual network
* Create a deployment in the resource group in which you set up the virtual network, using the modified template JSON file
