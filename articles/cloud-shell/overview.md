---
title: Azure Cloud Shell (Preview) overview | Microsoft Docs
description: Overview of the Azure Cloud Shell.
services: 
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
 
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
---
# Overview of Azure Cloud Shell (Preview)
Azure Cloud Shell is an interactive, browser-accessible shell for managing Azure resources.

![](media/startup.gif)

## Features
### Browser-based shell experience
Cloud Shell enables access to a browser-based command-line experience built with Azure management tasks in mind. Leverage Cloud Shell to work untethered from a local machine in a way only the cloud can provide.

### Pre-configured Azure workstation
Cloud Shell comes pre-installed with popular command-line tools and language support so you can work faster.

[View the full tooling list for Azure Cloud Shell here.](features.md#tools)

### Automatic authentication
Cloud Shell securely authenticates automatically on each session for instant access to your resources through the Azure CLI 2.0.

### Connect your Azure File storage
Cloud Shell machines are temporary and as a result require an Azure file share to be mounted to persist your $Home directory.
On first launch Cloud Shell prompts to create a resource group, storage account, and file share on your behalf. This is a one-time step and will be automatically attached for all sessions. 

![](media/storage-prompt.png)

An LRS storage account is created on your behalf with an Azure file share containing a default 5-GB disk image.
This disk image is used to sync and persist your $Home directory. Regular storage costs apply.
Three resources will be created on your behalf:
1. Resource Group named: `cloud-shell-storage-<region>`
2. Storage Account named: `cs-uniqueGuid`
3. File Share named: `cs-<user>-<domain>-com-uniqueGuid`

[Explore more about how Cloud Shell persists files] (persisting-shell-storage.md).

## Concepts
* Cloud Shell runs on a temporary machine provided on a per-session, per-user basis
* Cloud Shell times out after 10 minutes without interactive activity
* Cloud Shell can only be accessed with a file share attached
* Cloud Shell is assigned one machine per user account
* Permissions are set as a regular Linux user

[Learn more about all Cloud Shell features.](features.md)

## Examples
* Create or edit scripts to manage Azure resources from any browser
* Simultaneously manage resources via Azure portal and Azure CLI 2.0
* Test-drive Azure CLI 2.0

[Try out all these examples at the Cloud Shell quickstart.](quickstart.md)

## Pricing
The machine hosting Cloud Shell is free, with a pre-requisite of a mounted Azure file share to persist your $Home directory. 
Regular storage costs apply.

## Supported browsers
Cloud Shell is recommended for Chrome, Edge, and Safari. 
While Cloud Shell is supported for Chrome, Firefox, Safari, IE, and Edge, Cloud Shell is subject to specific browser settings.
For specific known limitations, visit [limitations of Cloud Shell](limitations.md).

## Open-source tooling
Cloud Shell was built with open-source tools like [xterm.js](https://github.com/sourcelair/xterm.js/), [Kubernetes](https://github.com/kubernetes/kubernetes), and [Docker](https://github.com/moby/moby). Learn more about [how Azure supports open source technologies.](https://azure.microsoft.com/en-us/overview/choose-azure-opensource/)