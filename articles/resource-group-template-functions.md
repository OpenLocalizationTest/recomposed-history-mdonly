<properties
   pageTitle="Azure Resource Manager Template Functions"
   description="Describes the functions to use in an Azure Resource Manager template to deploy apps to Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="04/27/2015"
   ms.author="tomfitz;ilygre"/>

# Azure Resource Manager Template Functions

This topic describes all of the functions you can use in an Azure Resource Manager template.

## base64

**base64 (inputString)**

Returns the base64 representation of the input string.

| Parameter                          | Required | Description
| :--------------------------------: | :------: | :----------
| inputString                        |   Yes    | The string value to return as a base64 representation.

The following example show how to use the base64 function.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

## concat

**concat (arg1, arg2, arg3, ...)**

Combines multiple string values and returns the resulting string value. This function can take any number of arguments.

The following example shows how to combine multiple values to return a value.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## listKeys

**listKeys (resourceName or resourceIdentifier, [apiVersion])**

Returns the keys of a storage account. This function takes the resourceId and the API version as parameters. The resourceId can be specified by using the resourceId function described above or by using the format 'providerNamespace/resourceType/resourceName'). You can use the function to get the primaryKey and secondaryKey.
  
| Parameter                          | Required | Description
| :--------------------------------: | :------: | :----------
| resourceName or resourceIdentifier |   Yes    | Unique identifier of a storage account.
| apiVersion                         |   Yes    | API version of resource runtime state.

The following example shows how to return the keys from a storage account in the outputs section.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## parameters

**parameters (parameterName)**

Returns a parameter value. The specified parameter name must be defined in the parameters section of the template.

| Parameter                          | Required | Description
| :--------------------------------: | :------: | :----------
| parameterName                      |   Yes    | The name of the parameter to return.

The following example shows a simplified use of the parameters function.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

## provider

**provider (providerNamespace, [resourceType])**

Return information about a resource provider and its supported resource types. If not type is provided, all of the supported types are returned.

| Parameter                          | Required | Description
| :--------------------------------: | :------: | :----------
| providerNamespace                  |   Yes    | Namespace of the provider
| resourceType                       |   No     | The type of resource within the specified namespace.

Each supported type is returned in the following format:

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

The following example shows how to use the provider function:

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

## reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

Enables an expression to derive its value from another resource's runtime state.

| Parameter                          | Required | Description
| :--------------------------------: | :------: | :----------
| resourceName or resourceIdentifier |   Yes    | Name or the unique identifier of a resource.
| apiVersion                         |   No     | API version of resource runtime state. Parameter should be used if resource is not provisioned within same template.

The **reference** function derives its value from a runtime state, and therefore cannot be used in the variables section. It can be used in outputs section of a template.

By using the reference expression, you declare that one resource depends on another resource if the referenced resource is provisioned within same template.

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## resourceGroup

**resourceGroup()**

Returns a structured object (with id, name, and location properties) that represents the current resource group. The object will be in the following format:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

The following example uses the resource group location to assign the location for a web site.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

## resourceId

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

Returns the unique identifier of a resource. You use this function when the resource name is ambiguous or not provisioned within same template. The identifier is returned in the following format:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Parameter         | Required | Description
| :---------------: | :------: | :----------
| resourceGroupName |   No     | Optional resource group name. Default value is current resource group.
| resourceType      |   Yes    | Type of resource including resource provider namespace.
| resourceName1     |   Yes    | Name of resource.
| resourceName2     |   No     | Next resource name segment if resource is nested.

The following examples show how to retrieve the resource id for a web site and a database:

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]

## subscription

**subscription()**

Returns details about the subscription in the following format.

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

The following example shows the subscription function called in the outputs section. 

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 

## variables

**variables (variableName)**

Returns the value of variable. The specified variable name must be defined in the variables section of the template.

| Parameter                          | Required | Description
| :--------------------------------: | :------: | :----------
| variable Name                      |   Yes    | The name of the variable to return.


## Next Steps
- [Authoring Templates](./resource-group-authoring-templates.md)
- [Deploying Templates](./resouce-group-template-deploy.md)
- [Advanced Template Operations](./resource-group-advanced-template.md)
- [Azure Resource Manager Overview](./resource-group-overview.md)
