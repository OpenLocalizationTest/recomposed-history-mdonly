<properties
	pageTitle="Azure Government documentation | Microsoft Azure"
	description="This provides a comparision of features and guidance on developing applications for Azure Government"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="09/29/2016"
	ms.author="ryansoc"/>


#  Azure Government Compute

##  Virtual Machines

For details on this service and how to use it, see [Azure Virtual Machines public documentation](/documentation/services/virtual-machines/windows/).

### Variations

The following VM SKUs are generally available (GA) in Azure Government:

VM SKU|US Gov VA|US Gov IA|Notes
---|---|---|---
A0-A7|GA|GA|None
D1-D14 v1|GA|-|None
DS1-DS14 v1|GA|-|None
D1-D14 v2|Planned|GA|15 is coming soon
F1-F16|Planned|GA|None

For additional details, and the most up to date information see [Sizes for Virtual Machines in Azure](../virtual-machines-windows-sizes.md).

###  Data Considerations

The following information identifies the Azure Government boundary for Azure Virtual Machines:

| Regulated/controlled data permitted | Regulated/controlled data not permitted |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Data entered, stored, and processed within a VM can contain export controlled data. Binaries running within Azure Virtual Machines. Static authenticators, such as passwords and smartcard PINs for access to Azure platform components. Private keys of certificates used to manage Azure platform components. SQL connection strings.  Other security information/secrets, such as certificates, encryption keys, master keys, and storage keys stored in Azure services.  | Metadata is not permitted to contain export controlled data. This metadata includes all configuration data entered when creating and maintaining your Azure Virtual Machine.  Do not enter Regulated/controlled data into the following fields:  Tenant role names, Resource groups, Deployment names, Resource names, Resource tags  

### Next Steps

For supplemental information and updates, subscribe to the
<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog. </a>
