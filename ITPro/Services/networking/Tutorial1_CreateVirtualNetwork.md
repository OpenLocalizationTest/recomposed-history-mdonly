# Tutorial 1: Creating a Virtual Network in Windows Azure

This tutorial walks you through the steps to create a Windows Azure virtual network using the Management Portal.

This tutorial assumes you have no prior experience using Windows Azure.

After completing this tutorial, you will have a virtual network to which you can deploy your Windows Azure services.

For information about creating a virtual network with cross-premises connectivity (i.e., connecting to Active Directory or SharePoint located at your company), see the following tutorials:


*  [Tutorial 2: Creating a Virtual Network for cross-premises connectivity](../../../manage/services/networking/Tutorial2_CreateVNetCrossPrem/)

*  [Install a Replica Active Directory Domain Controller in Windows Azure Virtual Network](../../../manage/windows/tutorials/tutorial-AD-1/)

*  [Install a new Active Directory forest in Windows Azure](../../../manage/windows/tutorials/tutorial-AD-2/)

##  Objectives

In this tutorial you will learn:

*  How to setup a basic Windows Azure virtual network to which you can add Windows Azure services.

##  Prerequisites

*  Windows Live account with at least one valid, active subscription.

*  Address space and IPs to be used for the virtual network and subnets.

##  Create a Virtual Network

**To create a cloud-only virtual network:**

1.	Log in to the [Windows Azure Management Portal](http://manage.windowsazure.com/).
2.	In the lower left-hand corner of the screen, click **New**. 

	![Image1][]

3.	In the navigation pane, click **Network**, and then click **Custom Create**.

	![Image2][]

4.	On the **Virtual Network Details** screen, enter the following information, and then click the next arrow.

	*  **NAME:** Type *YourVirtualNetwork*.

*  **AFFINITY GROUP:** From the drop-down list, select **Create a new affinity group**. Affinity groups are a way to physically group Windows Azure services together at the same data center to increase performance. Only one virtual network can be assigned an affinity group.

*  **REGION:** From the drop-down list, select **West US**. Your virtual network will be created at a datacenter located in the specified region.

*  **AFFINITY GROUP NAME:** Type *YourAffinityGroup*.

	![Image3] []

5.	On the **Address Space and Subnets** screen, enter the following information, and then click the next arrow. Address space must conform to RFC 1918 and cannot overlap other virtual network or local network sites.
*  **ADDRESS SPACE:** Type *10.4.0.0/16*.
*  **SUBNETS:** Enter the following:

	-  *FrontEndSubnet, 10.4.2.0/24*

	-  *BackEndSubnet, 10.4.3.0/24*
 
	![Image4] []

6.	On the **DNS Servers and Local Network** screen, on the lower right-hand of the screen, click the check button.

	Your virtual network will now be created.


 	**Note:** You only need to enter DNS server or local network information if you plan to connect to your company’s network. For more information, see [Tutorial 2: Creating a Virtual Network for cross-premises connectivity](../../../manage/services/networking/Tutorial2_CreateVNetCrossPrem/).
 
	![Image5] []

7.	You now have a virtual network in Windows Azure, which you can see on the portal’s **Virtual Network** tab.


	![Image6] []


##  Next Steps
If you’d like, you can continue with the following tutorials:


- [Tutorial 3: Adding a Virtual Machine to a Virtual Network](../../../manage/services/networking/tutorial-AD-2/)

*  [Install a Replica Active Directory Domain Controller in Windows Azure Virtual Network](../../../manage/windows/tutorials/tutorial-AD-1/)

*  [Install a new Active Directory forest in Windows Azure](../../../manage/windows/tutorials/Tutorial3_AddVMachineToVNet/)


[Image1]: ../media/VNTut1_00_New.png
[Image2]: ../media/VNTut1_01_Network_CustomCreate.png
[Image3]: ..//media/VNTut1_02_VNDetails.png
[Image4]: ..//media/VNTut1_03_AddressSpaceAndSubnets.png
[Image5]: ../media/VNTut1_04_DNSServersAndLocalNetworks_CloudOnly.png
[Image6]: ../media/VNTut1_06_VNStatus_Created.png
