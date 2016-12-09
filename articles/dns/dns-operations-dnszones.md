---
title: Manage DNS zones using PowerShell | Microsoft Docs
description: You can manage DNS zones using Azure Powershell. How to update, delete and create DNS zones on Azure DNS
services: dns
documentationcenter: na
author: georgewallace
manager: timlt

ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: gwallace
---
# How to manage DNS Zones using PowerShell

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

<<<<<<< HEAD
This article will show you how to manage your DNS zone by using PowerShell. In order to use these steps, you'll need to install the latest version of the Azure Resource Manager PowerShell cmdlets. See [How to install and configure Azure PowerShell](../powershell-install-configure.md) for more information about installing the PowerShell cmdlets.
=======
This article will show you how to manage your DNS zone by using PowerShell. In order to use these steps, you'll need to install the latest version of the Azure Resource Manager PowerShell cmdlets (1.0 or later). See [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) for more information about installing the PowerShell cmdlets.
>>>>>>> refs/remotes/Microsoft/master

## Create a new DNS zone

A DNS zone is created by using the `New-AzureRmDnsZone` cmdlet. There are examples below for creating a DNS zone with or without [Azure Resource Manager tags](dns-zones-records.md#tags).

### To create a DNS zone

The example below creates a DNS zone called *contoso.com* in the resource group called *MyResourceGroup*.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### To create a DNS zone with tags

The following example shows how to create a DNS zone with two tags, *project = demo* and *env = test*. 

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

## Get a DNS zone

To retrieve a DNS zone, use the `Get-AzureRmDnsZone` cmdlet. This operation returns a DNS zone object corresponding to an existing zone in Azure DNS. The object contains data about the zone (such as the number of record sets), but does not contain the record sets themselves (see `Get-AzureRmDnsRecordSet`).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., 
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## List DNS zones

By omitting the zone name from `Get-AzureRmDnsZone`, you can enumerate all zones in a resource group. This operation returns an array of zone objects.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

By omitting both the zone name and the resource group name from `Get-AzureRmDnsZone`, you can enumerate all zones in the Azure subscription.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## Update a DNS zone

Changes to a DNS zone resource can be made by using `Set-AzureRmDnsZone`. This does not update any of the DNS record sets within the zone (see [How to Manage DNS records](dns-operations-recordsets.md)). It's only used to update properties of the zone resource itself. This is currently limited to the [Azure Resource Manager ‘tags’ for the zone resource](dns-zones-records.md#tags).

Use one of the following two ways to update DNS zone:

### Specify the zone using the zone name and resource group

This approach will replace the existing zone tags with the values specified.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### Specify the zone using a $zone object

This approach retrieves the existing zone object, modifies the tags, and then commits the changes. In this way, existing tags can be preserved.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved") 

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

When using `Set-AzureRmDnsZone` with a $zone object, [Etag checks](dns-zones-records.md#etags) will be used to ensure concurrent changes are not overwritten. You can use the optional `-Overwrite` switch to suppress these checks.

## Delete a DNS Zone

DNS zones can be deleted using the `Remove-AzureRmDnsZone` cmdlet.

> [!NOTE]
> Deleting a DNS zone will also delete all DNS records within the zone. This operation cannot be undone. If the DNS zone is in use, services using the zone will fail when the zone is deleted.
>
>To protect against accidental zone deletion, see [How to protect DNS zones and records](dns-protect-zones-recordsets.md).


Use one of the following two ways to remove a DNS zone:

### Specify the zone using the zone name and resource group name

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### Specify the zone using a $zone object

Specify the zone using a $zone object from `Get-AzureRmDnsZone`.

When using `Remove-AzureRmDnsZone` with a $zone object, [Etag checks](dns-zones-records.md#etags) will be used to ensure concurrent changes are not deleted. You can use the optional `-Overwrite` switch to suppress these checks.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

The zone object can also be piped instead of being passed as a parameter:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

As with `Set-AzureRmDnsZone`, specifying the zone using a $zone object enables Etag checks to ensure concurrent changes are not deleted. Use the `-Overwrite` switch to suppress these checks.

## Confirmation prompts

The `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` and `Remove-AzureRmDnsZone` cmdlets all support confirmation prompts.

Both `New-AzureRmDnsZone` and `Set-AzureRmDnsZone` will prompt for confirmation if the `$ConfirmPreference` PowerShell preference variable has a value of `Medium` or lower. Due to the potentially high impact of deleting a DNS zone, the `Remove-AzureRmDnsZone` cmdlet prompts for confirmation if the `$ConfirmPreference` PowerShell variable has any value other than `None`.

Since the default value for `$ConfirmPreference` is `High`, only `Remove-AzureRmDnsZone` will prompt for confirmation by default.

You can override the current `$ConfirmPreference` setting using the `-Confirm` parameter. If you specify `-Confirm` or `-Confirm:$True` , the cmdlet prompts you for confirmation before it runs. If you specify `-Confirm:$False` , the cmdlet does not prompt you for confirmation. 

For more information about `-Confirm` and `$ConfirmPreference`, see [About Preference Variables](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## Next steps

Learn how to [manage record sets and records](dns-getstarted-create-recordset.md) in your DNS zone.
<br>
Learn how to [delegate your domain to Azure DNS](dns-domain-delegation.md).

