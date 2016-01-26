<properties
	pageTitle="Storage accounts in Azure Stack"
	description="Learn how an Azure Stack storage account gives you access to the Blob and Table services."
	services="azure-stack"
	documentationCenter=""
	authors="erikje"
	manager="v-kiwhit"
	editor=""/>

<tags
	ms.service="azure-stack"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="erikje"/>

# Storage accounts in Azure Stack

A storage account gives you access to the Blob and Table services. Your storage account provides the unique namespace for your storage data objects. By default, the data in your account is available only to you, the account owner. For more information about storage accounts, see [About Azure storage accounts](../storage/storage-create-storage-account.md).

## Provision a storage account

1.  Click **New**, then click **Data + Storage**, and then click **Storage** account.

2.  KNOWN BUG: Do not enter a name before selecting the subscription and resource group. If you do, the user interface will freeze. Close the blade and go back to step 1.

3.  If you have more than one subscription, then the **Subscription** field is displayed. Select the subscription in which you want to create the new storage account.

4.  Specify a new resource group or select an existing resource group.

5.  Enter a name for your storage account.

6.  Click **Create** to create the storage account.

## Delete a storage account

To remove a storage account that you are no longer using, navigate to the storage account in portal, and click **Delete**. Deleting a storage account deletes the entire account, including all data in the account.

## Next Steps

[Use Azure Resource Manager templates](azure-stack-arm-templates.md)
