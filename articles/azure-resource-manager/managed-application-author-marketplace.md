---
title: Azure Managed Application in Marketplace | Microsoft Docs
description: Describes Azure Managed Application that are available through the Marketplace.
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

# Azure Managed Applications in Marketplace

As discussed in the [Managed Application overview](managed-application-overview.md) article, managed applications in Azure marketplace enables MSPs, ISVs, and System Integrators (SIs) offer their solutions to all Azure customers. Such solutions reduce the maintenance and servicing overhead for customers. Publishers not only sell infrastructure and software through the marketplace, but also attach services and operational support to them. 

This article explains how an MSP, ISV, or SI can publish an application to the Azure Marketplace and make it broadly available to the customers.  

## Pre-requisites for publishing a Managed Application

Prerequisites to listing on Azure Marketplace

1.  Technical

    *   [Author Azure Resource Manager templates](resource-group-authoring-templates.md)

    *   Azure Quickstart templates:

        *   [https://azure.microsoft.com/en-us/documentation/templates/](https://azure.microsoft.com/en-us/documentation/templates/)

        *   [https://github.com/azure/azure-quickstart-templates](https://github.com/azure/azure-quickstart-templates)

    *   Create UI Definition

        *   [Create user interface definition file](managed-application-createuidefinition-overview.md)

2.  Non-technical (business requirements)

    *   Your company (or its subsidiary) must be located in a sell from country supported by the Azure Marketplace
    *   Your product must be licensed in a way that is compatible with billing models supported by the Azure Marketplace
    *   You are responsible for making technical support available to customers in a commercially reasonable manner. The support can be free, paid, or through community support.
    *   You are responsible for licensing your software and any third-party software dependencies.
    *   You must provide content that meets criteria for your offering to be listed on Azure Marketplace and in the Azure Management Portal
    *   You must agree to the terms of the Azure Marketplace Participation Policies and Publisher Agreement?
    *   You must agree to comply with the Terms of Use, Microsoft Privacy Statement, and Microsoft Azure Certified Program Agreement.

## How to create a new Azure Application offer

Once all the pre-requisites have been met, you are ready to start authoring your managed application offer. Before going in the details of authoring the offer, a quick overview of an offer and SKU.

**Offer**

An Azure Application offer corresponds to a class of product offering from a publisher. If you have a new type of solution/application that you would like to be available in Azure Marketplace, a new offer would be the way to go. An offer is a collection of SKUs. Every offer appears as its own entity in Azure Marketplace.

**SKU**

A SKU is the smallest purchasable unit of an offer. While within the same product class (offer), SKUs allow you to differentiate between different features supported, whether the offer is managed or unmanaged and billing models supported.


A SKU shows up under the parent offer in Azure Marketplace while it shows up as its own purchasable entity in Azure portal.

1.  Log in to the [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  From the left navigation bar, click on **+ New offer** and select **Azure Applications**.

	![Alt text](./media/managed-application-author-marketplace/newOffer.png)

3.  A new offer "Editor" view is now opened for you, and we are ready to start authoring.

	![Alt text](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

4.  The "forms" that need to be filled out are visible on the left within the "Editor" view. Each "form" consists of a set of fields that are to be filled out. Required fields are marked with a red asterisk (*).

 **There are four main forms for authoring a Managed Application**

    *   Offer Settings

    *   SKUs

    *   Marketplace

    *   Support

## How to fill out the Offer Settings form

The offer settings form is a basic form to specify the offer settings. The different fields are:

**Offer ID**

This field is a unique identifier for the offer within a publisher profile. This ID is visible in product URLs, Resource Manager templates, and billing reports. It can only be composed of lowercase alphanumeric characters or dashes (-). The ID cannot end in a dash and can have a maximum of 50 characters. This field is locked once an offer goes live.

**Publisher ID**

This field dropdown allows you to choose the publisher profile you want to publish this offer under. This field is locked once an offer goes live.

**Name**

This field is the display name for your offer. It is the name that shows up in Azure Marketplace and in Azure portal. It can have a maximum of 50 characters. Guidance here is to include a recognizable brand name for your product. Don't include your company name here unless that is how it is marketed. If you are marketing this offer at your own website, ensure that the name is exactly how it shows up in your website.

Click on "Save" to save your progress. Next step would be to add SKUs for your offer.

## How to create SKUs

Select the **SKUs** form. Here you can see an option to **Add a SKU**. Selecting this option allows you to enter a **SKU ID**.

![Alt text](./media/managed-application-author-marketplace/newOffer_skus.png)

"SKU ID" is a unique identifier for the SKU within an offer. This ID is visible in product URLs, Resource Manager templates, and billing reports. It can only be composed of lowercase alphanumeric characters or dashes (-). The ID cannot end in a dash and can have a maximum of 50 characters. This field is locked once an offer goes live. You can have multiple SKUs within an offer. You need a SKU for each image you are planning to publish.

Once a SKU has been added, it appears in the list of SKUs within the "SKUs" form. Click on the SKU name to get into the details of that particular SKU. Here are some details for some of the fields.

After clicking "New SKU", you will need to fill the following form

![Alt text](./media/managed-application-author-marketplace/newOffer_newsku.png)

### How to fill Sku Details section

**Title** - Provide a title for this Sku. This is what shows up in the gallery for this item.

**Summary** - Provide a short summary for this sku. This text shows up right under the title.

**Description** - Provide a detailed description about the SKU.

**Sku Type** - The allowed values are **Managed Application** and **Solution Templates**. For this case, select **Managed Application**.

### How to fill Package Details section

The package section has the following fields that need to be filled out

![Alt text](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

**Current version** - Provide a version for the package you upload. It should be in the format - `{number}.{number}.{number}{number}`

**Package File** - This package contains the following files that are compressed into a .zip file.

*   **applianceMainTemplate.json** - The deployment template file that is used to deploy the solution/application. You can find more details on how to author deployment template files here - [Create your first Azure Resource Manager template](resource-manager-create-first-template.md)

*  **mainTemplate.json** - The template file that contains only the Microsoft.Solution/appliances resource. The key properties of this resource to be aware of are as follows:
*  
	*  "kind" - The value is "Marketplace" for Marketplace Managed application scenario
	*  "ManagedResourceGroupId" - The resource group in the customer's subscription where all the resources defined in the applianceMainTemplate.json are deployed.
	*  "PublisherPackageId": The string that uniquely identifies the package. The value needs to be in the following format - 
	
		It is a concatenation of [publisherId].[OfferId].[SKUID].[PackageVersion]
		
		The publisherId and OfferId could be obtained from the publishing portal.

		![Alt text](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
		
		The SKU ID can be obtained as shown in the following image:

		![Alt text](./media/managed-application-author-marketplace/UniqueString_skuid.png)
		
		The package version can be obtained as shown in the following image:

		![Alt text](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
	

  So using the preceding examples, the value of **PublisherPackageId** is `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`

  Sample mainTemplate.json:

  ```json
  {
  		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  		"contentVersion": "1.0.0.0",
  		"parameters": {
			"storageAccountNamePrefix": {
  				"type": "string",
  				"metadata": {
					"description": "Specify the name of the storage account"
  				}
			},
			"storageAccountType": {
  				"type": "string"
			}
  		},
  		"variables": {
			"managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
  		},
  		"resources": [{
  			"type": "Microsoft.Solutions/appliances",
  			"apiVersion": "2016-09-01-preview",
  			"name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
  			"location": "[resourceGroup().location]",
  			"kind": "marketplace",
  			"properties": {
				"managedResourceGroupId": "[variables('managedResourceGroup')]",
				"PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0"
				"parameters": {
  					"storageAccountName": {
						"value": "[parameters('storageAccountNamePrefix')]"
  					},
  					"storageAccountType": {
						"value": "[parameters('storageAccountType')]"
 	 				}
				}
  			}
		}],
  		"outputs": {

  		}
  }
  ```

*   **appliancecreateUIDefinition.json** - This file is used by the Azure portal to generate the user interface for provisioning this solution/application. You can find more details on how to create this file here - [https://docs.microsoft.com/en-us/azure/azure-resource-managermanaged-application-createuidefinition-overview](managed-application-createuidefinition-overview.md)


This package should contain any other nested templates or scripts that are required to successfully provision this application. The mainTemplate.json, applianceMainTemplate.json, and applianceCreateUIDefinition.json must be present at the root folder.

More details on how to author this package can be found here.

**Authorizations** - This property defines who gets access what level of access to the resources in customers subscriptions. It enables the publisher to manage the application on behalf of the customer.

*   PrincipalId - This property is the Azure Active directory identifier of a user, user group, or application that is granted certain permissions (as described by the Role Definition) on the resources in the customers subscription.

*   Role Definition - This property is a list of all the built-in RBAC roles provided by Azure AD. You can select the role that is most appropriate and allows you to manage the resources on behalf of the customer.

Multiple authorizations can be added. However, it is recommended to create an Active directory user group and specify its Id in the **PrincipalId**. It enables addition of more users to the user group and required without having to update the SKU.

For more information about RBAC, see [Get started with Role-Based Access Control in the Azure portal](../active-directory/role-based-access-control-what-is.md).

## Marketplace Form

The marketplace form within an Azure application offer asks for fields that show up on [Azure Marketplace](https://azuremarketplace.microsoft.com) and on [Azure portal](https://portal.azure.com/). Here are some details for some of the fields.

#### Preview Subscription Ids

Enter here a list of Azure Subscription IDs that you would like to have access to the offer once it's published. These white-listed subscriptions allow you to test the previewed offer before making it live. The partner portal allows you to white-list upto 100 subscriptions.

#### Suggested Categories

Select up to five categories from the provided list that your offer can be best associated with. The selected categories are used to map your offer to the product categories available in [Azure Marketplace](https://azuremarketplace.microsoft.com) and [Azure portal](https://portal.azure.com/).

Here are some of the places that the data you provide on this form shows up in.

##### Azure Marketplace

![publishvm10](./media/managed-application-author-marketplace/publishvm10.png)

![publishvm11](./media/managed-application-author-marketplace/publishvm11.png)

![publishvm15](./media/managed-application-author-marketplace/publishvm15.png)

##### Azure portal

![publishvm12](./media/managed-application-author-marketplace/publishvm12.png)

![publishvm13](./media/managed-application-author-marketplace/publishvm13.png)

##### Logo Guidelines

Use the following guidelines for all the logos uploaded in the Cloud Partner Portal:

*   The Azure design has a simple color palette. Keep the number of primary and secondary colors on your logo low.
*   The theme colors of the Azure portal are white and black. Hence avoid using these colors as the background color of your logos. Use some color that would make your logos prominent in the Azure portal. We recommend simple primary colors. **If you are using transparent background, then make sure that the logos/text are not white or black or blue.**
*   Do not use a gradient background on the logo.
*   Avoid placing text, even your company or brand name, on the logo. The look and feel of your logo should be 'flat' and should avoid gradients.
*   Make sure the logo is not stretched.

##### Hero Logo

The Hero logo is optional. The publisher can choose not to upload a Hero logo. However once uploaded the hero icon cannot be deleted. At that time, the partner must follow the Azure Marketplace guidelines for Hero icons.

###### Guidelines for the Hero logo icon

*   The Publisher Display Name, plan title and the offer long summary are displayed in white font color. Hence you should avoid keeping any light color in the background of the Hero Icon. Black, white, and transparent background is not allowed for Hero icons.

*   The publisher display name, plan title, the offer long summary and the create button are embedded programmatically inside the Hero logo once the offer goes listed. So you should not enter any text while you are designing the Hero logo. Just leave empty space on the right because the text (that is, publisher display name, plan title, the offer long summary) is included programmatically by us over there. The empty space for the text should be 415x100 on the right (and it is offset by 370 px from the left).

![publishvm14](./media/managed-application-author-marketplace/publishvm14.png)

## Support Form

The next form to fill out is the support form. This form asks for support contacts from your company like engineering contact information and customer support contact information.

## How to publish an offer

Now that you have your offer drafted, the next step would be to publish the offer to Azure Marketplace. Follow instructions to [get your offer live in Azure Marketplace](https://df.publishing.azure-test.net/#documentation/make-offer-live-on-Azure-Marketplace)