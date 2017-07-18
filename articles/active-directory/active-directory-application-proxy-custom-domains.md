---
title: Custom domains in Azure AD Application Proxy | Microsoft Docs
description: Manage custom domains in Azure AD Application Proxy so that the URL for the app is the same regardless of where your users access it. 
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila

ms.assetid: 2fe9f895-f641-4362-8b27-7a5d08f8600f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
---

# Working with custom domains in Azure AD Application Proxy

When you publish an application through Azure Active Directory Application Proxy, you create an external URL for your users to go to when they're working remotely. This URL gets the default domain *yourtenant-msappproxy.net*. If you want to use your own domain name, configure a custom domain for your application. 

We recommend that you set up custom domains for your applications whenever possible. Some of the benefits of custom domains include:

- Your users can get to the application with the same URL, whether they are working inside or outside of your network.
- If all of your applications have the same internal and external URLs, then links in one application that point to another continue to work outside the corporate network. 
- You control your branding, and create the URLs you want. 


Application Proxy uses the custom domain names that you set up in Azure Active Directory. If you haven't added your organization's domain yet, follow the steps in [Add a custom domain name to Azure Active Directory].

## Configure a custom domain

Before you configure a custom domain, make sure that you have the following: 
- A [verified domain added to Azure Active Directory](active-directory-domains-add-azure-portal.md).
- A custom certificate for the domain, in the form of a PFX file. 
- An on-premises app [published through Application Proxy](application-proxy-publish-azure-portal.md).

When you have those three requirements ready, follow these steps to set up your custom domain:

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Navigate to **Azure Active Directory** > **Enterprise applications** > **All applications** and choose the app you want to manage.
3. Select **Application Proxy**. 
4. In the External URL field, use the dropdown list to select your custom domain. If you don't see your domain in the list, then it hasn't been verified yet. 
5. The **Certificate** field that was disabled becomes enabled. Select this field. 
6. Select the location of the PFX certificate and click **Upload**.
7. Select **Save** to save your changes. 
8. Add a DNS record that redirects the new external URL to the application. This record enables you to have the same URL for internal and external access to the app, and a single shortcut in the user’s applications list.

## Manage certificates

### Certificate format
There is no restriction on the certificate signature methods, so ECC, SAN, and other common certificate types are all supported. You can also use wildcard certificates. If you use a wildcard certificate, please make sure the wildcard will match the desired external URL. Self-signed certificates are also accepted. If you’re using a private certificate authority, the CDP (certificate revocation point distribution point) for the certificate should be public.

### Changing the domain
All verified domains appear in the External URL dropdown list for your application. To change the domain, just update that field for the application. If you select a domain that doesn't have an associated certificate, follow steps 5-7 to add the certificate. If the domain you want isn't in the list, [add it as a verified domain](active-directory-domains-add-azure-portal.md). Then, make sure you update the DNS record to redirect from the new external URL. 

### Certificate management
You can use the same certificate for multiple applications unless the applications share an external host. 

When a certificate expires, you will get a warning and you will need to go upload another certificate through the portal. If the certificate is revoked, your users may see a security warning when accessing the application. We don’t perform revocation checks for certificates.  To update the certificate for a given application, navigate to the application and follow steps 5-7 above for configuring custom domains on published applications to upload a new certificate. If the old certificate is not being used by other applications, it will automatically be deleted. 

Currently all certificate management is through individual application pages so you will need to manage certificates in the context of the relevant applications. 

## Next steps
* [Enable single sign-on](active-directory-application-proxy-sso-using-kcd.md) to your published apps with Azure AD authentication.
* [Enable conditional access](active-directory-application-proxy-conditional-access.md) to your published apps.
* [Add your custom domain name to Azure AD](active-directory-add-domain.md)


