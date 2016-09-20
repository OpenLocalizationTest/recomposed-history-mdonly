<properties 
	pageTitle="Configure a connection from an Azure Search indexer to SQL Server on an Azure virtual machine | Microsoft Azure | Indexers" 
	description="Enable encrypted connections and configure the firewall to allow connections to SQL Server on an Azure virtual machine (VM) from an indexer on Azure Search." 
	services="search" 
	documentationCenter="" 
	authors="jack4it" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/19/2016" 
	ms.author="jackma"/>

# Configure a connection from an Azure Search indexer to SQL Server on an Azure VM

As noted in [Connecting Azure SQL Database to Azure Search using indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), creating indexers against **SQL Server on Azure VMs** (or **SQL Azure VMs** for short) is supported by Azure Search, but there are two security-related prerequisites to take care of first. 

**Task Duration:** Approximately 30 minutes, assuming you already obtained and installed an SSL certificate for the VM.

## Enable encrypted connections

The first prerequisite is to enable encrypted connections from Azure Search to the SQL Azure VM. Azure Search requires an encrypted channel for all requests over a public internet connection. In order for the connection to succeed, the connection must be encrypted using an SSL certificate; the indexer won't retrieve data without it. This section lists the steps to make this work.

1. Verify the subject name of the certificate is the fully qualified domain name (FQDN) of the Azure VM. You can find the FQDN of the VM as the **DNS name/label** in the [Azure portal](https://portal.azure.com/).

  For VMs created using the newer **Resource Manager** template, the FQDN is formatted as `<your-VM-name>.<region>.cloudapp.azure.com`. 

  For older VMs created as a **Classic** VM, the FQDN is formatted as `<your-cloud-service-name.cloudapp.net>`. 

2. Configure SQL Server to use the certificate using the Registry Editor (regedit). 

  > [AZURE.NOTE] Although SQL Server Configuration Manager is typically used for this task, you can't use it for this scenario. Configuration Manager won't find the imported certificate because the FQDN of the VM isn't the same as the FQDN as determined by the VM (where the domain is either the local computer itself, or the network domain to which it is joined). When the names don't match, you must use regedit to specify the certificate.

  a. In regedit, browse to this registry key: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`. The `[MSSQL13.MSSQLSERVER]` part varies based on your SQL Server version and instance name. 

  b. Set the value of the **Certificate** key to the **thumbprint** of the SSL certificate you imported to the VM.

  > [AZURE.IMPORTANT] There are several ways to get the thumbprint, some better than others. If you copy it from the **Certificates** snap-in in MMC, you will probably pick up an invisible leading character [as described in this support article](https://support.microsoft.com/kb/2023869/), which results in an error when you attempt a connection. Several workarounds exist for correcting this problem. The easiest is to backspace over and then retype the first character of the thumbprint to remove the leading character in the key value field in regedit. Alternatively, you can use a different tool to copy the thumbprint.

3. Grant permissions to the service account. 

  Make sure the SQL Server service account is granted appropriate permission on the private key of the SSL certificate. If you overlook this step, SQL Server will not start. You can use the **Certificates** snap-in or **CertUtils** for this task.

4. Restart the SQL Server service.

## Configure SQL Server connectivity in the VM

After you set up the encrypted connection required by Azure Search, there are additional configuration steps intrinsic to SQL Server on Azure VMs. The next step is to finish configuration using either one of these articles:

- For a **Resource Manager** VM, see [Connect to a SQL Server Virtual Machine on Azure using Resource Manager](../virtual-machines/virtual-machines-windows-sql-connect.md). 

- For a **Classic** VM, see [Connect to a SQL Server Virtual Machine on Azure Classic](../virtual-machines/virtual-machines-windows-classic-sql-connect.md).

In particular, review the connection scenarios in each article for "connecting over the internet".


## Configure the Network Security Group (NSG) to allow connections from Azure Search

It is not unusual to configure the NSG and corresponding Azure endpoint or Access Control List (ACL) to make your Azure VM accessible to other parties. Chances are you've done this before to allow your own application logic to connect to your SQL Azure VM. It's no different for an Azure Search connection to your SQL Azure VM. If you haven't done this yet, this section covers a few good security practices to keep in mind.

> [AZURE.NOTE] For background, see [What is a Network Security Group?](../virtual-machines/virtual-networks-nsg.md). You can also refer to [How to create NSGs for ARM deployments](../virtual-machines/virtual-networks-create-nsg-arm-pportal.md) and [How to create NSGs for Classic deployments](../virtual-machines/virtual-networks-create-nsg-classic-ps.md) for instructions. The following recommendations assume you already understand your NSG configuration and how to ACL an Azure Search endpoint based on its IP address.

#### Restrict access to the search service IP address

For either VM deployment model (**Resource Manager** or **Classic**), we strongly recommend that you restrict the access to the IP address of your search service in the ACL instead of making your SQL Azure VMs wide open to any connection requests. You can easily find out the IP address by pinging the FQDN (for example, `<your-search-service-name>.search.windows.net`) of your search service.

#### Managing IP address fluctuations

If your search service has only one search unit (that is, one replica and one partition), the IP address may change during routine service restarts. This disrupts connections for ACLs having your search service's IP address.

One way to avoid connectivity failure is to use more than one replica and one partition in Azure Search. Doing so increases the cost, but it also solves the IP address problem. In Azure Search, IP addresses don't change when you have more than one search unit.

A second approach is to allow the connection to fail, and then reconfigure the ACLs in the NSG. Although there is no guarantee, on average IP addresses change every few weeks. If indexing is always planned and occurs infrequently, this approach might be viable.

A third viable-but-not-secure approach is to specify the IP address range of the Azure region where your search service is provisioned. The list of IP ranges from which public IP addresses are allocated to Azure resources is published at [Azure Datacenter IP ranges](https://www.microsoft.com/download/details.aspx?id=41653). 

#### Include the Azure Search portal IP addresses

Additionally, if you are using the Azure portal to create an indexer, Azure Search portal logic also needs access to your SQL Azure VM during creation time. Azure search portal IP addresses can be found by pinging `stamp1.search.ext.azure.com` and `stamp2.search.ext.azure.com`.

## Next steps

With configuration out of the way, you can now specify a SQL Server on Azure VM as the data source for an Azure Search indexer. See [Connecting Azure SQL Database to Azure Search using indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) for more information.
