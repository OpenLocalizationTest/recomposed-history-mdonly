<properties urlDisplayName="Use Azure PowerShell to create and preconfigure Windows-based Virtual Machines" pageTitle="Use Azure PowerShell to create and preconfigure Windows-based Virtual Machines" metaKeywords="" description="Learn how to use Azure PowerShell to create and preconfigure Windows-based virtual machines in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Use Azure PowerShell to create and preconfigure Windows-based Virtual Machines" authors="josephd" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="1/12/2015" ms.author="josephd" />

<h1 id="azpswindowsvms">Use Azure PowerShell to create and preconfigure Windows-based Virtual Machines</h1>

These steps show you how to customize a set of Azure PowerShell commands that create and pre-configure a Windows-based Azure virtual machine by using a building block approach. You can use this process to quickly create a command set for a new Windows-based virtual machine and expand an existing deployment or to create multiple command sets that quickly build out a custom dev/test or IT pro environment.

These steps follow a fill-in-the-blanks approach for creating Azure PowerShell command sets. This approach can be useful if you are new to PowerShell or you just want to know what values to specify for successful configuration. Advanced Azure PowerShell users can take the commands and substitute their own values for the variables (the lines beginning with "$").

##Step 1

If you haven't done so already, use the instructions in [How to install and configure Azure PowerShell](http://azure.microsoft.com/en-us/documentation/articles/install-configure-powershell/) to install Azure PowerShell on your local computer. Then, open an administrator-level Azure PowerShell command prompt.

##Step 2

Set your Azure subscription and storage account by running theses commands at the Azure PowerShell command prompt. Replace everything within the quotes, including the < and > characters, with the correct names.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

You can get the correct subscription name from the SubscriptionName property of the output of the **Get-AzureSubscription** command. You can get the correct storage account name from the Label property of the output of the **Get-AzureStorageAccount** command after you issue the **Select-AzureSubscription** command. You can also store these commands in a text file for future use.

##Step 3

Next, you need to determine the ImageFamily value for the specific image corresponding to the Azure virtual machine you want to create. Here are some examples from the Gallery in the Azure Management Portal.

![](./media/virtual-machines-use-PS-create-preconfigure-windows-vms/PSPreconfigWindowsVMs_1.png)
 
You can also get the list of available ImageFamily values by running this command.

	Get-AzureVMImage | select ImageFamily -Unique

Here are some examples of ImageFamily values for Windows-based computers:

- Windows Server 2012 R2 Datacenter
- Windows Server 2008 R2 SP1
- Windows Server Technical Preview
- SQL Server 2012 SP1 Enterprise on Windows Server 2012

Next, run these commands to list the images for that family.

	$family="<ImageFamily value>"
	Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}

Each image has a set of properties that starts with the ImageName property and ends with the OperationStatus property. If there are multiple images for different months, locate the image with the most recent PublishedDate property.

##Step 4

Open a fresh instance of the text editor of your choice and copy the following into the new text file.  

	$image="<ImageName value>"

From the output of images in step 3, copy the ImageName property from the appropriate image and replace the **<ImageName value>** part of the command. Here is an example.

	$image="a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201411.01-en.us-127GB.vhd"

##Step 5

Build the rest of your command set by copying the appropriate set of blocks below into your new text file and then filling in the variables and removing the < and > characters. See the two examples at the end of this article for an idea of the final result.

Start your command set by choosing one of the two command blocks (required):


Option 1:Specify a virtual machine name and a size.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Option 2: Specify a name, size, and availability set name.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Optionally, for a standalone Windows computer, specify the local administrator account and password.

	$localadminusername="<local administrator account name>"
	$localadminpassword="<local administrator account password>"
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $localadminusername -Password $localadminpassword

Optionally, to add the Windows computer to an existing Active Directory domain, specify the local administrator account and password, the domain, and the account credentials of a domain account.

	$localadminusername="<local administrator account name>"
	$localadminpassword="<local administrator account password>"
	$domaindns="<FQDN of the domain that the machine is joining>"
	$domacctdomain="<domain of the account that has permission to add the machine to the domain>"
	$domacctname="<domain account name that has permission to add the machine to the domain>"
	$domacctpassword="<password of the domain account that has permission to add the machine to the domain>"
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $localadminusername -Password $localadminpassword -WindowsDomain -Domain $domacctdomain -DomainUserName $domacctname -DomainPassword $domacctpassword -JoinDomain $domaindns

