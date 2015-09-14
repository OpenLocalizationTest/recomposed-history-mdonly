<properties
                pageTitle="Sharing accounts using Azure AD |  Microsoft Azure"
                description="Describes how Azure Active Directory enables organizations to securely share accounts for on-premises apps and consumer cloud services."
                services="active-directory"
                documentationCenter=""
                authors="msStevenPo"
                manager="msStevenPo"
                editor=""/>

<tags
                ms.service="active-directory"
                ms.workload="identity"
                ms.tgt_pltfrm="na"
                ms.devlang="na"
                ms.topic="article"
                ms.date="08/20/2015"
                ms.author="stevenpo"/>


# Sharing accounts

Often in organizations, we find ourselves in situations where a single username and password need to be used by multiple people. This typically happens in two cases:

- When accessing applications that require a unique login and password for each user, whether on-premises apps or consumer cloud services ( e.g. corporate social media accounts or (TODO: figure out an good on-prem example))
- When creating multi-user environments. You may have a single, local account that has elevated privileges and can be used to do core setup, administration, and recovery activities (e.g. the local "global administrator" account for Office 365 or the root account in Salesforce).

Traditionally, these accounts would be shared by distributing the credentials (username/password) to the right individuals or storing them in a shared location where multiple trusted agents can access them.

## What's wrong with the traditional sharing model?

The traditional sharing model has several drawbacks:
- Enabling access to new applications requires you to distribute credentials to everyone that needs access.
- Each shared application may require its own unique set of shared credentials, requiring users to remember multiple sets of credentials. When users have to remember many credentials, the risk increases that they will resort to risky practices. (e.g. writing down passwords).
- You can't tell who has access to an application.
- You can't tell who has accessed an application.
- When you need to remove access to an application, you have to update the credentials and re-distribute them to everyone that needs access to that application.

## How does Azure Active Directory help?

Azure AD provides a new approach to using shared accounts that eliminates these drawbacks. The Azure AD administrator configures which apps a user can access by using the Access Panel.  For each application, the administrator chooses the type of single sign-on best suited for that app. One of those types, password-based single-sign on, lets Azure AD act as a kind of broker during the sign-on process for that app. The administrator can easily grant or revoke access to an application and also know, at any given moment, who has access to the account and who accessed it in the past.

Users log in once by using their organizational account, the same account that they regularly use to access their desktop or email. They can discover and access only those applications that they are assigned to. With shared accounts, this list of applications can include any number of shared credentials. The end user doesn't need to remember or write down the various accounts they may be using.

Shared accounts don't only increase oversight and improve usability, they also enhance your security. Users with permissions to use the credentials don't see the shared password, but rather get permissions to use the password as part of an orchestrated authentication flow. Further, with some [password SSO applications](), you have the option to have Azure AD periodically rollover (update) the password using large, complex passwords, increasing the account security.

Azure AD supports shared accounts for any Enterprise Mobility Suite (EMS), Premium, or Basic licensed users,  across all types of password [single sign on]() applications. You can share accounts for any of thousands of pre-integrated applications in the [application gallery]() and can add your own password-authenticating application with [custom SSO apps]().

To use Azure AD to share an account you will need to:
- Add an application [app gallery]() or [custom application]()
- Configure the application for password Single Sign-On (SSO)
- Use [group based assignment](active-directory-accessmanagement-group-saasapps.md) and select the option enter a shared credential
- Optional: in some applications you can enable the option automatic password rollover.  

You can even share an account for an on-premises application by using the [Azure AD Cloud Application Proxy](active-directory-application-proxy-get-started.md) to publish an on-premises application. In this case you would set the on-premises application to use pass-through authentication. Then, you can add a [custom SSO app]() by using the external proxy URL.

Using Azure AD you can make your shared account more secure with Multi-Factor Authentication (MFA) (learn more about [securing applications with Azure AD](multi-factor-authentication-get-started.md)) and you can delegate the ability to manage who has access to the application using [Azure AD Self-service](active-directory-accessmanagement-self-service-group-management.md) Group Management.

## How can I get started?

First, if you aren't already using Azure AD and you are an IT admin:

 - [Try it out!](https://azure.microsoft.com/trial/get-started-active-directory/) - you can sign up for a free 30 trial today and deploy your first cloud solution in under 5 minutes using this link

Azure AD features that enable account sharing include:

- [Password single sign-on](active-directory-passwords-getting-started.md)
- [Password single sign-on agent]()
- [Group assignment](active-directory-accessmanagement-self-service-group-management.md)
- [Custom Password apps]()
- [App usage dashboard/reports](active-directory-passwords-get-insights.md)
- [End user access portals]()
- [App proxy](active-directory-application-proxy-get-started.md)

## Where can I learn more?

- [Protecting apps with conditional access](active-directory-conditional-access.md)
- [Self-service group management/SSAA](active-directory-accessmanagement-self-service-group-management.md)
