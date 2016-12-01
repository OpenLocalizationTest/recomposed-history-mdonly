---
title: OMS virtual machine extension for Windows | Microsoft Docs
description: Deploy the OMS agent on Windows virtual machine using a virtual machine extension.
services: virtual-machines-windows
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/29/2016
ms.author: nepeters

---
# OMS virtual machine extension for Windows

## Overview

Operations Management Suite (OMS) provides monitoring, alerting, and alert remediation capabilities across cloud and on-premises assets. The OMS Agent virtual machine extension for Windows is published and supported by Micosoft. The extension installs the OMS agent on Azure virtual machines, and enrolls virtual machines into an existing OMS workspace. This document details the supported platforms, configurations, and deployment options for the OMS virtual machine extension for Windows.

For general information on Azure virtual machine extensions see, [Virtual Machine extensions overview](./virtual-machines-windows-extensions-features.md).

For more information on Operations Management Suite, see [Operations Management Suite overview](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite).

## Prerequisites

The OMS Agent extension for Windows can be run against Windows Server 2012, 2012 R2, and 2016 releases.

## Extension configuration

The OMS Agent virtual machine extension for Windows requires the workspace Id and workspace key from the target OMS workspace. Because the workspace key should be treated as sensitive data, it is stored in a protected configuration. Azure VM extension protected configuration data is encrypted, and only decrypted on the target virtual machine. The public and private configurations are specified at deployment time, which is detailed in subsequent sections of this document.

### Public configuration

Schema for the public configuration:

- workspaceId: (required, string) the OMS workspace id to onboard the virtual machine into.

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### Private configuration

Schema for the public configuration:

- workspaceKey: (required, string) the primary/secondary shared key of the workspace.

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## Template deployment

Azure VM extensions can be deployed with Azure Resource Manager templates. Templates are ideal when deploying one or more virtual machines that require post deployment configuration such as onboarding to OMS. A sample Resource Manager template that includes the OMS Agent VM extension can be found on the [Azure Quick Start Gallery](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

This sample can be deployed from this document using this button:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-windows-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

The JSON used to deploy the OMS Agent VM extension looks like the following:

```json
{
	"type": "extensions",
	"name": "Microsoft.EnterpriseCloud.Monitoring",
	"apiVersion": "[variables('apiVersion')]",
	"location": "[resourceGroup().location]",
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
	],
	"properties": {
		"publisher": "Microsoft.EnterpriseCloud.Monitoring",
		"type": "MicrosoftMonitoringAgent",
		"typeHandlerVersion": "1.0",
		"autoUpgradeMinorVersion": true,
		"settings": {
			"workspaceId": "myWorkSpaceKey"
		},
		"protectedSettings": {
			"workspaceKey": "myWorkspaceId"
		}
	}
}
```

## PowerShell deployment

The `Set-AzureRmVMExtension` command can be used to deploy the OMS Agent virtual machine extension to an existing virtual machine. Before running the command, the public and private configurations need to be stored in a PowerShell hash table. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS ` 
```

## Troubleshoot and support

### Troubleshoot

Data about the state of extension deployments can be retrieved from the Azure portal, and by using the Azure CLI. To see the deployment state of extensions for a given VM, run the following command using the Azure CLI.

```azurecli
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Extension execution output is logged to files found in the following directory:

`
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
`

### Support

If you need more help at any point in this article, you can contact the Azure experts on the [MSDN Azure and Stack Overflow forums](https://azure.microsoft.com/en-us/support/forums/). Alternatively, you can file an Azure support incident. Go to the [Azure support site](https://azure.microsoft.com/en-us/support/options/) and select Get support. For information about using Azure Support, read the [Microsoft Azure support FAQ](https://azure.microsoft.com/en-us/support/faq/).