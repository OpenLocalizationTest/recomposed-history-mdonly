<properties
   pageTitle="Common resources in OMS custom solutions | Microsoft Azure"
   description="Many custom solutions in OMS will include runbooks in Azure Automation to automate processes such as collecting and processing monitoring data.  This article describes how to include runbooks and their related resources in a custom solution."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/30/2016"
   ms.author="bwren" />

# Automation resources in OMS custom solutions (Preview)

>[AZURE.NOTE]This is preliminary documentation for custom solutions in OMS which are currently in preview. The schema described below is subject to change.     

[Custom solutions in OMS](operations-management-suite-custom-solutions.md) will typically include runbooks in Azure Automation to automate processes such as collecting and processing monitoring data.  In addition to runbooks, Automation accounts includes assets such as variables and schedules that support the runbooks used in the solution.  This article describes how to include runbooks and their related resources in a custom solution.

>[AZURE.NOTE]The samples in this article use parameters and variables that are either required or common to solutions  and described in [Custom solutions in Operations Management Suite (OMS)](operations-management-suite-custom-solutions.md) 

>[AZURE.NOTE]You can get sample Resource Manager templates for Automation resources from the [QuickStart templates in GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## Automation account
All resources in Azure Automation are contained in an [Automation account](../automation/automation-security-overview.md#automation-account-overview).  As described in [OMS workspace and Automation account](operations-management-suite-custom-solutions.md#oms-workspace-and-automation-account) the Automation account isn't included in the solution and but exist before the solution is installed.  If it isn't available, then the solution install will fail.

The name of their Automation account is in the name of each Automation resource.  This is done in the solution with the **accountName** parameter as in the following.
	
	"name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## Runbooks
[Azure Automation runbook](../automation/automation-runbook-types.md) resources have a type of **Microsoft.Automation/automationAccounts/runbooks** and have the properties in the following table.

| Property | Description |
|:--|:--|
| runbookType | Specifies the types of the runbook. <br><br> Script - PowerShell script <br>PowerShell - PowerShell workflow <br> GraphPowerShell - Graphical PowerShell script runbook <br> GraphPowerShellWorkflow - Graphical PowerShell workflow runbook   |
| logProgress | Specifies whether [progress records](../automation/automation-runbook-output-and-messages.md) should be generated for the runbook. |
| logVerbose  | Specifies whether [verbose records](../automation/automation-runbook-output-and-messages.md) should be generated for the runbook. |
| description | Optional description for the runbook. |
| publishContentLink | Specifies the content of the runbook. <br><br>uri - Uri to the content of the runbook.  This will be a .ps1 file for PowerShell and Script runbooks, and an exported graphical runbook file for a Graph runbook.  <br> version - Version of the runbook for your own tracking. |

An example of a runbook resource is below.  In this case, it retrieves the runbook from the [PowerShell Gallery](https://www.powershellgallery.com).

	"name": "[concat(parameters('accountName'), '/MyRunbook'))]",
	"type": "Microsoft.Automation/automationAccounts/runbooks",
	"apiVersion": "[variables('AutomationApiVersion')]",
	"location": "[parameters('regionId')]",
	"tags": { },
	"properties": {
		"runbookType": "PowerShell",
		"logProgress": "true",
		"logVerbose": "true",
		"description": "",
		"publishContentLink": {
			"uri": "https://www.powershellgallery.com/packages/Start-AzureV2VMs/1.0/Content/Start-AzureV2VMs.ps1",
			"version": "1.0.0.0"
		}
	}

### Starting a runbook
There are two methods to start a runbook in a solution.

- To start the runbook when the solution is installed, create a [job resource](#automation-jobs) as described below.
- To start the runbook on a schedule, create a [schedule resource](#schedules) as described below. 


## Automation jobs
In order to start a runbook when the solution is installed, you create a **job** resource.  Job resources have a type of **Microsoft.Automation/automationAccounts/jobs** and have the properties in the following table.

| Property | Description |
|:--|:--|
| runbook    | Single **name** entity with the name of the runbook.  |
| parameters | Entity for each parameter required by the runbook. |


The job includes the runbook name and any parameter values to be sent to the runbook.  The job must depend on the runbook that it's starting since the runbook must be created before the job.  You also create dependencies on other jobs for runbooks that should be completed before the current one.

Following is an example of a job resource that starts a runbook when the solution is installed.  Two other runbooks must be completed before this one starts, so it has dependencies on the jobs for those runbooks.  The runbook also accepts parameters   

	{
		"name": "[concat(parameters('accountName'), '/', parameters('ScheduleReregisterJobIDGuid'))]",
		"type": "Microsoft.Automation/automationAccounts/jobs",
		"apiVersion": "[variables('AutomationApiVersion')]",
		"location": "[parameters('regionId')]",
		"dependsOn": [
			"[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ScheduleReRegisterRunbookName'))]",
			"[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('ScheduleCreationGuid'))]",
			"[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('ElasticPoolScheduleCreationGuid'))]"
		],
		"tags": { },
		"properties": {
			"runbook": {
				"name": "[variables('ScheduleReRegisterRunbookName')]"
			},
			"parameters": {
				"targetSubscriptionId": "[subscription().subscriptionId]",
				"resourcegroup": "[resourceGroup().name]",
				"automationaccount": "[parameters('accountName')]"
			}
		}
	}


## Certificates
[Azure Automation certificates](../automation/automation-certificates.md) have a type of **Microsoft.Automation/automationAccounts/certificates** and have the properties in the following table.

| Property | Description |
|:--|:--|
| base64Value   | Base 64 value for the certificate. |
| thumbprint    | Thumbprint for the certificate. |

An example of a certificate resource is below.

	"name": "[concat(parameters('accountName'), '/MyCertificate')]",
	"type": "certificates",
	"apiVersion": "2015-01-01-preview",
	"location": "[parameters('regionId')]",
	"tags": {},
	"dependsOn": [
		"[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'))]"
	],
	"properties": {
		"base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
		"thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
	}

## Credentials
[Azure Automation credentials](../automation/automation-credentials.md) have a type of **Microsoft.Automation/automationAccounts/credentials** and have the properties in the following table.

| Property | Description |
|:--|:--|
| userName   | User name for the credential. |
| password   | Password for the credential. |

An example of a credential resource is below.

	"name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
	"type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
	"apiVersion": "[variables('AutomationApiVersion')]",
	"location": "[parameters('regionId')]",
	"tags": { },
	"dependsOn": [
		"[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'))]"
	],
	"properties": {
		"userName": "User01",
		"password": "password"
	}


## Schedules
[Azure Automation schedules](../automation/automation-schedules.md) have a type of **Microsoft.Automation/automationAccounts/schedules** and have the properties in the following table.

| Property | Description |
|:--|:--|
| description | Optional description for the schedule. |
| startTime   | Specifies the start time of a schedule as a DateTime object. A string can be provided if it can be converted to a valid DateTime. |
| isEnabled   | Specifies whether the schedule is enabled. |
| interval    | The type of interval for the schedule.<br><br>day<br>hour |
| frequency   | Frequency that the schedule should fire in number of days or hours. |

An example of a schedule resource is below.

	"name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
	"type": "microsoft.automation/automationAccounts/schedules",
	"apiVersion": "[variables('AutomationApiVersion')]",
	"tags": { },
	"dependsOn": [
		"[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'))]"
	],
	"properties": {
		"description": "Schedule for StartByResourceGroup runbook",
		"startTime": "10/30/2016 12:00:00",
		"isEnabled": "true",
		"interval": "day",
		"frequency": "1"
	}

A schedule resource is limit

## Variables
[Azure Automation variables](../automation/automation-variables.md) have a type of **Microsoft.Automation/automationAccounts/variables** and have the properties in the following table.

| Property | Description |
|:--|:--|
| description | Optional description for the variable. |
| isEncrypted | Specifies whether the variable should be encrypted. |
| type        | Data type for the variable. |
| value       | Value for the variable. |

An example of a variable resource is below.

	"name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
	"type": "microsoft.automation/automationAccounts/variables",
	"apiVersion": "[variables('AutomationApiVersion')]",
	"tags": { },
	"dependsOn": [
		"[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'))]"
	],
	"properties": {
		"description": "Description for the variable.",
		"isEncrypted": "true",
		"type": "String",
		"value": "This is a string value."
	}


## Modules
Your solution does not need to define global modules used by your runbooks because they will always be available.  You do need to include a resource for any other module used by your runbooks, and the runbook should depend on the module resource to ensure that it's created before the runbook.



[Integration modules](../automation/automation-integration-modules.md) have a type of **Microsoft.Automation/automationAccounts/modules** and have the properties in the following table.

| Property | Description |
|:--|:--|
| contentLink | Specifies the content of the module. <br><br>uri - Uri to the content of the runbook.  This will be a .ps1 file for PowerShell and Script runbooks, and an exported graphical runbook file for a Graph runbook.  <br> version - Version of the runbook for your own tracking. |

An example of a module resource is below.

	{		
		"name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
		"type": "Microsoft.Automation/automationAccounts/modules",
		"apiVersion": "[variables('AutomationApiVersion')]",
		"dependsOn": [
			"[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('ModuleImportGuid'))]",
			"[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('SolutionCleanupJobIDGuid'))]"
		],
		"properties": {
			"contentLink": {
				"uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
			}
		}
	}

If you update a solution that includes a runbook that uses a schedule, and the new version of your solution has a new module used by that runbook, then the runbook may use the old version of the module.  The script [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com) will ensure that all of the modules used by runbooks in your solution are the latest version.  


## Next steps

- [Test your solution](operations-management-suite-custom-solutions.md#testing-a-custom-solution) to ensure that it is a valid Resource Manager template.
- [Add a view to your custom solution](operations-management-suite-custom-solutions-resources-views.md) to visualize collected data.