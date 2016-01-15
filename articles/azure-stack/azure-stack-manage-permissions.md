﻿<properties
	pageTitle="Manage permissions to resources per user (service administrator and tenant)"
	description="Manage permissions to resources per user (service administrator and tenant)"
	services="azure-stack" 
	documentationCenter=""
	authors="v-anpasi"
	manager="v-kiwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="v-anpasi"/>

# Manage permissions to resources per user (service administrator and tenant)

For each subscription, resource group, or service, you can assign any user in Microsoft Azure Stack one of three different roles: Reader, Owner, or Contributor.

-   Reader: User can view everything, but can’t make any changes.

-   Contributor: User can manage everything except access to resources.

-   Owner: User can manage everything, including access to resources.

For example, User A might have reader permissions to Subscription 1, but have owner permissions to Virtual Machine 7.

## To set access permissions for a user

1.  Sign in with an account that has owner permissions to the resource you want to manage.

2.  In the blade for the resource, click the **Access** icon ![](media/azure-stack-manage-permissions/image1.png).

3.  In the **Users** blade, click **Roles**.

4.  In the **Roles** blade, click **Add** to add permissions for the user.
