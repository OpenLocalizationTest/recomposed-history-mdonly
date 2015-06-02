<properties 
	pageTitle="Deploy a web app that is linked to a GitHub repository" 
	description="" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="tomfitz"/>

# Deploy a logic app

In this topic, you will learn how to create an Azure Resource Manager template to create an empty logic app that can be used to define workflows.. You will learn how to define which resources are deployed and 
how to define parameters that are specified when the deployment is executed. You can use this template for your own deployments, or customize it to meet your requirements.

For more details on the Logic app properties, see [Logic App Workflow Management API](https://msdn.microsoft.com/library/azure/dn948513.aspx). 
For examples of the definition itself, see [Author Logic App definitions](https://azure.microsoft.com/documentation/articles/app-service-logic-author-definitions/). 

For more information about creating templates, see [Authoring Azure Resource Manager Templates](resource-group-authoring-templates.md).

For the complete template, see [Web App Linked to GitHub template](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WebAppLinkedToGithub.json).

## What you will deploy

With this template, you will provision a logic app.

## Parameters to specify

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }
    
## Resources to deploy

### App service plan

Creates an app service plan. 

It uses the same location as the resource group to which it is being deployed.

    {
        "apiVersion": "2014-06-01",
        "name": "[parameters('svcPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "AppServicePlan"
        },
        "properties": {
            "name": "[parameters('svcPlanName')]",
            "sku": "[parameters('sku')]",
            "workerSize": "[parameters('svcPlanSize')]",
            "numberOfWorkers": 1
        }
    }

### Logic app

Creates the logic app.

A Logic app requires a name, location, SKU (which points to that App service plan), definition, and, optionally, parameters.

This particular definition will run once an hour, and then ping the location that's at `testUri`. 

    {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-02-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "LogicApp"
        },
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "testURI": {
                        "type": "string",
                        "defaultValue": "[parameters('testUri')]"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "recurrence",
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                },
                "actions": {
                    "http": {
                        "type": "Http",
                        "inputs": {
                            "method": "GET",
                            "uri": "@parameters('testUri')"
                        }
                    }
                },
                "outputs": { }
            },
            "parameters": { }
        }
    }

## Commands to run deployment

[AZURE.INCLUDE [app-service-deploy-commands](../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/davidebbo/AzureWebsitesSamples/master/ARMTemplates/WebAppLinkedToGithub.json  -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/davidebbo/AzureWebsitesSamples/master/ARMTemplates/WebAppLinkedToGithub.json


