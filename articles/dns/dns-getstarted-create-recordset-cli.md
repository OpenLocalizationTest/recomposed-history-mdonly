<properties
   pageTitle="Create a record set and records for a DNS Zone using CLI| Microsoft Azure"
   description="How to create host records for Azure DNS.Setting up record sets and records using CLI"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# Create DNS record sets and records using CLI

> [AZURE.SELECTOR]
- [Azure Portal](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


This article will walk you through creating records and records sets by using CLI. After creating your DNS Zone, you need to add the DNS records for your domain. To do this, you'll first need to understand DNS records and record sets.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)] 

## Create a record set and a record

In this section, we will show you how to create a record set and records. In this example, you will create a record set has relative name *www* in the DNS Zone *contoso.com*. The fully-qualified name of the records will be *www.contoso.com*. The record type is *A* and the TTL is 60 seconds. After completing this step, you will have created an empty record set.

To create a record set in the apex of the zone (in this case, 'contoso.com'), use the record name "@", including the quotation marks. This is a common DNS convention.

### 1. Create a record set

To create record set, use `azure network dns record-set create`. Specify the resource group, zone name, record set relative name, the record type, and time to live (TTL). If the --ttl parameter is not defined, the value defaults to 4 (in seconds). After completing this step, you will have an empty "www" record set. 
	
*Usage: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>*

	azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2. Add records

In order to be able to use the newly created *www* record set, you'll need to add records to it. Records are added to record sets using the `azure network dns record-set add-record`. 

The parameters for adding records to a record set vary depending on the type of the record set. For example, when using a record set of type *A*, you will only be able to specify records with the parameter `-a <IPv4 address>`. 

You can add IPv4 *A* records to the *www* record set using the following command:

*Usage: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>*

	azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## Additional record type examples

The following examples show how to create a record set of each record type, each containing a single record.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)] 

## Next Steps

To manage your record set and records, see [Manage DNS records and record sets using CLI](dns-operations-recordsets-portal.md).<!-- Loc Comment: This link title is for "CLI", but linked article is for Azure portal. So it seems that either the link title should be fixed to "Manage DNS records and record sets using the Azure portal" or linked article should be to fixed to "dns-operations-recordsets-cli". -->

For more information about Azure DNS, see the [Azure DNS Overview](dns-overview.md).
