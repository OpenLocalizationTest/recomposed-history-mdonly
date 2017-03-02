---
title: Implementing password synchronization with Azure AD Connect sync | Microsoft Docs
description: Provides information about how password synchronization works and how to enable it.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''

ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2017
ms.author: markvi

---
# Implementing password synchronization with Azure AD Connect sync
This topic provides you with the information you need to synchronize your user passwords from an on-premises Active Directory (AD) to a cloud-based Azure Active Directory (Azure AD).

## What is password synchronization
The probability that you are blocked from getting your work done due to a forgotten password is related to the number of different passwords you need to remember. The more passwords you need to remember, the higher the probability to forget one. Questions and calls about password resets and other password-related issues demand the most helpdesk resources.

Password synchronization is a feature to synchronize user passwords from an on-premises Active Directory to a cloud-based Azure Active Directory (Azure AD).
This feature enables you to sign in to Azure Active Directory services (such as Office 365, Microsoft Intune, CRM Online, and Azure AD Domain Services) using the same password you are using to sign in to your on-premises Active Directory.

![What is Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

By reducing the number of passwords your users need to maintain to just one, password synchronization helps you to:

* Improve the productivity of your users
* Reduce your helpdesk costs  

Also, if you select to use [**Federation with AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), you can optionally enable password synchronization as a backup in case your AD FS infrastructure fails.

Password synchronization is an extension to the directory synchronization feature implemented by Azure AD Connect sync. To use password synchronization in your environment, you need to:

* Install Azure AD Connect  
* Configure directory synchronization between your on-premises AD and your Azure Active Directory
* Enable password synchronization

For more details, see [Integrating your on-premises identities with Azure Active Directory](active-directory-aadconnect.md)

> [!NOTE]
> For more details about Active Directory Domain Services that are configured for FIPS and password synchronization, see [Password Sync and FIPS](#password-synchronization-and-fips).
>
>

## How password synchronization works
The Active Directory domain service stores passwords in form of a hash value representation of the actual user password. A hash value is a result of a one-way mathematical function (the "*hashing algorithm*"). There is no method to revert the result of a one-way function to the plain text version of a password. You cannot use a password hash to sign in to your on-premises network.

To synchronize your password, Azure AD Connect sync extracts your password hash from the on-premises Active Directory. Extra security processing is applied to the password hash before it is synchronized to the Azure Active Directory authentication service. Passwords are synchronized on a per-user basis and in chronological order.

The actual data flow of the password synchronization process is similar to the synchronization of user data such as DisplayName or Email Addresses. However, passwords are synchronized more frequently than the standard directory synchronization window for other attributes. The password synchronization process runs every 2 minutes. You cannot modify the frequency of this process. When you synchronize a password, it overwrites the existing cloud password.

The first time, you enable the password synchronization feature, it performs an initial synchronization of the passwords of all in-scope users. You cannot explicitly define a subset of user passwords you want to synchronize.

When you change an on-premises password, the updated password is synchronized, most often in a matter of minutes.
The password synchronization feature automatically retries failed synchronization attempts. If an error occurs during an attempt to synchronize a password, an error is logged in your event viewer.

The synchronization of a password has no impact on the currently logged on user.
Your current cloud service session is not immediately affected by a synchronized password change that occurs while you are signed in to a cloud service. However, when the cloud service requires you to authenticate again, you need to provide your new password.

> [!NOTE]
> Password sync is only supported for the object type user in Active Directory. It is not supported for the iNetOrgPerson object type.
>
>

### How password synchronization works with Azure AD Domain Services
You can also use the password synchronization feature to synchronize your on-premises passwords to the [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). This scenario allows the Azure AD Domain Services to authenticate your users in the cloud with all the methods available in your on-premises AD. The experience of this scenario is similar to using the Active Directory Migration Tool (ADMT) in an on-premises environment.

### Security considerations
When synchronizing passwords, the plain-text version of your password is not exposed to the password synchronization feature, to Azure AD, or any of the associated services.

Also, there is no requirement on the on-premises Active Directory to store the password in a reversibly encrypted format. A digest of the Active Directory password hash is used for the transmission between the on-premises AD and Azure Active Directory. The digest of the password hash cannot be used to access resources in your on-premises environment.

### Password policy considerations
There are two types of password policies that are affected by enabling password synchronization:

1. Password Complexity Policy
2. Password Expiration Policy

**Password complexity policy**  
When you enable password synchronization, the password complexity policies in your on-premises Active Directory override complexity policies in the cloud for synchronized users. You can use all valid passwords of your on-premises Active Directory to access Azure AD services.

> [!NOTE]
> Passwords for users that are created directly in the cloud are still subject to password policies as defined in the cloud.
>
>

**Password expiration policy**  
If a user is in the scope of password synchronization, the cloud account password is set to "*Never Expire*".
You can continue to sign in to your cloud services using a synchronized password that has been expired in your on-premises environment. Your cloud password is updated the next time you change the password in the on-premises environment.

### Overwriting synchronized passwords
An administrator can manually reset your password using Windows PowerShell.

In this case, the new password overrides your synchronized password and all password policies defined in the cloud are applied to the new password.

If you change your on-premises password again, the new password is synchronized to the cloud, and overrides the manually updated password.

## Enabling password synchronization
Password synchronization is automatically enabled, when you install Azure AD Connect using the **Express Settings**. For more details, see [Getting started with Azure AD Connect using express settings](active-directory-aadconnect-get-started-express.md).

If you use custom settings when you install Azure AD Connect, you enable password synchronization on the user sign-in page. For more details, see [Custom installation of Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Enabling password synchronization](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### Password synchronization and FIPS
If your server has been locked down according to Federal Information Processing Standard (FIPS), then MD5 has been disabled.

**To enable MD5 for password synchronization, perform the following steps:**

1. Go to **%programfiles%\Azure AD Sync\Bin**.
2. Open **miiserver.exe.config**.
3. Go to the **configuration/runtime** node (at the end of the file).
4. Add the following node: `<enforceFIPSPolicy enabled="false"/>`
5. Save your changes.

For reference, this snip is how it should look like:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

For information about security and FIPS see [AAD Password Sync, Encryption and FIPS compliance](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## Troubleshooting password synchronization
If you have problems with password synchronization, then see [Troubleshoot password synchronization](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## Next steps
* [Azure AD Connect Sync: Customizing Synchronization options](active-directory-aadconnectsync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory](active-directory-aadconnect.md)
