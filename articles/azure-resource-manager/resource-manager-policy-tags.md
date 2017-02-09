---
title: Azure resource policies for tags | Microsoft Docs
description: Provides examples of resource policies for managing tags on resources
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2017
ms.author: tomfitz

---
# Apply resource policies for tags

This topic provides common policy rules you can apply to ensure consistent use of tags on resources.

Applying a tag policy to a resource group or subscription with existing resources does not retroactively apply the policy to those resources. You must trigger an update to the existing resources to enforce the policies on those resources.

## Ensure all resources in a resource group have a tag/value

A common requirement is that all resources in a resource group have a particular tag and value. This requirement is often needed to track costs by department. The following conditions must be met:

* The required tag and value are appended to new and updated resources that do not have any existing tags.
* The required tag and value are appended to new and updated resources that have other tags, but not the required tag and value.
* The required tag and value cannot be removed from any existing resources.

You accomplish this requirement by applying the following three policies ([Append tag](#append-tag), [Append tag with other tags](#append-tag-with-other-tags), [Require tag and value](#require-tag-and-value)) to a resource group. 

### Append tag

The following policy rule appends costCenter tag with a predefined value when no tags are present:

```json
{
  "if": {
    "field": "tags",
    "exists": "false"
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags",
        "value": {"costCenter":"myDepartment" }
      }
    ]
  }
}
```

### Append tag with other tags

The following policy rule appends costCenter tag with a predefined value when tags are present, but the costCenter tag is not defined:

```json
{
  "if": {
    "allOf": [
      {
        "field": "tags",
        "exists": "true"
      },
      {
        "field": "tags.costCenter",
        "exists": "false"
      }
    ]
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags.costCenter",
        "value": "myDepartment"
      }
    ]
  }
}
```

### Require tag and value

The following policy rule denies update or creation of resources that do not have the costCenter tag assigned to the predefined value.

```json
{
  "if": {
    "not": {
      "field": "tags.costCenter",
      "equals": "myDepartment"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## Require tags for a resource type
The following example shows how to nest logical operators to require an application tag for only a specified resource type (in this case, storage accounts).

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## Require tag
The following policy denies requests that don't have a tag containing "costCenter" key (any value can be applied):

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## Trigger updates to existing resources

The following PowerShell script triggers an update to existing resources to enforce tag policies you have added.

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($_.Tags -eq $NULL) { @{}} else {$_.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## Next steps
* For an introduction to resource policies, see [Resource policy overview](resource-manager-policy.md).
* For storage policies, see [Resource policies for storage accounts](resource-manager-policy-storage.md).
* For Linux VM policies, see [Apply security and policies to Linux VMs with Azure Resource Manager](../virtual-machines/virtual-machines-linux-policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json).
* For Windows VM policies, see [Apply security and policies to Windows VMs with Azure Resource Manager](../virtual-machines/virtual-machines-windows-policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json.)
* For guidance on how enterprises can use Resource Manager to effectively manage subscriptions, see [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md).

