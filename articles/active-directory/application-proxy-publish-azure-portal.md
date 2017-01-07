---
title: Publish apps with Azure AD Application Proxy | Microsoft Docs
description: Publish on-premises applications to the cloud with Azure AD Application Proxy in the Azure portal.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: harshja

ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: kgremban
---


# Publish applications using Azure AD Application Proxy - Public Preview

> [!div class="op_single_selector"]
> * [Azure portal](application-proxy-publish-azure-portal.md)
> * [Azure classic portal](active-directory-application-proxy-publish.md)

Azure Active Directory (AD) Application Proxy helps you support remote workers by publishing on-premises applications to be accessed over the internet. Through the Azure portal, you can publish applications that are running on your local network and provide secure remote access from outside your network.

This article walks you through the steps to publish your first app with Application Proxy. After you complete this article, you'll be ready to configure the application with personalized information or security requirements.

> [!NOTE]
> Application Proxy is a feature that is available only if you upgraded to the Premium or Basic edition of Azure Active Directory. For more information, see [Azure Active Directory editions](active-directory-editions.md).


## Publish an on-premises app for remote access

For your first published app, choose an application that's already set up for password-based authentication. Application Proxy supports other types of authentication, but password-based apps are the easiest to get up and running quickly. 

1. Sign in as an administrator in the [Azure portal](https://portal.azure.com/).
2. Select **Azure Active Directory** > **Enterprise applications** > **Add**.

  ![Add an enterprise application](./media/application-proxy-publish-azure-portal/add-app.png)

3. On the Categories page, select **Or add your own**.  

  ![Add your own application](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Choose **Deploying an existing application** from the dropdown menu.
5. Provide a name for your app, then select **Add**. A loading window pops up, and once your app is added the Quick start blade opens.
6. On the Quick start blade, select **Enable remote access for your on-premises application**.

  ![Enable remote access for your on-premises application](./media/application-proxy-publish-azure-portal/enable-remote-access.png)

7. Provide the following information about your application:

   - **Internal URL**: The address that the Application Proxy Connector uses to access the application from inside your private network. You can provide a specific path on the backend server to publish, while the rest of the server is unpublished. In this way, you can publish different sites on the same server as different apps, and give each one its own name and access rules.

     > [!TIP]
     > If you publish a path, make sure that it includes all the necessary images, scripts, and style sheets for your application. For example, if your app is at https://yourapp/app and uses images located at https://yourapp/media, then you should publish https://yourapp/ as the path.

   - **External URL**: The address your users will go to in order to access the app from outside your network.
   - **Pre Authentication**: How Application Proxy verifies users before giving them access to your application. For this walkthrough, choose Azure Active Directory from the dropdown menu. 

     - Azure Active Directory: Application Proxy redirects users to sign in with Azure AD, which authenticates their permissions for the directory and application.
     - Passthrough: Users don't have to authenticate against Azure Active Directory to access the application. You can still set up authentication requirements on the backend.
   - **Translate URL in Headers?**: Choose whether to translate the URL in the headers, or keep the original. 
   - **Connector Group**: Connectors process the remote access to your application, and connector groups help you organize connectors and apps by region, network, or purpose. If you don't have any connector groups created yet, your app is assigned to **Default** and you'll see a warning message asking you to [create a connector group](active-directory-application-proxy-connectors-azure-portal.md).

   ![Configure your application](./media/application-proxy-publish-azure-portal/configure-app.png)

8. Select **Save**.

   ![Save your Application Proxy settings](./media/application-proxy-publish-azure-portal/save-app-proxy.png)

## Add a test user 

To test that your app was published correctly, add a user account that you have access to. 

1. Back on the Quick start blade, select **Assign a user for testing**.

  ![Assign a user for testing](./media/application-proxy-publish-azure-portal/assign-user.png)

2. On the Users and groups blade, select **Add**.

  ![Add a user or group](./media/application-proxy-publish-azure-portal/add-user.png)

3. On the Add assignment blade, select **Users and groups** then choose the account you want to add. 
4. Select **Assign**.

## Test your published app

In your browser, navigate to the external URL that you configured during the publish step. You should see the start screen, and be able to sign in with the test account you set up.

![Test your published app](./media/application-proxy-publish-azure-portal/test-app.png)


## Next steps
- Download connectors and create connector groups to [Publish applications on separate networks and locations](active-directory-application-proxy-connectors-azure-portal.md)

- [Set up single sign-on](application-proxy-sso-azure-portal.md) for your newly-published app
