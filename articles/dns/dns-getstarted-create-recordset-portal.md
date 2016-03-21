<properties
   pageTitle="Create a record set and records for a DNS Zone using the Azure portal | Microsoft Azure"
   description="How to create host records for Azure DNS and create record sets and records using the Azure portal"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/21/2016"
   ms.author="joaoma"/>


# Create DNS records using the Azure portal


> [AZURE.SELECTOR]
- [Azure Portal](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


After creating your DNS Zone, you need to add the DNS records for your domain.  To do this, you first need to understand DNS records and record sets.

## Understanding record sets and records

Each DNS record has a name and a type.

A _fully qualified_ name includes the zone name, whereas a _relative_ name does not.  For example, the relative record name ‘www’ in the zone ‘contoso.com’ gives the fully qualified record name ‘www.contoso.com’.

>[AZURE.NOTE] In Azure DNS, records are specified using relative names.

Records come in various types according to the data they contain. The most common type is an ‘**A**’ record, which maps a name to an IPv4 address.  Another type is an ‘**MX**’ record, which maps a name to a mail server.

Azure DNS supports all common DNS record types: A, AAAA, CNAME, MX, NS, SOA, SRV and TXT. Note that SPF records should be created using the TXT record type. See [This page](http://tools.ietf.org/html/rfc7208#section-3.1) for more information.

Sometimes you need to create more than one DNS record with a given name and type. For example, suppose the www.contoso.com web site is hosted on two different IP addresses. This requires two different A records, one for each IP address:

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

This is an example of a record set. Azure DNS manages DNS records using record sets. A record set is the collection of DNS records in a zone with the same name and the same type.  Most record sets contain a single record, but examples like the one above in which a record set contains more than one record are not uncommon. Records sets of type SOA and CNAME are an exception; the DNS standards do not permit multiple records with the same name for these types.

To create a record set in the apex of the zone, use the record name "@", including the quotation marks. This is a common DNS convention. To do this, you must use PowerShell. See [this article](dns-getstarted-create-recordset-portal.md) for more information.

The Time-to-Live, or TTL, specifies how long each record is cached by clients before being re-queried. In the above example, the TTL is 3600 seconds or 1 hour. The TTL is specified for the record set, not for each record, so the same value is used for all records within that record set.

## To create record sets and records

In the following example we will show how to create a record set and records.  We'll use the DNS 'A' record type, for other record types see [How to manage DNS records](dns-operations-recordsets.md)

1. Log in to the Azure portal.
2. Navigate to the DNS zone blade for which you want to create a record set.
3. In your DNS zone blade, at the top of the blade click Record set to open the Add record set blade.
4. In the Add record set blade, name your records set. For example, you could name your record set *www*.
5. Type, select the type of record you want to create. For example, A.
6. Set the TTL. The default in the portal is 1 hour.
7. Add the IP addresses, one IP address per line. This adds the IPv4 IP addresses to the A record for the www record set.
8. When you have finished added IP addresses, click OK. The DNS record set will create.


## To view your record set

It's very easy to view your DNS record set and records from the Azure portal.

1. In the Azure portal, navigate to your DNS zone blade.
2. You can search for the record set and select it from the listed items. Click the record set to select it. This will open the record set properties.

## To modify your record set

1. From the record set properties blade for your record set, modify the settings.
2. Save your settings at the top of the page before closing the blade.
3. In the corner, you will see that the record is saving and can view the results on the DNS zone blade.




## Next Steps

[How to manage DNS zones](dns-operations-dnszones.md)

[How to manage DNS records](dns-operations-recordsets.md)<BR>

[Automate Azure Operations with .NET SDK](dns-sdk.md)
 
