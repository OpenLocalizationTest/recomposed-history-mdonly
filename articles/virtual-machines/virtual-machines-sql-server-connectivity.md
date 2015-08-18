<properties 
   pageTitle="Connect to a SQL Server Virtual Machine on Azure"
   description="This topic describes how to connect to SQL Server running on a Virtual Machine in Azure. The scenarios differ depending on the networking configuration and the location of the client."
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/18/2015"
   ms.author="jroth" />

# Connect to a SQL Server Virtual Machine on Azure

## Links

- [Configure a Site-to-Site VPN in the Management Portal](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [Virtual Network Overview](..\virtual-network\virtual-networks-overview.md)
- [Specify Replicas Page (New Availability Group Wizard/Add Replica Wizard)](https://msdn.microsoft.com/library/hh213088.aspx)
- [Configure an ILB listener for AlwaysOn Availability Groups in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)

## Overview

In many ways, configuring connectivity to SQL Server running on an Azure Virtual Machine does not differ dramatically from the steps required for an on-premises SQL Server instance. You still have to work with the firewall, authentication stategies, and database logins.

But there are some SQL Server connectivity aspects that are specific to Azure VMs. This article covers some [general connectivity scenarios](#connection-scenarios) and then provides [detailed steps for configuring SQL Server connectivity in an Azure VM](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE] This article focuses on connectivity. For a full walk-through of both provisioning and connectivity, see [Provisioning a SQL Server Virtual Machine on Azure](virtual-machines-provision-sql-server.md).

## Connection scenarios

The way a client connects to SQL Server running on a Virtual Machine differs depending on the location of the client and the machine/networking configuration.

### Connect to SQL Server in the same cloud service

Multiple virtual machines can be created in the same cloud service. To understand this virtual machines scenario, see [How to connect virtual machines with a virtual network or cloud service](cloud-services-connect-virtual-machine.md).

After going through the [steps in this article to configure connectivity](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm), you can use the VM **hostname** in the client connection string. For example, if you SQL VM had a DNS name of **yoursqlvm.cloudapp.net**, the hostname is **yoursqlvm**. A client VM in the same cloud service could use the following connection string to connect:

	"Server=yoursqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### Connect to SQL Server over the internet

If you want to connect to your SQL Server database engine from the Internet, you must create a virtual machine endpoint for incoming TCP communication. This Azure configuration step, directs incoming TCP port traffic to a TCP port that is accessible to the virtual machine.

Follow the [steps in this article to configure connectivity](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Any client with internet access could then connect to the SQL Server instance by specifying the cloud service DNS name (such as **yoursqlvm.cloudapp.net**) and the VM endpoint (such as **57500**).

	"Server=yoursqlvm.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Although this enables connectivity for clients over the internet, this does not imply that anyone can connect. Outside clients have to have the correct username and password. For additional security, don't use the well-known port 1433 for the public virtual machine endpoint. And if possible, consider adding an ACL on your endpoint to restrict traffic only to the clients you permit. For instructions on using ACLs with endpoints, see [How to set up endpoints to a virtual machine](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint). 

>[AZURE.NOTE] It is important to note that when you use this technique to communicate with SQL Server, all data returned is considered outgoing traffic from the datacenter. It is subject to normal pricing on egress data. This is true even if you use this technique from another machine or cloud service within the same Azure datacenter, because traffic still goes through Azure's public load balancer.
>
### Connect to SQL Server in the same virtual network

[Virtual Network](..\virtual-network\virtual-networks-overview.md) enables additional scenarios. You can connect VMs in the same virtual network, even if those VMs exist in different cloud services. And with a [site-to-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md), you can create a hybrid architecture that connects VMs with on-premises networks and machines.

Virtual networks also enables you to join your Azure VMs to a domain. This is the only way to use Windows Authentication to SQL Server. The other connection scenarios require SQL Authentication with user names and passwords.

First, follow the [steps in this article to configure connectivity](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). 

>[AZURE.NOTE]If you are going to set up Windows Authentication, you do not need to use the steps in this article to configure SQL Authentication and logins. Also, a public endpoint is not required in this scenario.

Assuming that you have configured DNS, you can connect to your SQL Server instance by specifying the SQL Server VM name in the connection string. The following example assumes that Windows Authentication has also been configured and that the user has been granted access to the SQL Server instance.

	"Server=yoursqlvm;Integrated Security=true" 

Note that in this scenario, you could also specify the IP address of the VM.

## Steps for configuring SQL Server connectivity in an Azure VM

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

## Next Steps

To see provisioning instructions along with these connectivity steps, see [Provisioning a SQL Server Virtual Machine on Azure](virtual-machines-provision-sql-server.md).

If you are also planning to use AlwaysOn Availability Groups for high availability and disaster recovery, you should consider implementing a listener. Database clients connect to the listener rather than directly to one of the SQL Server instances. The listener routes clients to the primary replica in the availability group. For more information, see [Configure an ILB listener for AlwaysOn Availability Groups in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

It is important to review all of the security best practices for SQL Server running on an Azure virtual machine. For more information, see [Security Considerations for SQL Server in Azure Virtual Machines](https://msdn.microsoft.com/library/azure/dn133147.aspx).

For other topics related to running SQL Server in Azure VMs, see [SQL Server on Azure Virtual Machines](virtual-machines-sql-server-infrastructure-services.md).