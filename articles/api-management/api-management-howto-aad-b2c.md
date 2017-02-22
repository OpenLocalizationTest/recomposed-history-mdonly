---
title: Authorize developer accounts using Azure Active Directory B2C - Azure API Management | Microsoft Docs
description: Learn how to authorize users using Azure Active Directory B2C in API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: erikre
editor: ''

ms.assetid: 33a69a83-94f2-4e4e-9cef-f2a5af3c9732
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
---
# How to authorize developer accounts using Azure Active Directory B2C in Azure API Management
## Overview
Azure Active Directory B2C is a cloud identity manaagement solution for consumer-facing web and mobile applications. You can use it to manage access to your developer portal. This guide shows you the configuration required in your API Management service to integrate with Azure Active Directory B2C.

For information about enabling access to the developer portal using the classic Azure Active Directory, see [How to authorize developer accounts using Azure Active Directory].

> To complete the steps in this guide you must first have an Azure Active Directory B2C tenant in which to create an application and have signup, signin policies ready. For more information, see [Azure Active Directory B2C overview].
> 
> 

## How to authorize developer accounts using Azure Active Directory B2C
To get started, click **Publisher portal** in the Azure Portal for your API Management service. This takes you to the API Management publisher portal.

![Publisher portal][api-management-management-console]

> If you have not yet created an API Management service instance, see [Create an API Management service instance][Create an API Management service instance] in the [Get started with Azure API Management][Get started with Azure API Management] tutorial.
> 
> 

Click **Security** from the **API Management** menu on the left and click **External Identities**.

![External Identities][api-management-howto-aad-b2c-security-tab]

Click **Azure Active Directory B2C**. Make a note of the **Redirect URL** and switch over to your Azure Active Directory B2C in the Azure Portal.

![External Identities][api-management-howto-aad-b2c-security-tab-reply-url]

Click the **Applications** button.

![Register New Application][api-management-howto-aad-b2c-portal-menu]

Click the **Add** button to create a new Azure Active Directory B2C application.

![Register New Application][api-management-howto-aad-b2c-add-button]

In the **New application** blade, enter a name for the application, choose **Yes** under **Web App/Web API** and choose **Yes** under **Allow implicit flow**. Then copy the **Redirect URL** from the **Azure Active Directory B2C** section of the **External Identities** tab in the publisher portal and paste it into the **Reply URL** text box. 

![Register New Application][api-management-howto-aad-b2c-app-details]

Click the **Create** button. When the application is created, it will appear in the **Applications** blade. Click the application name to see its details. 

![Register New Application][api-management-howto-aad-b2c-app-created]

From the **Properties** blade, copy the **Application Id** to the clipboard.

![App ID][api-management-howto-aad-b2c-app-id]

Switch back to the publsiher portal and paste the ID into the **Client ID** text box.

![App ID][api-management-howto-aad-b2c-client-id]

Switch back to the Azure Portal, click the **Keys** button, then click **Generate key**.

![App Key][api-management-howto-aad-b2c-app-key]

Click **Save** to save the configuration and display the **App key**. Copy the key to the clipborad. 

![App Key][api-management-howto-aad-b2c-app-key-saved]  

Switch back to the publisher portal and paste the key into the **Client Secret** text box.

![App Key][api-management-howto-aad-b2c-client-secret]

Specify the domain name of the AAD B2C tenant in the **Allowed Tenant** field.

![Allowed Tenant][api-management-howto-aad-b2c-allowed-tenant]

Specify the **Signup Policy** and **Signin Policy**. Optionally, you can also provide **Profile Editing Policy** and **Password Reset Policy**. 

![Policies][api-management-howto-aad-b2c-policies]

> For more details on policies, see [Azure Active Directory B2C: Extensible policy framework].
>
>

Once the desired configuration is specified, click **Save**.

Once the changes are saved, developers will be able to create new accounts and signin to the developer portal using AAD B2C. 

## How to signup for a developer account using Azure Active Directory B2C

To signup for a developer account using Azure Active Directory B2C, open a new browser window and go to the developer portal. Click the **Sign up** button.

![Developer Portal][api-management-howto-aad-b2c-dev-portal]

Choose sign up with **Azure Active Directory B2C** in the button. 

![Developer Portal][api-management-howto-aad-b2c-dev-portal-b2c-button]

You will be redirected to the signup policy configured in the previous section and can choose to signup using their email address or their existing social accounts. 

![Developer Portal][api-management-howto-aad-b2c-dev-portal-b2c-options]

When the signup is complete, You will be redireted back to the developer portal. You are now logged into the developer portal for your API Management service instance.

![Registration Complete][api-management-registration-complete]

## Next step

*  [Azure Active Directory B2C overview]
*  [Azure Active Directory B2C: Extensible policy framework]
*  [Use Microsoft Account as an identity provider in Azure Active Directory B2C]
*  [Use Google Account as an identity provider in Azure Active Directory B2C]
*  [Use linkedin Account as an identity provider in Azure Active Directory B2C]
*  [Use Facebook Account as an identity provider in Azure Active Directory B2C]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.png
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.png
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.png
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.png
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.png
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.png
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.png
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.png
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.png
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.png
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.png
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.png
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.png
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.png
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.png
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Azure Active Directory B2C overview]: https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview
[How to authorize developer accounts using Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: Extensible policy framework]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Use Microsoft Account as an identity provider in Azure Active Directory B2C]: https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Use Google Account as an identity provider in Azure Active Directory B2C]: https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Use Facebook Account as an identity provider in Azure Active Directory B2C]: https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Use Linkedin Account as an identity provider in Azure Active Directory B2C]: https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-setup-fb-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

