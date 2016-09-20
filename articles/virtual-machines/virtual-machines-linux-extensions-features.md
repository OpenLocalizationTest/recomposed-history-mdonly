<properties
 pageTitle="Virtual machine extensions and features | Microsoft Azure"
 description="Learn what extensions are available for Azure virtual machines, grouped by what they provide or improve."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="squillace"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="08/23/2016"
 ms.author="rasquill"/>

# About virtual machine extensions and features

## Azure VM Extensions

Azure Virtual Machine extensions are small applications that provide post deployment host configuration and automation task on Azure Virtual Machines. For example, if a Virtual Machine requires software to be installed, anti-virus protection, or Docker configuration, a VM extension can be used to these tasks. Azure VM extensions can be run using PowerShell, the Azure CLI, and Azure Resource Manage templates and can be bundled with a new virtual machine deployment, or run against any existing system.

This document provides prerequisites for Azure Virtual Machine extension, and guidance on how to detect available VM extensions. 

## Azure VM Agent

The Azure VM Agent manages interaction between an Azure Virtual Machine and the Azure Fabric Controller. The VM agent is responsible for many functional aspects of deploying and managing Azure Virtual Machines, including running VM Extensions. The Azure VM Agent is pre-installed on Azure Gallery Images. For information on support operating systems and installation instructions, see [Azure Linux Agent User Guide](./virtual-machines-linux-agent-user-guide.md).

## Discover VM Extensions

Many different VM extensions are available for use with Azure Virtual Machines. To see a complete list, run the following command with the Azure CLI.

```none
azure vm extension-image list westus
```

## Common VM Extensions

|Extension Name   |Description   |More Information   |
|---|---|---|
|Custom Script Extension for Linux  | Run scripts against an Azure Virtual Machine  |[Custom Script Extension for Linux](./virtual-machines-linux-extensions-customscript.md)   |
|Docker Extension |Installs the Docker daemon to support remote Docker commands.  | [Docker VM Extension](./virtual-machines-linux-dockerextension.md)  |
|VM Access Extension | Regain access to Azure Virtual Machine  |[VM Access Extension](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess.md) |
|Azure Diagnostics Extension |Manage Azure Diagnostics |[Azure Diagnostics Extension](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

