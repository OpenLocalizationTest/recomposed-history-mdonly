<properties linkid="manage-linux-howto-custom-create-vm" urlDisplayName="Custom create a VM" pageTitle="Custom create a virtual machine running Linux in Azure" metaKeywords="Azure custom vm, creating custom vm" description="Learn how to create a custom virtual machine in Azure." metaCanonical="http://www.windowsazure.com/en-us/manage/windows/how-to-guides/custom-create-a-vm/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

#How to Create a Custom Virtual Machine

A *custom* virtual machine refers to a virtual machine you create using the **From Gallery** option because it gives you more configuration choices than the **Quick Create** option. These  include:

- More choices for the image to use to create the virtual machine (VM)
- Connecting the VM to a virtual network
- Installing the VM Agent and extensions, such as for antimalware 
- Adding the VM to an existing cloud service 
- Adding the VM to an availability set

**Important**: If you want your virtual machine to use a virtual network so you can connect to it directly by hostname or set up cross-premises connections, make sure you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For more information about virtual networks, see [Azure Virtual Network Overview](http://go.microsoft.com/fwlink/p/?LinkID=294063).

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]


