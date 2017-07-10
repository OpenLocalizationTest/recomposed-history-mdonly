---
title: Overview of Azure Managed Application | Microsoft Docs
description: Describes the concepts for Azure Managed Application
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax


ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
---

# Azure Managed Applications overview

Azure provides a robust Marketplace where vendors can offer their solutions to customers around the world. Azure Marketplace is a gallery that consists of hundreds of complex, multi-resource templates from first and third-party vendors. Customers can within minutes deploy and start using PaaS and SaaS applications. Although it provides a great way to quickly deploy an offering, the customer is still responsible for maintaining and updating the solution. For vendors, there is no way to charge customers for use of an application beyond the virtual machine image billing. Furthermore, vendors have no way of preventing customers from modifying critical application resources, and no way to block access to intellectual property that makes up an application. Azure Managed Applications provides a solution for these concerns. 

A managed application is similar to a marketplace solution template with one key difference. In a managed application, the resources are provisioned to a resource group that is managed by the vendor. The resource group is present in the customer's subscription, but an identity in the vendors tenant has access to the resource group.

## Advantages of managed applications

Managed applications enable Managed Service Providers (MSPs), Independent Software Vendors (ISVs), and corporate central IT teams to deliver solutions through the Azure Marketplace or Service Catalog. Although customers deploy these managed applications in their subscriptions, they don't have to maintain, update, or service them. The vendors manage and support these applications. Therefore, customers don't have to develop application-specific domain knowledge to manage these applications. It enables customers to automatically acquire application updates without having to worry about troubleshooting and diagnosing issues with the application.

For vendors and providers, managed applications create a channel to not only sell infrastructure and software through the marketplace, but also a way to attach services and operational support to Azure customers. It enables vendors to bill customers using Azure's billing system and use templates to manage the lifecycle of deployed applications. These solutions are self-contained and sealed to the customer, so vendors can provide high-quality service. This approach not only benefits the PaaS and SaaS vendors, but also corporate central platform teams and System Integrators that wish to package and resell their solutions.

## Managed Application Types
Azure Managed Applications come in two flavors - Service Catalog and Marketplace.
 
### Service Catalog  

Service Catalog allows organizations to create a catalog of approved solutions for Azure to be used by people in that organization. Maintaining such a catalog of solutions is helpful for central IT teams in enterprises. It enables them to ensure compliance with certain organizational standards while providing great solutions for their organization. They can control, update, and maintain these applications. It allows employees in the organization to easily discover the rich set of applications that are recommended and approved by the IT department. Customers only see the Service Catalog managed applications that they have created, or managed applications that other people in their organization have been shared with them.
 
To learn more about how a publisher can author a Service Catalog Managed Application, see [Create and publish Service Catalog Managed Application](managed-application-publishing.md).
 
To learn more about how people in an organization can consume Service Catalog Managed Application, see [Consume a Service Catalog Managed Application](managed-application-consumption.md).
 
### Marketplace

Marketplace applications are available through the marketplace in the Azure portal. Once published by the vendor, these applications are available for everyone inside or outside of your organization to consume. This approach enables MSPs, ISVs, and System Integrators (SIs) to offer their solutions to all Azure customers. The customers get the benefit of leveraging such complex solutions without having to invest in understanding and maintaining the solutions. Currently, the publisher can make their offer available as a managed application or as a solution template that is unmanaged. The main components of publishing a managed application include the template files, which describe the resources that are provisioned, and the UI definition file, which describes how the required inputs for provisioning these resources are displayed in the portal. The required files are packaged in a .zip file and uploaded through the publishing portal.
 
To learn more about marketplace-managed applications, see <hyperlink to the new article>

## Key concepts

### Managed resource group
The resource group where all the Azure resources being provisioned in the template are created. For example, if the appliance is creating a storage account, this resource group contains the storage account resource. It does not contain the appliance resource.

### Appliance package
The publisher creates a package that contains the template files and the createUIDefinition file. Specifically it contains the following files:

- **applianceMainTemplate.json** - The template file that defines all the resources that are provisioned by the appliance. This file is a regular template file that is used for creating resources.

- **MainTemplate.json** - Template file that defines the appliance resource (Microsoft.Solutions/appliances). One key property defined in this resource is the ManagedResourceGroupId. This property indicates which resource group is used for hosting the actual resources defined in the applianceMainTemplate.json.

- **applianceCreateUIDefinition.json** - This file describes how the UI needed for the parameters defined in the template is rendered.

### Authorization
The publisher needs to specify the permissions required by the vendor to manage the resources on behalf of the customer. This permission applies to the managed resource group. You set the following values:

- **PrincipalID** - The Azure AD identifier of the user, group, or application that is used to grant access to the managed resource group. This identifier belongs to the publisher's tenant.

- **RoleDefinitionID** - The Azure AD identifier of the role assigned to the preceding principal ID. It could be any of the built-in RBAC roles in the publisher's tenant. For more information, see [Built-in roles for Azure role-based access control](../active-directory/role-based-access-built-in-roles.md).

## Next steps

* To understand the vendor experience, see [Create and publish an Azure Managed Application](managed-application-publishing.md).
* To understand the consumer experience, see [Consume an Azure Managed Application](managed-application-consumption.md).
* To create a UI definition file, see [Getting started with CreateUiDefinition](managed-application-createuidefinition-overview.md).