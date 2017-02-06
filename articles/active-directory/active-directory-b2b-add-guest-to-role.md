---
title: Adding an Azure Active Directory B2B collaboration user to a role | Microsoft Docs
description: Azure Active Directory B2B collaboration user properties are configurable
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
ms.date: 02/03/2017
ms.author: sasubram

---

# Adding an Azure Active Directory B2B collaboration user to a role

While Azure Active Directory (Azure AD) B2B collaboration users are added as guest users to the directory and guest permissions in the directory are restricted by default, your business may need some guest users to fill more privilege roles in your organization. To support this, guest users can be added to any roles you desire, based on your organization's needs.

## Default Role

![default role](media/active-directory-b2b-add-guest-to-role/default-role.png)

## Global Administrator Role

![global admin role](media/active-directory-b2b-add-guest-to-role/global-admin-role.png)

## Limited Administrator Role

![limited admin role](media/active-directory-b2b-add-guest-to-role/limited-admin-role.png)

## Auditing and reporting
With guest users, you now get auditing capabilities on par with member users. Here's an example below of the invitation and redemption history of the just invited Sam Oogle:

![audit log](media/active-directory-b2b-add-guest-to-role/audit-log.png)

You can dive into each of these events to get the details. For example, let's look at the acceptance details.

![activity details](media/active-directory-b2b-add-guest-to-role/activity-details.png)

You can also export these logs from Azure AD and use the reporting tool of your choice in order to get customized reports.

## Resending Invitations
It is now possible to go to a B2B collaboration user's profile page and resend invitations to not-yet-redeemed guest users for any reason:

![resend invitation](media/active-directory-b2b-add-guest-to-role/resend-invitation.png)

> ![NOTE]
> This will resend an invitation from the signed-in user and invite the user to the directory, even if the original invitation was to a specific app or group.

## Next steps

Browse our other articles on Azure AD B2B collaboration:

* [What is Azure AD B2B collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B collaboration user properties](active-directory-b2b-user-properties.md)
* [Delegate B2bB collaboration invitations](active-directory-delegate-invitations.md)
* [Dynamic groups and B2B collaboration](active-directory-b2b-dynamic-groups.md)
* [B2B collaboration code and PowerShell samples](active-directory-b2b-code-samples.md)
* [Configure SaaS apps for B2B collaboration](active-directory-b2b-configure-saas-apps.md)
* [B2B collaboration user tokens](active-directory-b2b-user-token.md)
* [B2B collaboration user claims mapping](active-directory-b2b-claims-mapping.md)
* [Office 365 external sharing](active-directory-b2b-o365-external-user.md)
* [B2B collaboration current limitations](active-directory-b2b-current-limitations.md)
