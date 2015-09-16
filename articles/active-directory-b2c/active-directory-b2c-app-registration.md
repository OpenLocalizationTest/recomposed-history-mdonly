<properties
	pageTitle="Azure Active Directory B2C preview: Application registration | Microsoft Azure"
	description="How to register your application with Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C preview: how to Register your Application

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Pre-requisite

To build an application that accepts consumer sign up & sign in, you'll first need to register it with an Azure Active Directory B2C directory. Get your own directory using the steps outlined in this [article](active-directory-b2c-get-started.md).

## Navigate to the B2C Features Blade

You can get to the B2C features blade in one of the following two ways. Only a Global Administrator of the B2C directory can access the B2C features blade.

### 1. Directly on the Azure Preview Portal

If you followed the steps in this [article](active-directory-b2c-get-started.md) while creating the Azure AD B2C directory, you should have the B2C features blade pinned to your Startboard when you sign in to the [Azure preview portal](https://portal.azure.com/).

You can also access the B2C features blade directly by navigating to [https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com) where **{directory}** is to be replaced by the name used at directory creation time (for example, contosob2c). You can bookmark this link for future reference.

### 2. Access via the Azure Portal

Sign in to the [Azure portal](https://manage.windowsazure.com/), and navigate to your B2C directory. On the **Quick Start** tab, click on **Manage B2C settings** under **Administer**. This will open up the B2C features blade in a new browser window or tab.

You can also find the **Manage B2C settings** link (under **B2C Administration**) in the **Configure** tab.

## Register an Application

1. On the B2C features blade on the [Azure preview portal](https://portal.azure.com/), click on **Applications**.
2. Click **+Add** at the top of the blade.
3. The **Name** of the application will describe your application to consumers. For example, enter "Contoso B2C app".
4. If you are writing a web-based application, toggle the **Include web app / web API** switch to **Yes**. The **Reply URLs** are endpoints where Azure AD B2C will return any tokens your application requests. For example, enter `https://localhost:44321/`. If your application includes a server-side component (API) that needs to be secured, you'll want to create (and copy) an **Application Secret** as well by clicking the **Generate Key** button.

    > [AZURE.NOTE]
    **Application Secret** is an important security credential.

5. If you are writing a mobile application, toggle the **Include native client** switch to **Yes**. Copy down the default **Redirect URI** automatically created for you.
6. Click **Create** to register your application.
7. Click on the application that you just created and copy down the globally unique **Application ID** that you'll use later in your code.

## Build a Quick Start Application

Now that you have an application registered with Azure AD B2C, you can complete one of our quick start tutorials to get up & running. Here are a few recommendations:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]
