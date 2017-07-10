---
title: Deploy an Azure Resource Manager template in an Azure Automation runbook | Microsoft Docs
description: How to deploy an Azure Resource Manager template stored in Azure Storage from a runbook
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: powershell,  runbook, json, azure automation

ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 07/09/2017
ms.author: eslesar
---

# Deploy an Azure Resource Manager template in an Azure Automation PowerShell runbook

You can write an [Azure Automation PowerShell runbook](automation-first-runbook-textual-powershell.md)
that deploys an Azure resource by using an 
[Azure Resource Management template](../azure-resource-manager/resource-manager-create-first-template.md).

By doing this, you can automate deployment of Azure resources. You can maintain your Azure Resource Manager templates in a central,
secure location such as Azure Storage.

In this topic, we create a PowerShell runbook that uses an Azure Resource Manager template stored in
[Azure Storage](../storage/storage-introduction.md) to deploy a new Azure Storage account.

## Prerequisites

To complete this tutorial, you need the following:

* Azure subscription. If you don't have one yet, you can [activate your MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) or [sign up for a free account](https://azure.microsoft.com/free/).
* [Automation account](automation-sec-configure-azure-runas-account.md) to hold the runbook and authenticate to Azure resources.  This account must have permission to start and stop the virtual machine.
* [Azure Storage account](../storage/storage-create-storage-account.md) in which to store the Azure Resource Manager template
* Azure PowerShell installed on a local machine. See [Install and configure Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) for information about how to get Azure PowerShell.

## Create the Azure Resource Manager template

For this example, we use an Azure Resource Manager template that deploys a new Azure Storage account.

In a text editor, copy the following text:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Save the file locally as `ARMTest.json`.

## Save the Azure Resource Manager template in Azure Storage

Now we use PowerShell to create an Azure Storage file share and upload the `ARMTest.json` file.
For instructions on how to create a file share and upload a file in the Azure portal, see
[Get started with Azure File storage on Windows](../storage/storage-dotnet-how-to-use-files.md).

Launch PowerShell on your local machine, and run the following commands to create a file share and upload the Azure Resource Manager template
to that file share.

```powershell
# Login to Azure
Login-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'arm-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'arm-templates' -Context $context

# Add the ARMTest.json file to the new file share
# "TemplatePath" is the path where you saved the ARMTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## Create the PowerShell runbook script

Now we create a PowerShell script that gets the `ARMTemplate.json` file from Azure Storage
and deploys the Azure Resource Manager template to create a new Azure Storage account.

In a text editor, paste the following text:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'arm-templates' -Context $Context -path 'ARMTest.json' -Destination 'C:\Temp'

$ARMFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $ArmFile -TemplateParameterObject $Parameters 
``` 

Save the file locally as `DeployArmTemplate.ps1`.

## Import and publish the runbook into your Azure Automation account

Now we use PowerShell to import the runbook into your Azure Automation account,
and then publish the runbook.
For information about how to import and publish a runbook in the Azure portal, see 
[Creating or importing a runbook in Azure Automation](automation-creating-importing-runbook.md).

To import `DeployArmTemplate.ps1` into your Automation account as a PowerShell runbook,
run the following PowerShell commands:

```powershell
# MyPath is the path where you saved DeployArmTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployArmTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployArmTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## Start the runbook

Now we start the runbook by calling the 
[Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook?view=azurermps-4.1.0)
cmdlet.

For information about how to start a runbook in the Azure portal, see
[Starting a runbook in Azure Automation](automation-starting-a-runbook.md).

Run the following commands in the PowerShell console:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'ARMTemplate.json'
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployArmTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

The runbook runs, and you can check its status by running `$job.Status`.

The runbook gets the Azure Resource Manager template and uses it to deploy a new Azure Storage account.
You can see that the new storage account was created by running the following command:
```powershell
Get-AzureRmStorageAccount
```

## Summary

That's it! Now you can use Azure Automation and Azure Storage, and Azure Resource Manager templates to deploy all your Azure resources.

## Next steps

* To learn more about Azure Resource Manager templates, see [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md)
* To get started with Azure Storage, see [Introduction to Azure Storage](../storage/storage-introduction.md).
* To find other useful Azure Automation runbooks, see
[Runbook and module galleries for Azure Automation](automation-runbook-gallery.md).
* To find other useful Azure Resource Manager templates, see [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/)

