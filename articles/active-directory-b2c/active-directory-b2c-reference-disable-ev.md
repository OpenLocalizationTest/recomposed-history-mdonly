---
title: 'Azure Active Directory B2C: Disable Email Verification During User Sign-up | Microsoft Docs'
description: A topic demonstrating how to disable email verification during user sign-up in Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: krassk
editor: parakhj

ms.assetid: 433f32b8-96d2-4113-aa82-efcf42fa9827
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/26/2017
ms.author: parakhj

---
# Azure Active Directory B2C: Disable email verification during user sign-up
Azure Active Directory (Azure AD) B2C gives your users the ability to sign up for your application by providing their email address and creating a local account. When you enable this feature, Azure AD B2C forces your users to verify their email address during the sign-up process. This feature ensures that the new users are entering a valid email address. It also prevents a malicious automated process from generating fake accounts for your application.

We have received feedback that some application developers would prefer skipping the email verification during the sign-up process, and choose to have their users verify the email address later. Disabling email verification creates a more frictionless sign-up process and helps developers differentiate between the users that have verified their email address from those users that have not. To support this, we have added an option that disables email verification and gives you more control over the sign-up process.

By default, your sign-up policy has email verification turned on. Use the following steps to turn it off:

1. [Follow these steps to navigate to the B2C features blade on the Azure portal](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Click **Sign-up policies** or **Sign-up or sign-in policies** depending on what you configured for sign-up.
3. Click your policy (for example, "B2C_1_SiUp") to open it. Click **Edit** at the top of the blade.
4. Click **Page UI Customization**.
5. Click **Local account sign-up page**.
6. Click **Email Address** in the **Name** column under the **Sign-up attributes** section.
7. Toggle the **Require verification** option to **No**.
8. Click **OK** at the bottom until you reach the **Edit policy** blade.
9. Click **Save** at the top of the blade. You're done!

> [!NOTE]
> Disabling email verification in the sign-up process may lead to spam. If you disable the default one, we recommend adding your own verification system.
> 
> 

We are always open to feedback and suggestions! If you have any difficulties with this topic, or recommendations for improving this content, we would appreciate your feedback at the bottom of the page. For feature requests, add them to [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).