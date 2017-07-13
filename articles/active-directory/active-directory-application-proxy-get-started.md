---
title: How to provide secure remote access to on-premises apps
description: Covers how to use Azure AD Application Proxy to provide secure remote access to your on-premises apps.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila

ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
---

# How to provide secure remote access to on-premises applications

Employees today want to be productive at any place, at any time, and from any device. They want to work on their own devices, whether they be tablets, phones, or laptops. And they expect to be able to access all their applications, both SaaS apps in the cloud and corporate apps on-premises. Providing access to on-premises applications has traditionally involved virtual private networks (VPNs) or demilitarized zones (DMZs). Not only are these solutions complex and hard to make secure, but they are costly to set up and manage.

There is a better way!

A modern workforce in the mobile-first, cloud-first world needs a modern remote access solution. Azure AD Application Proxy is a feature of Azure Active Directory that offers remote access as a service. That means it's easy to deploy, use, and manage.

> [!NOTE]
> Application Proxy is a paid feature of Azure Active Directory. For more information, see [Azure Active Directory editions](active-directory-editions.md#application-proxy).

## What is Azure Active Directory Application Proxy?
Azure AD Application Proxy provides single sign-on (SSO) and secure remote access for web applications hosted on-premises. Some apps you would want to publish include SharePoint sites, Outlook Web Access, or any other LOB web applications you have. These on-premises web applications are integrated with Azure AD, the same identity and control platform that is used by O365. End users can access your on-premises applications the same way they access O365 and other SaaS apps integrated with Azure AD. You don't need to change the network infrastructure or require VPN to provide this solution for your users.

## Why is Application Proxy a better solution?
Azure AD Application Proxy provides a simple, secure, and cost-effective remote access solution to all your on-premises applications.

Azure AD Application Proxy is:

* **Simple**
   * You don't need to change or update your applications to work with Application Proxy. 
   * Your users get a consistent authentication experience. They can use the MyApps portal to get single sign-on to both SaaS apps in the cloud and your apps on-premises. 
* **Secure**
   * When you publish your apps using Azure AD Application Proxy, you can take advantage of the rich authorization controls and security analytics in Azure. You get advanced security capabilities for all your existing apps without having to change any app.
   * You don't have to open any inbound connections through your firewall to give your users remote access. 
* **Cost-effective**
   * Application Proxy works in the cloud, so you can save time and money. On-premises solutions require you to set up and maintain DMZs, edge servers, or other complex infrastructures.  

## What kind of applications work with Application Proxy?
With Azure AD Application Proxy you can access different types of internal applications:

* Web applications that use Integrated Windows Authentication for authentication  
* Web applications that use form-based or header-based access  
* Web APIs that you want to expose to rich applications on different devices  
* Applications hosted behind a Remote Desktop Gateway  
* Rich client apps that are integrated with the Active Directory Authentication Library (ADAL)

## How does Application Proxy work?
Application Proxy works by installing a slim Windows Server agent called a connector inside your network. With the connector, you don't have to open any inbound ports or put anything in the DMZ. If you have high traffic to your apps you can add more connectors, and Application Proxy takes care of the load balancing. The connectors are stateless and pull everything from the cloud as necessary.

For information about connectors, see [Understand Azure AD Application Proxy connectors](application-proxy-understand-connectors.md). 

Every app that you publish with Application Proxy gets an external URL for your users to go to, and appears in the MyApps portal. When users access applications through one of these endpoints, they authenticate in Azure AD and then are routed through the connector to the on-premises application.

 ![AzureAD Application Proxy diagram](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. The user accesses the application through the Application Proxy and is directed to the Azure AD sign-in page to authenticate.
2. After a successful sign-in, a token is generated and sent to the user.
3. The user sends the token to Application Proxy, which retrieves the user principal name (UPN) and security principal name (SPN) from the token, then directs the request to the connector.
4. On behalf of the user, the connector requests a Kerberos ticket that can be used for internal (Windows) authentication. This step is known as Kerberos Constrained Delegation.
5. Active Directory retrieves the Kerberos ticket.
6. The ticket is sent to the application server and verified.
7. The response is sent through Application Proxy to the user.

### Single sign-on
Azure AD Application Proxy provides single sign-on (SSO) to applications that use Integrated Windows Authentication (IWA), or claims-aware applications. If your application uses IWA, Application Proxy impersonates the user using Kerberos Constrained Delegation to provide SSO. If you have a claims-aware application that trusts Azure Active Directory, SSO works because the user was already authenticated by Azure AD.

For more information about Kerberos, see [All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### Managing apps
One your app is published with Application Proxy, you can manage it like any other enterprise app in the Azure portal. You can use Azure Active Directory security features like conditional access and two-step verification, control user permissions, and customize the branding for your app. 

## Get started

Before you configure Application Proxy, make sure you have a supported [Azure Active Directory edition](active-directory-editions.md#application-proxy) and an Azure AD directory for which you are a global administrator.

Get started with Application Proxy in two steps:

1. [Enable Application Proxy and configure the connector](active-directory-application-proxy-enable.md).    
2. [Publish applications](active-directory-application-proxy-publish.md) - use the quick and easy wizard to get your on-premises apps published and accessible remotely.

## What's next?
Once you publish your first app, there's a lot more you can do with Application Proxy:

* [Enable single-sign on](active-directory-application-proxy-sso-using-kcd.md)
* [Publish applications using your own domain name](active-directory-application-proxy-custom-domains.md)
* [Learn about Azure AD Application Proxy connectors](application-proxy-understand-connectors.md)
* [Working with existing on-premises Proxy servers](application-proxy-working-with-proxy-servers.md) 
* [Set a custom home page](application-proxy-office365-app-launcher.md)

For the latest news and updates, check out the [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)

