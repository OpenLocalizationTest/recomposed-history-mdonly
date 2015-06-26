<properties
	pageTitle="Microsoft Identity | Microsoft Azure"
	description="An introduction to developing apps that integrate with Microsoft Identity."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2015"
	ms.author="dastrock"/>

# Introducing Microsoft Identity
Microsoft Identity is the union of the Microsoft Account and Azure Active Directory cloud authentication services.  With Microsoft Identity you can write your identity code once and accept sign-ins from both types of accounts.  

> [AZURE.NOTE]
This information applies to the Microsoft Identity Public Preview.  For instructions on how to integrate with the generally available Azure AD service, please refer to the [Azure Active Directory Developer Guide](active-directory-developers-guide.md).

## Overview

In the past, an app developer who wanted to support both Microsoft Accounts and Azure Active Directory accounts in their app was required to integrate with two completely separate systems.  Now, Microsoft Identity gives you the ability to sign users into your apps using both AAD and MSA accounts with a single app registration, a single library, and a single integration.

You can now quickly reach an audience that spans millions of consumer and enterprise users.  Your apps will be able to consume a single set of Microsoft APIs using either type of account, including OneDrive, OneDrive for Business, Outlook, Exchange, OneNote, and more.  You write your code once and get immediate integration to both the business and consumer worlds.

If you've developed apps using Azure Active Directory in the past, you might want to check out [Azure AD vs. Microsoft Identity](active-directory-microsoft-identity-compare.md).

## Getting Started
To get your own Microsoft Identity integrated app up & running, try out one of our quick start tutorials below.  You might also want to learn about Microsft Identity's [currently supported authentication flows](active-directory-microsoft-identity-scenarios.md).

[AZURE.INCLUDE [active-directory-msid-quickstart-table](../includes/active-directory-msid-quickstart-table.md)]

## Reference
These links will be useful for exploring the platform in depth:

- [ADAL Library Reference]()
- [Microsoft Identity Protocol Reference]()
- [Microsoft Identity FAQs](active-directory-microsoft-identity-faq.md)
- [Office 365 Rest API Reference]()
- [Scopes and Resources in Microsoft Identity](active-directory-microsoft-identity-scopes.md)
