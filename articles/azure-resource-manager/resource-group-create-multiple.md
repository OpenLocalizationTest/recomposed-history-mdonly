---
title: Deploy multiple instances of Azure resources | Microsoft Docs
description: Use copy operation and arrays in an Azure Resource Manager template to iterate multiple times when deploying resources.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''

ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: tomfitz

---
# Deploy multiple instances of a resource or property in Azure Resource Manager templates
This topic shows you how to iterate in your Azure Resource Manager template to create multiple instances of a resource, or multiple values for a property on the resource.

## Resource iteration
To create multiple instances of a resource type, add a `copy` element to the resource type. In the copy element, you specify the number of iterations and a name for this loop. The count value must be a positive integer and cannot exceed 800. Resource Manager creates the resources in parallel. Therefore, the order in which they are created is not guaranteed. To create iterated resources in sequence, see [Sequential looping for Azure Resource Manager templates](resource-manager-sequential-loop.md). 

The resource to create multiple times takes the following format:

```json
{
	"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	"contentVersion": "1.0.0.0",
	"resources": [
		{
			"apiVersion": "2016-01-01",
			"type": "Microsoft.Storage/storageAccounts",
			"name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
			"location": "[resourceGroup().location]",
			"sku": {
				"name": "Standard_LRS"
			},
			"kind": "Storage",
			"properties": {},
			"copy": {
				"name": "storagecopy",
				"count": 3
			}
		}
	],
	"outputs": {}
}
```

Notice that the name of each resource includes the `copyIndex()` function, which returns the current iteration in the loop. `copyIndex()` is zero-based. So, the following example:

```json
"name": "[concat('storage', copyIndex())]",
```

Creates these names:

* storage0
* storage1
* storage2.

To offset the index value, you can pass a value in the copyIndex() function. The number of iterations to perform is still specified in the copy element, but the value of copyIndex is offset by the specified value. So, the following example:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Creates these names:

* storage1
* storage2
* storage3

The copy operation is helpful when working with arrays because you can iterate through each element in the array. TUse the `length` function on the array to specify the count for iterations, and `copyIndex` to retrieve the current index in the array. So, the following example:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Creates these names:

* storagecontoso
* storagefabrikam
* storagecoho

## Property iteration

To create multiple values for a property on a resource, add a `copies` array in the properties element. This array contains objects, and each object has the following properties:

* name - the name of the property to create multiple values for
* count - the number of values to create
* input - an object that contains the values to assign to the property  

The following example shows how to apply `copies` to the dataDisks property on a virtual machine:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2016-04-30-preview",
  "properties": {
    "storageProfile": {
      "copies": [{
          "name": "dataDisks",
          "count": 3,
          "input": {
              "lun": "[copyIndex('dataDisks')]",
              "name": "[concat('myDataDisk', copyIndex('dataDisks',1))]",
              "vhd": {
                  "uri": "[concat('http://mystorage.blob.core.windows.net/vhds/mydatadisk',copyIndex('dataDisks', 1), '.vhd')]"
              },
              "caching": "ReadOnly",
              "createOption": "Empty",
              "diskSizeGB": 1
          }
      }],
      ...
```

Resource Manager expands the `copies` array during deployment. The name of the array becomes the name of the property. The input values become the object properties. The deployed template becomes:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2016-04-30-preview",
  "properties": {
    "storageProfile": {
      "dataDisks": [
          {
              "lun": 0,
              "name": "myDataDisk1",
              "vhd": {
                  "uri": "http://mystorage.blob.core.windows.net/vhds/mydatadisk1.vhd"
              },
              "caching": "ReadOnly",
              "createOption": "Empty",
              "diskSizeGB": 1
          },
          {
              "lun": 1,
              "name": "myDataDisk2",
              "vhd": {
                  "uri": "http://mystorage.blob.core.windows.net/vhds/mydatadisk2.vhd"
              },
              "caching": "ReadOnly",
              "createOption": "Empty",
              "diskSizeGB": 1
          },
          {
              "lun": 2,
              "name": "myDataDisk3",
              "vhd": {
                  "uri": "http://mystorage.blob.core.windows.net/vhds/mydatadisk3.vhd"
              },
              "caching": "ReadOnly",
              "createOption": "Empty",
              "diskSizeGB": 1
          }
      }],
      ...
```

Notice that when using `copyIndex` inside a property iteration, you must provide the name of the iteration. You do not have to provide the name when used with resource iteration.

You can use resource and property iteration together. You reference either iteration by name.

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2016-04-30-preview",
  "copy": {
      "name": "vmcopy",
      "count": 5
  },
  "properties": {
    "storageProfile": {
      "copies": [{
          "name": "dataDisks",
          "count": 3,
          "input": {
              "lun": "[copyIndex('dataDisks')]",
              "name": "[concat('vm', copyIndex('vmcopy',1),'myDataDisk', copyIndex('dataDisks',1))]",
              "vhd": {
                  "uri": "[concat('http://mystorage.blob.core.windows.net/vhds/vm', copyIndex('vmcopy',1), 'mydatadisk',copyIndex('dataDisks', 1), '.vhd')]"
              },
              "caching": "ReadOnly",
              "createOption": "Empty",
              "diskSizeGB": 1
          }
      }],
      ...
```

## Depend on resources in a loop
You specify that a resource is deployed after another resource by using the `dependsOn` element. To deploy a resource that depends on the collection of resources in a loop, provide the name of the copy loop in the dependsOn element. The following example shows how to deploy three storage accounts before deploying the Virtual Machine. The full Virtual Machine definition is not shown. Notice that the copy element has name set to `storagecopy` and the dependsOn element for the Virtual Machines is also set to `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
			"apiVersion": "2016-01-01",
			"type": "Microsoft.Storage/storageAccounts",
			"name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
			"location": "[resourceGroup().location]",
			"sku": {
				"name": "Standard_LRS"
			},
			"kind": "Storage",
			"properties": {},
			"copy": {
				"name": "storagecopy",
				"count": 3
			}
		},
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## Create multiple instances of a child resource
You cannot use a copy loop for a child resource. To create multiple instances of a resource that you typically define as nested within another resource, you must instead create that resource as a top-level resource. You define the relationship with the parent resource through the type and name properties.

For example, suppose you typically define a dataset as a child resource within a data factory.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

To create multiple instances of data sets, move it outside of the data factory. The dataset must be at the same level as the data factory, but it is still a child resource of the data factory. You preserve the relationship between data set and data factory through the type and name properties. Since type can no longer be inferred from its position in the template, you must provide the fully qualified type in the format: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

To establish a parent/child relationship with an instance of the data factory, provide a name for the data set that includes the parent resource name. Use the format: `{parent-resource-name}/{child-resource-name}`.  

The following example shows the implementation:

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## Next steps
* If you want to learn about the sections of a template, see [Authoring Azure Resource Manager Templates](resource-group-authoring-templates.md).
* To create iterated resources in sequence, see [Sequential looping for Azure Resource Manager templates](resource-manager-sequential-loop.md).
* To learn how to deploy your template, see [Deploy an application with Azure Resource Manager Template](resource-group-template-deploy.md).

