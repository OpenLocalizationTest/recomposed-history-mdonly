<properties
	pageTitle="What's new in Azure Stack | Microsoft Azure"
	description="What's new in Azure Stack"
	services="azure-stack"
	documentationCenter=""
	authors="HeathL17"
	manager="byronr"
	editor=""/>

<tags
	ms.service="azure-stack"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/26/2016"
	ms.author="helaw"/>

# What's new in Azure Stack Technical Preview 2
This release provides new features for both tenants and administrators.

## Network   
   - [Azure-provided DNS](azure-stack-what-is-idns.md) provides internal network name registration and Domain Name System (DNS) resolution without additional DNS infrastructure.
   - [Virtual network gateways](azure-stack-virtual-network-gateways.md) provide VPN connectivity options to Azure or on-premises resources.
   - User-defined routes can route network traffic through firewalls, security, other appliances, and other services.
   - You can create network resources from the Microsoft Azure Marketplace.   

## Storage
 - [Azure Queues](https://msdn.microsoft.com/library/dd179353.aspx) enable reliable and persistent service messaging.
 - [Storage analytics](https://msdn.microsoft.com/library/azure/hh343270.aspx) capture storage performance data. You can use this data to trace requests, analyze usage trends, and diagnose issues with your storage account.
 - Blob storage supports [append block operation](https://msdn.microsoft.com/library/azure/mt427365.aspx).
 - Premium Storage API account support.
 - Storage service support for common tools and SDKs, such as Azure CLI, PowerShell, .NET, Python, and Java SDK. 
 - [Storage Account Shared Access Signature](https://msdn.microsoft.com/en-us/library/azure/mt584140.aspx) enable granular delegation of access to your storage services without having to share your full account key.  
 - Storage services now use [Group Managed Service Accounts](https://technet.microsoft.com/library/hh831477(v=ws.11).aspx#BKMK_group_managed_sa) for strong security with low management overhead.
 - You can reclaim unused tenant resources on-demand.
 - Deleted storage account retention length is configurable.

## Compute
- You can deallocate virtual machines.
- You can redeploy virtual machine extensions for troubleshooting and configuration management purposes.
- You can resize virtual machine disks.
- Virtual machines can have multiple network interfaces.

## Key Vault
- [Azure Key Vault](azure-stack-intro-key-vault.md) provides secure management of your keys and passwords for cloud apps.
- You can audit and monitor key usage by apps and VMs.

## Billing and usage
 - Billing and consumption APIs expose data on how your services are consumed.  
 - You can capture plans and offers in Resource Manager templates.
 - Delegated Providers enable resellers to offer your Azure Stack services to their customers.

## Monitoring and health
 - New monitoring capabilities available in the portal and APIs allow you to proactively view and manage alerts on your environment.  
 - System Health Tests automatically test your fabric to ensure services are working as expected.   

## Next steps
- [Understand Azure Stack POC Architecture](azure-stack-architecture.md)      
- [Understand deployment prerequisites](azure-stack-deploy.md)
- [Deploy Azure Stack](azure-stack-run-powershell-script.md)

  
