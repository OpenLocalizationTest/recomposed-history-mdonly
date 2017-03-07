---
title: Automation resources in OMS solutions | Microsoft Docs
description: Solutions in OMS will typically include runbooks in Azure Automation to automate processes such as collecting and processing monitoring data.  This article describes how to include runbooks and their related resources in a solution.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: bwren

ms.custom: H1Hack27Feb2017

---
# Adding Azure Automation resources to an OMS management solution (Preview)
> [!NOTE]
> This is preliminary documentation for creating management solutions in OMS which are currently in preview. Any schema described below is subject to change.   
> 
> 

[Management solutions in OMS](operations-management-suite-solutions.md) will typically include runbooks in Azure Automation to automate processes such as collecting and processing monitoring data.  In addition to runbooks, Automation accounts includes assets such as variables and schedules that support the runbooks used in the solution.  This article describes how to include runbooks and their related resources in a solution.

> [!NOTE]
> The samples in this article use parameters and variables that are either required or common to management solutions  and described in [Creating management solutions in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md) 
> 
> 

## Prerequisites
This article assumes that you're already familiar with how to [create a management solution](operations-management-suite-solutions-creating.md) and the structure of a solution file.

## Samples
You can get sample Resource Manager templates for Automation resources from the [QuickStart templates in GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## Automation account
All resources in Azure Automation are contained in an [Automation account](../automation/automation-security-overview.md#automation-account-overview).  As described in [OMS workspace and Automation account](operations-management-suite-solutions.md#oms-workspace-and-automation-account) the Automation account isn't included in the management solution but must exist before the solution is installed.  If it isn't available, then the solution install will fail.

The name of their Automation account is in the name of each Automation resource.  This is done in the solution with the **accountName** parameter as in the following example of a runbook resource.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## Runbooks
You can include any runbooks in the solution file so that their created when the solution is installed.  You cannot contain the body of the runbook in the template though, so you should publish the runbook to a public location where it can be accessed by any user installing your solution.

[Azure Automation runbook](../automation/automation-runbook-types.md) resources have a type of **Microsoft.Automation/automationAccounts/runbooks** and have the following structure. 

	{
		"name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
		"type": "Microsoft.Automation/automationAccounts/runbooks",
		"apiVersion": "[variables('AutomationApiVersion')]",
		"dependsOn": [
		],
		"location": "[parameters('regionId')]",
		"tags": { },
		"properties": {
			"runbookType": "[variables('Runbook').Type]",
			"logProgress": "true",
			"logVerbose": "true",
			"description": "[variables('Runbook').Description]",
			"publishContentLink": {
				"uri": "[variables('Runbook').Uri]",
				"version": "1.0.0.0"
			}
		}
	}


The properties for runbooks are described in the following table.

| Property | Description |
|:--- |:--- |
| runbookType |Specifies the types of the runbook. <br><br> Script - PowerShell script <br>PowerShell - PowerShell workflow <br> GraphPowerShell - Graphical PowerShell script runbook <br> GraphPowerShellWorkflow - Graphical PowerShell workflow runbook |
| logProgress |Specifies whether [progress records](../automation/automation-runbook-output-and-messages.md) should be generated for the runbook. |
| logVerbose |Specifies whether [verbose records](../automation/automation-runbook-output-and-messages.md) should be generated for the runbook. |
| description |Optional description for the runbook. |
| publishContentLink |Specifies the content of the runbook. <br><br>uri - Uri to the content of the runbook.  This will be a .ps1 file for PowerShell and Script runbooks, and an exported graphical runbook file for a Graph runbook.  <br> version - Version of the runbook for your own tracking. |


### Starting a runbook
There are two methods to start a runbook in a management solution.

* To start the runbook when the solution is installed, create a [job resource](#automation-jobs) as described below.
* To start the runbook on a schedule, create a [schedule resource](#schedules) as described below. 

## Automation jobs
When you start a runbook in Azure Automation, it creates an automation job.  You can add an automation job resource to your solution to start a runbook when the management solution is installed.  Job resources have a type of **Microsoft.Automation/automationAccounts/jobs** and have the following structure.

    {
        "name": "<name-of-job-resource>",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "<api-version-of-resource>",
        "location": "<resource-group-region>",
        "dependsOn": [
            "<name-of-runbook>",
            "<name-of-previous-jobs>"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "<name-of-runbook>"
            },
            "parameters": {
                "<runbook-parameter-values>"
            }
        }
    }

The properties for automation jobs are described in the following table.

| Property | Description |
|:--- |:--- |
| runbook |Single **name** entity with the name of the runbook to start. |
| parameters |Entity for each parameter value required by the runbook. |

The job includes the runbook name and any parameter values to be sent to the runbook.  The job must [depend on](operations-management-suite-solutions-solution-file.md#resources) the runbook that it's starting since the runbook must be created before the job.  You also create dependencies on other jobs for runbooks that should be completed before the current one.

The name of a job resource must contain a GUID which is typically assigned by a parameter.  You can read more about GUID parameters in [Creating solutions in Operations Management Suite (OMS)](operations-management-suite-solutions-solution-file.md#parameters).  


## Certificates
[Azure Automation certificates](../automation/automation-certificates.md) have a type of **Microsoft.Automation/automationAccounts/certificates** and have the following structure.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



The properties for Certificates resources are described in the following table.

| Property | Description |
|:--- |:--- |
| base64Value |Base 64 value for the certificate. |
| thumbprint |Thumbprint for the certificate. |



## Credentials
[Azure Automation credentials](../automation/automation-credentials.md) have a type of **Microsoft.Automation/automationAccounts/credentials** and have the following structure.  


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

The properties for Credential resources are described in the following table.

| Property | Description |
|:--- |:--- |
| userName |User name for the credential. |
| password |Password for the credential. |


## Schedules
[Azure Automation schedules](../automation/automation-schedules.md) have a type of **Microsoft.Automation/automationAccounts/schedules** and have the the following structure.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

The properties for schedule resources are described in the following table.

| Property | Description |
|:--- |:--- |
| description |Optional description for the schedule. |
| startTime |Specifies the start time of a schedule as a DateTime object. A string can be provided if it can be converted to a valid DateTime. |
| isEnabled |Specifies whether the schedule is enabled. |
| interval |The type of interval for the schedule.<br><br>day<br>hour |
| frequency |Frequency that the schedule should fire in number of days or hours. |

The challenge with including schedules in a solution file is the start time which must be a value greater than the current time.  You cannot provide this value with a variable since you would have no way of knowing when it's going to be installed.

Use one of the following two strategies when using schedule resources in a solution.

- Use a parameter for the start time of the schedule.  This will prompt the user to provide a value when they install the solution.  If you have multiple schedules, you could use a single parameter value for more than one of them.
- Create the schedules using a runbook that starts when the solution is installed.  This removes the requirement of the user to specify a time, but you can't contain the schedule in your solution so it will be removed when the solution is removed.


### Schedule links

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }




## Variables
Variables 

[Azure Automation variables](../automation/automation-variables.md) have a type of **Microsoft.Automation/automationAccounts/variables** and have the following structure.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

The properties for variable resources are described in the following table.

| Property | Description |
|:--- |:--- |
| description | Optional description for the variable. |
| isEncrypted | Specifies whether the variable should be encrypted. |
| type | Data type for the variable. |
| value | Value for the variable. |

## Modules
Your management solution does not need to define [global modules](../automation/automation-integration-modules.md) used by your runbooks because they will always be available.  You do need to include a resource for any other module used by your runbooks, and the runbook should depend on the module resource to ensure that it's created before the runbook.

[Integration modules](../automation/automation-integration-modules.md) have a type of **Microsoft.Automation/automationAccounts/modules** and have the properties in the following table.


An example of a module resource is below.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


The properties for module resources are described in the following table.

| Property | Description |
|:--- |:--- |
| contentLink |Specifies the content of the module. <br><br>uri - Uri to the content of the module.  This will be a .ps1 file for PowerShell and Script runbooks, and an exported graphical runbook file for a Graph runbook.  <br> version - Version of the module for your own tracking. |

### 

### Updating modules
If you update a management solution that includes a runbook that uses a schedule, and the new version of your solution has a new module used by that runbook, then the runbook may use the old version of the module.  You should include the following runbooks in your solution and create a job to run them before any other runbooks.  This will ensure that any modules are updated as required before the runbooks are loaded.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) will ensure that all of the modules used by runbooks in your solution are the latest version.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) will reregister all of the schedule resources to ensure that the runbooks linked to them with use the latest modules.

Following is a sample of the required elements of a solution to support the module update.

    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]




## Sample







## Next steps
* [Add a view to your solution](operations-management-suite-solutions-resources-views.md) to visualize collected data.