Note that this requires you to specify the account name and password of an Active Directory domain account. If you are saving the resulting command set as a file, ensure that you store it in a secure location to protect the domain account name and password.

Optionally, assign the virtual machine a specific IP address, known as a static DIP.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

You can verify that a specific IP address is available with:

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Optionally, assign the virtual machine to a specific subnet in an Azure virtual network.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Optionally, add a single data disk to the virtual machine. 

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

For an Active Directory domain controller, set $hcaching to "None".

Optionally, add the virtual machine to an existing load-balanced set for external traffic.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Finally, start the virtual machine creation process by choosing one of these command blocks (required):

Option 1: Create the virtual machine in a new cloud service. 

	New-AzureVM –Location "<An Azure location, such as US West>" -VMs $vm1

Option 2: Create the virtual machine in an existing cloud service. 

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

The short name of the cloud service is the name that appears in the list of Cloud Services in the Azure Management Portal or in the list of Resource Groups in the Azure Preview Portal. 

Option 3: Create the virtual machine in an existing cloud service and virtual network.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


##Step 6

Review the Azure PowerShell command set you built in your text editor consisting of multiple blocks of commands from step 5. Ensure that you have specified all the needed variables and that they have the correct values. Also make sure that you have removed all the < and > characters.

Copy the command set to the clipboard and then right-click your open Azure PowerShell command prompt. This will issue the command set as a series of PowerShell commands and create your Azure virtual machine.

If you will be creating this VM again or a similar one, you can: 

- Save this command set as a text file or as a PowerShell script file (*.ps1)
- Save this command set as an Azure automation runbook in the **Automation** section of the Azure Management Portal 

##Examples

Here are two examples of using the steps above to build Azure PowerShell command sets that create Azure virtual machines.

###Example 1

I need a PowerShell command set to create the initial virtual machine for an Active Directory domain controller that:

- Uses the Windows Server 2012 R2 image
- Has the name AZDC1 
- Is a standalone computer
- Has an additional data disk of 20 GB
- Has the static IP address 192.168.244.4
- Is in the BackEnd subnet of the AZDatacenter virtual network
- Is in the Azure-TailspinToys cloud service

Here is the corresponding Azure PowerShell command set to create this virtual machine, with blank lines between each block for readability.

	$image="a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201411.01-en.us-127GB.vhd"
	$vmname="AZDC1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$localadminusername="DCLocalAdmin"
	$localadminpassword="DCeq7294*"
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $localadminusername -Password $localadminpassword

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=20
	$disklabel="DCData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

###Example 2

I need a PowerShell command set to create a virtual machine for a line-of-business server that:

- Uses the Windows Server 2012 R2 image
- Has the name LOB1
- Is a member of the corp.contoso.com domain
- Has an additional data disk of 200 GB 
- Is in the FrontEnd subnet of the AZDatacenter virtual network
- Is in the Azure-TailspinToys cloud service

Here is the corresponding Azure PowerShell command set to create this virtual machine.

	$image="a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201411.01-en.us-127GB.vhd"
	$vmname="LOB1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$localadminusername="LOBLocalAdmin"
	$localadminpassword="LOBmx7137*"
	$domacctdomain="CORP"
	$domacctname="admin7"
	$domacctpassword="frank0987&"
	$domaindns="corp.contoso.com"
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $localadminusername -Password $ localadminpassword -WindowsDomain -Domain $domacctdomain -DomainUserName $domacctname -DomainPassword $domacctpassword -JoinDomain $domaindns

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=200
	$disklabel="LOBData"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


##Additional Resources

[Virtual machines documentation](http://azure.microsoft.com/en-us/documentation/services/virtual-machines/)

[Azure virtual machines FAQ](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Overview of Azure Virtual Machines](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[How to install and configure Azure PowerShell](http://azure.microsoft.com/en-us/documentation/articles/install-configure-powershell/)