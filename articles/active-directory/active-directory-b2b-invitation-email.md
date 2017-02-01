---
title: The elements of the B2B collaboration invitation email | Microsoft Docs
description: Azure Active Directory B2B collaboration invitation email template
services: active-directory
documentationcenter: ''
author: sasubram
manager: femila
editor: ''
tags: ''

ms.assetid:
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 09/27/2017
ms.author: sasubram

---


# The elements of the B2B collaboration invitation email

Invitation emails are a critical component to bring partners on board as B2B collaboration users in Azure AD. The primary goal for this is to increase trust in the recipient and add legitimacy and social proof to the email, to make sure the recipient feels comfortable with selecting the **Get Started** button to accept the invitation. This is a key component to reducing sharing friction. And of course, we also want the email to look great!

## Explaining the email
Let's look at a few elements of the email so you know how best to make use of these capabilities.

### Subject
The subject of the email follows the following pattern:
You're invited to the &lt;tenantname&gt; organization

### From address
We use a LinkedIn-like pattern for the From address. Our goal here is to be clear who the inviter is and from which company and also clarify that the email is coming from a Microsoft email address. The format is:
&lt;Display name of inviter&gt; from &lt;tenantname&gt; (via Microsoft) <invites@microsoft.com&gt;

### Reply To
The reply to email is set to the inviter's email when available, so that replying to the email will send an email back to the inviter.

### Branding
The invitation emails from your tenant use the company branding that you may have set up for your tenant. If you want to take advantage of this, here are the details on how to configure it. The banner logo will show up in the email. Follow the image size and quality instructions here for best results. In addition, the company name also shows up in the call to action.

### Call to action
The call to action consists of two parts: explaining why the recipient has received the mail and what the recipient is being asked to do about it.
- The "why" section can be addressed using the following pattern:
  You've been invited to access applications in the &lt;tenantname&gt; organization

- And the "what you're being asked to do" section is indicated by the presence of the **Get Started** button. When the recipient has been added without the need for invitations, this button doesn't show up.

### Inviter's information
The inviter's display name will be included in the email. And in addition, if you've set up a profile picture for your Azure AD account, the inviting email will include that picture as well. Both of these are intended to increase your recipient's confidence in the email.
If the inviter hasn't yet set up their profile picture, Azure AD creates an icon with the inviter's initials in place of the picture as shown:

  ![displaying the inviter's initials](media/active-directory-b2b-invitation-email/inviters-initials.png)

### Body
This will contain the message that the inviter typed in the UX or passed through the invitation API. This is just a simple text box, which will not process html tags for security reasons.

### Footer
The footer contains the Microsoft company brand and will let the recipient know if the email was sent from an unmonitored alias. Special cases:

- The inviter doesn't have an email address in the inviting tenancy

  ![picture of inviter doesn't have an email address in the inviting tenancy](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- The recipient doesn't need to redeem the invitation

  ![when recipient doesn't need to redeem invitation](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## Related articles

Browse our other articles on Azure AD B2B collaboration:

* [What is Azure AD B2B collaboration](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure AD B2B collaboration licensing](active-directory-b2b-licensing.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
