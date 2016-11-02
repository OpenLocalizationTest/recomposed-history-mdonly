<properties
   pageTitle="Deploy Multiple Instances of Resources | Microsoft Azure"
   description="Use copy operation and arrays in an Azure Resource Manager template to iterate multiple times when deploying resources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/01/2016"
   ms.author="tomfitz"/>

# Create multiple instances of resources in Azure Resource Manager

This topic shows you how to iterate in your Azure Resource Manager template to create multiple instances of a resource.

## copy, copyIndex, and length

Within the resource to create multiple times, you can define a **copy** object that specifies the number of times to iterate. The copy takes the following format:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

You can access the current iteration value with the **copyIndex()** function. The following example uses copyIndex with the concat function to construct a name.

    [concat('examplecopy-', copyIndex())]

When creating multiple resources from an array of values, you can use the **length** function to specify the count. You provide the array as the parameter to the length function.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## Use index value in name

You can use the copy operation create multiple instances of a resource that are uniquely named based on the incrementing index. For example, you might want to add a unique number to the end of each resource name that is deployed. To deploy three web sites named:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

Use the following template:

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## Offset index value

In the preceding example, the index value goes from zero to 2. To offset the index value, you can pass a value in the **copyIndex()** function, such as **copyIndex(1)**. The number of iterations to perform is still specified in the copy element, but the value of copyIndex is offset by the specified value. So, using the same template as the previous example, but specifying **copyIndex(1)** would deploy three web sites named:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Use copy with array
   
The copy operation is helpful when working with arrays because you can iterate through each element in the array. To deploy three web sites named:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

Use the following template:

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Of course, you can set the copy count to a value other than the length of the array. For example, you could create an array with many values, and then pass in a parameter value that specifies how many of the array elements to deploy. In that case, you set the copy count as shown in the first example. 

## Depending on resources in a loop

You can specify that a resource is deployed after another resource by using the **dependsOn** element. To deploy a resource that depends on the collection of resources in a loop, provide the name of the copy loop in the **dependsOn** element. The following example shows how to deploy three storage accounts before deploying the Virtual Machine. The full Virtual Machine definition is not shown. Notice that the 
copy element has **name** set to **storagecopy** and the **dependsOn** element for the Virtual Machines is also set to **storagecopy**.

    {
	    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "contentVersion": "1.0.0.0",
	    "parameters": {},
	    "resources": [
	        {
		        "apiVersion": "2015-06-15",
		        "type": "Microsoft.Storage/storageAccounts",
		        "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
		        "location": "[resourceGroup().location]",
		        "properties": {
                    "accountType": "Standard_LRS"
            	 },
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

## Looping on a nested resource

You cannot use a copy loop for a nested resource. To create multiple instances of a resource that you typically define as nested within another resource, you must instead create that resource as a top-level resource. You define the relationship with the parent resource through the **type** and **name** properties.

For example, suppose you typically define a dataset as a nested resource within a Data Factory.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
To create multiple instances of datasets, you need to change your template as shown in the following example. Notice the dataset is no longer defined within the data factory, and the **type** and **name** have changed significantly. You must provide the fully qualified type in the format **{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}** because the type cannot be inferred from its position in the template. In this example, the type is Microsoft.DataFactory/datafactories/datasets. You also must provide a name that includes the parent resource name. The format of the name is **{parent-resource-name}/{child-resource-name}**.  

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## Create multiple instances when copy won't work

You can only use **copy** on resource types, not on properties within a resource type. This requirement may create problems for you when you want to create multiple instances of something that is part of a resource. A common scenario is to create multiple data disks for a Virtual Machine. You cannot use **copy** with the data disks because **dataDisks** is a property on the Virtual Machine, not its own resource type. Instead, you create an array with as many data disks as you need, and pass in the actual number of data disks to create. In the virtual machine definition, you use the **take** function to get only the number of elements that you actually want from the array.

A full example of this pattern is show in the [Create a VM with a dynamic selection of data disks](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) template.

The relevant sections of the deployment template are shown in the following example. Much of the template has been removed to highlight the sections involved in dynamically creating a number of data disks. Notice the parameter **numDataDisks** that enables you to pass in the number of disks to create. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

## Return values from a loop

While creating multiple instances of a resource type is convenient, returning values from that loop can be difficult. One way to retain and return values is to use **copy** with a nested template and round trip an array that contains all the values to return. For example, suppose you want to create multiple storage accounts, and return the primary endpoint for each one. 

First, create the nested template that creates the storage account. Notice that it accepts an array parameter for the blob URIs. You use this parameter to round trip all the values from previous deployments. The output of the template is an array that concatenates the new blob URI to the previous URIs.

```
{
	"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	"contentVersion": "1.0.0.0",
  	"parameters": {
    		"indexValue": {
      			"type":"int"
    		},
    		"blobURIs": {
      			"type": "array",
      			"defaultValue": []
    		}
  	},
	"variables": {
    		"storageName": "[concat('storage', uniqueString(resourceGroup().id), parameters('indexValue'))]"
  	},
	"resources": [
    	{
      		"apiVersion": "2016-01-01",
      		"type": "Microsoft.Storage/storageAccounts",
      		"name": "[variables('storageName')]",
      		"location": "[resourceGroup().location]",
      		"sku": {
        		"name": "Standard_LRS"
      		},
      		"kind": "Storage",
      		"properties": {
        
      		}
    	}
	],
	"outputs": {
    		"result": {
      			"type": "array",
      			"value": "[concat(parameters('blobURIs'),split(reference(variables('storageName')).primaryEndpoints.blob, ','))]"
    		}
  	}
}
```

Now, create the parent template that has one static instance of the nested template, and loops over the remaining instances of the nested template. For each instance of the looped deployment, pass an array that is the output of the previous deployment.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "numberofStorage": { "type": "int", "minValue": 2 }
    },
    "resources": [
      {
        "apiVersion": "2016-09-01",
        "name": "nestedTemplate0",
        "type": "Microsoft.Resources/deployments",
        "properties": {
          "mode": "incremental",
          "templateLink": {
            "uri": "{storage-template-uri}",
            "contentVersion": "1.0.0.0"
          },
          "parameters": {
            "indexValue": {"value": 0}
          }
        }
      },
      {
        "apiVersion": "2016-09-01",
        "name": "[concat('nestedTemplate', copyIndex(1))]",
        "type": "Microsoft.Resources/deployments",
        "copy": {
          "name": "storagecopy",
          "count": "[sub(parameters('numberofStorage'), 1)]"
        },
        "properties": {
          "mode": "incremental",
          "templateLink": {
            "uri": "{storage-template-uri}",
            "contentVersion": "1.0.0.0"
          },
          "parameters": {
            "indexValue": {"value": "[copyIndex(1)]"},
            "blobURIs": {"value": "[reference(concat('nestedTemplate', copyIndex())).outputs.result.value]"}
          }
        }
      }
    ],
    "outputs": {
      "result": {
        "type": "object",
        "value": "[reference(concat('nestedTemplate', sub(parameters('numberofStorage'), 1))).outputs.result]"
      }
    }
}
```

## Next steps
- If you want to learn about the sections of a template, see [Authoring Azure Resource Manager Templates](./resource-group-authoring-templates.md).
- For all the functions you can use in a template, see [Azure Resource Manager Template Functions](./resource-group-template-functions.md).
- To learn how to deploy your template, see [Deploy an application with Azure Resource Manager Template](resource-group-template-deploy.md).
