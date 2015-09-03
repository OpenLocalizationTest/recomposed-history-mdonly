<properties
	pageTitle="Azure Active Directory B2C | Microsoft Azure"
	description="Developing consumer-facing applications with Azure Active Directory B2C"
	services="active-directory"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/20/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C preview: Sign up & sign in consumers in your applications

**Azure Active Directory B2C** is a comprehensive identity management cloud solution for your consumer-facing web and mobile applications. It is a highly available global service that scales to hundreds of millions of consumer identities. Built on an enterprise-grade secure platform, Azure Active Directory B2C keeps your applications, your business and your consumers protected.

In the past, application developers who wanted to sign up and sign in consumers into their applications would have to use with on-premises databases or systems and write all of the identity management code themselves. Azure Active Directory B2C offers developers a better way to integrate consumer identity management into their applications with the help of a standards-based platform and a rich set of extensible policies. Using Azure Active Directory B2C allows your consumers to sign-up for your applications using their existing social accounts (Facebook, Google, Amazon, LinkedIn) or by creating new credentials (email address & password or username & password); we call the latter "local accounts".

Azure Active Directory B2C is in preview. During this time, we are eager to hear your feedback and experience as you try things out. Based on that feedback, we may make breaking changes in the interest of improving the service.  You should not release a production app using the preview during this period.

## Getting Started

There are two ways to get your own application up & running with Azure Active Directory B2C. You can choose to send protocol messages directly, using [OAuth 2.0](active-directory-b2c-protocols.md#oauth2-authorization-code-flow) or [Open ID Connect](active-directory-b2c-protocols.md#openid-connect-sign-in-flow). Alternatively you can use our libraries to do the work for you - choose your favorite platform below and get started.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## What's New

Check back here often to learn about future changes to the Azure Active Directory B2C preview. We'll also tweet about any updates using @AzureAD.

- Learn about our [extensible policy framework](active-directory-b2c-policies.md) and about the types of policies that you can create & use in your applications.
- Current [preview limitations, restrictions and constraints](active-directory-b2c-limitations.md).

## HOWTOs

Learn how to use specific Azure Active Directory B2C preview features:

- Configure ([Facebook](active-directory-setup-fb-app.md), [Google+](active-directory-setup-goog-app.md), [Amazon](active-directory-setup-amzn-app.md) and [LinkedIn](active-directory-setup-li-app.md)) accounts for use in your consumer-facing applications.
- [Use custom attributes to collect information about your consumers](active-directory-b2c-reference-custom-attr.md).
- [Enable Multi-Factor Authentication in your consumer-facing applications](active-directory-b2c-reference-mfa.md).
- [Setup self-service password reset for your consumers](active-directory-b2c-reference-sspr.md).
- [Customize the look-and-feel of sign up, sign in and other consumer-facing pages](active-directory-b2c-reference-ui-customization.md) served by Azure Active Directory B2C.
- [Use Azure Active Directory Graph API to programmatically create, read, update and delete consumers](active-directory-b2c-devquickstarts-graph-dotnet.md) in your Azure Active Directory B2C directory.

## Reference

These links will be useful for exploring the service in depth:

- Get help on Stack Overflow using the [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) or [adal](http://stackoverflow.com/questions/tagged/adal) tags.
- Give us your thoughts on the preview using [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) - we want to hear them! Use the phrase "AzureADB2C:" in the title of your post so we can find it.
- Azure Active Directory B2C supports industry standard protocols, OpenID Connect and OAuth 2.0, using an application registration model that we call "App Model v2.0".
  - [App Model v2.0 Protocol Reference](active-directory-b2c-protocols.md)
  - [App Model v2.0 Token Reference](active-directory-b2c-tokens.md)
- [Azure Active Directory B2C FAQs](active-directory-b2c-faqs.md)
