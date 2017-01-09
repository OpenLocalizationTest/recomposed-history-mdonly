---
title: Azure Functions  | Microsoft Docs
description: 
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: ''
tags: ''
keywords: azure functions, functions, serverless architecture, infrastructure as code, azure resource manager

ms.assetid:
ms.server: functions
ms.devlang: multiple
ms.topic:
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/29/2016
ms.author: cfowler;glenga
---
# Automating Azure Functions Resource Deployment

In this topic, you will learn how to build an Azure Resource Manager template which will deploy a function app. You will learn how to define the baseline of resources required for an Azure Function and the parameters that are specified when the deployment is executed. Depending on the [triggers and bindings](./functions-triggers-bindings) that are used in your funciton, you may require deploying additional resources to encompass your entire application as infrastructure as code.

For more information about creating templates, see [Authoring Azure Resource Manager Templates](https://azure.microsoft.com/en-us/documentation/articles/resource-group-authoring-templates/)

For examples of a complete template, [Create consumption based Azure Function](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) and/or [Create an App Service Plan based Azure Function](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json)

## What you will deploy

With the examples below, you will be able to create a baseline Azure Function App. The resources required for an Function App are as follows:

* [Azure Storage](../storage/index) Account
* Hosting Plan (Consumption Plan or App Service Plan)
* Function App (Microsoft.Web/Site of kind `functionapp`)

## Parameters

With Azure Resource Manager, you define parameters for values you want to specify when the template is deployed. The template includes a section called Parameters that contains all of the parameter values. You should define parameters for those values that will vary based on the project you are deploying or based on the environment you are deploying to.

If you have common values which don't change as part of your deployment, or need to do some transformation on the values which are provided as parameter, it is useful to leverage the [Variables](../azure-resource-manager/resource-group-authoring-templates#variables) section of the template.

When defining parameters, use the **allowedValues** field to specify which values a user can provide during deployment. Use the **defaultValue** field to assign a value to the parameter, if no value is provided during deployment.

Let's describe the parameters for our template.

### appName

The name of the function you wish to create.

```json
"appName": {
    "type": "string"
}
```

### location

The location in which to deploy the Function App.

>[NOTE] The **defaultValue** parameter is used to inherit the location of the Resource Group, if a parameter value isn't specified during a Powershell or CLI deployment. If deploying from the Portal, a dropdown box will be provided to select from the **allowedValues**.

>[NOTE] For an up-to-date list of regions Azure Functions is available in visit the [Products available by region](https://azure.microsoft.com/en-us/regions/services/) page.

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### sourceCodeRepositoryURL (optional)

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### sourceCodeBranch (optional)

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Sourcecode Repo branch"
      }
    }
```

### sourceCodeManualIntegration (optional)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo, 'false' if you are deploying from your own fork. If you're using 'false', make sure you have admin permissions to the repo. If you get an error, you should add GitHub integration to another web app manually, so that you get a GitHub access token associated with your Azure Subscription."
    }
}
```

## Resources to Deploy

### Storage Account

An Azure Storage account is a required resource in Azure Functions.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### Fully Managed Scaling vs User Managed Scaling

There are scenarios when building functions in which you may want your functions to be scaled on-demand by the platform, or run

#### Consumption Plan

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

#### App Service Plan

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### Function App (Site)

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```

## Deploying your Template

* [Powershell](https://azure.microsoft.com/en-us/documentation/articles/resource-group-template-deploy/)
* [CLI](https://azure.microsoft.com/en-us/documentation/articles/resource-group-template-deploy-cli/)
* [Portal](https://azure.microsoft.com/en-us/documentation/articles/resource-group-template-deploy-portal/)
* [REST API](https://azure.microsoft.com/en-us/documentation/articles/resource-group-template-deploy-rest/)

### Deploy to Azure button

Replace ```<url-encoded-path-to-azuredeploy-json>``` with a [URL encoded](https://www.bing.com/search?q=url+encode)

#### markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```