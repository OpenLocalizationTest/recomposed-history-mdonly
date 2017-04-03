---
title: 'Azure AD Connect: Accounts and permissions | Microsoft Docs'
description: This topic describes the accounts used and created and permissions required.
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: ''

ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: billmath

---
# Azure AD Connect: Accounts and permissions
The Azure AD Connect installation wizard offers two different paths:

* In Express Settings, the wizard requires more privileges so that it can setup your configuration easily, without requiring you to create users or configure permissions separately.
* In Custom Settings, the wizard offers you more choices and options, but there are some situations in which you need to ensure you have the correct permissions yourself.

## Related documentation
If you did not read the documentation on [Integrating your on-premises identities with Azure Active Directory](../active-directory-aadconnect.md), the following table provides links to related topics.

|Topic |Link|  
| --- | --- |
|Download Azure AD Connect | [Download Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Install using Express settings | [Express installation of Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Install using Customized settings | [Custom installation of Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Upgrade from DirSync | [Upgrade from Azure AD sync tool (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|After installation | [Verify the installation and assign licenses ](active-directory-aadconnect-whats-next.md)|

## Express settings installation
In Express settings, the installation wizard asks for AD DS Enterprise Admin credentials so your on-premises Active Directory can be configured with required permissions for Azure AD Connect. If you are upgrading from DirSync, the AD DS Enterprise Admins credentials are used to reset the password for the account used by DirSync. You also need Azure AD Global Administrator credentials.

| Wizard Page | Credentials Collected | Permissions Required | Used For |
| --- | --- | --- | --- |
| N/A |User running the installation wizard |Administrator of the local server |<li>Creates the local account that is used as the [sync engine service account](#azure-ad-connect-sync-service-account). |
| Connect to Azure AD |Azure AD directory credentials |Global administrator role in Azure AD |<li>Enabling sync in the Azure AD directory.</li>  <li>Creation of the [Azure AD account](#azure-ad-service-account) that is used for on-going sync operations in Azure AD.</li> |
| Connect to AD DS |On-premises Active Directory credentials |Member of the Enterprise Admins (EA) group in Active Directory |<li>Creates an [account](#active-directory-account) in Active Directory and grants permissions to it. This created account is used to read and write directory information during synchronization.</li> |

### Enterprise Admin credentials
These credentials are only used during the installation and are not used after the installation has completed. The Enterprise Admin, not the Domain Admin should make sure the permissions in Active Directory can be set in all domains.

### Global Admin credentials
These credentials are only used during the installation and are not used after the installation has completed. It is used to create the [Azure AD account](#azure-ad-service-account) used for synchronizing changes to Azure AD. The account also enables sync as a feature in Azure AD.

### Permissions for the created AD DS account for express settings
The [account](#active-directory-account) created for reading and writing to AD DS have the following permissions when created by express settings:

| Permission | Used for |
| --- | --- |
| <li>Replicate Directory Changes</li><li>Replicate Directory Changes All |Password sync |
| Read/Write all properties User |Import and Exchange hybrid |
| Read/Write all properties iNetOrgPerson |Import and Exchange hybrid |
| Read/Write all properties Group |Import and Exchange hybrid |
| Read/Write all properties Contact |Import and Exchange hybrid |
| Reset password |Preparation for enabling password writeback |

## Custom settings installation
When using custom settings, the account used to connect to Active Directory must be created before the installation. The permissions you must grant this account can be found in [create the AD DS account](#create-the-ad-ds-account).

| Wizard Page | Credentials Collected | Permissions Required | Used For |
| --- | --- | --- | --- |
| N/A |User running the installation wizard |<li>Administrator of the local server</li><li>If using a full SQL Server, the user must be System Administrator (SA) in SQL</li> |By default, creates the local account that is used as the [sync engine service account](#azure-ad-connect-sync-service-account). The account is only created when the admin does not specify a particular account. |
| Install synchronization services, Service account option |AD or local user account credentials |User, permissions are granted by the installation wizard |If the admin specifies an account, this account is used as the service account for the sync service. |
| Connect to Azure AD |Azure AD directory credentials |Global administrator role in Azure AD |<li>Enabling sync in the Azure AD directory.</li>  <li>Creation of the [Azure AD account](#azure-ad-service-account) that is used for on-going sync operations in Azure AD.</li> |
| Connect your directories |On-premises Active Directory credentials for each forest that is connected to Azure AD |The permissions depend on which features you enable and can be found in [Create the AD DS account](#create-the-ad-ds-account) |This account is used to read and write directory information during synchronization. |
| AD FS Servers |For each server in the list, the wizard collects credentials when the logon credentials of the user running the wizard are insufficient to connect |Domain Administrator |Installation and configuration of the AD FS server role. |
| Web application proxy servers |For each server in the list, the wizard collects credentials when the logon credentials of the user running the wizard are insufficient to connect |Local admin on the target machine |Installation and configuration of WAP server role. |
| Proxy trust credentials |Federation service trust credentials (the credentials the proxy uses to enroll for a trust certificate from the FS |Domain account that is a local administrator of the AD FS server |Initial enrollment of FS-WAP trust certificate. |
| AD FS Service Account page, "Use a domain user account option" |AD user account credentials |Domain user |The AD user account whose credentials are provided is used as the logon account of the AD FS service. |

### Create the AD DS account
When you install Azure AD Connect, the account you specify on the **Connect your directories** page must be present in Active Directory and have required permissions granted. The installation wizard does not verify the permissions and any issues are only found during synchronization.

Which permissions you require depends on the optional features you enable. If you have multiple domains, the permissions must be granted for all domains in the forest. If you do not enable any of these features, the default **Domain User** permissions are sufficient.

| Feature | Permissions |
| --- | --- |
| Password sync |<li>Replicate Directory Changes</li>  <li>Replicate Directory Changes All |
| Exchange hybrid deployment |Write permissions to the attributes documented in [Exchange hybrid writeback](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) for users, groups, and contacts. |
| Password writeback |Write permissions to the attributes documented in [Getting started with password management](../active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) for users. |
| Device writeback |Permissions granted with a PowerShell script as described in [device writeback](active-directory-aadconnect-feature-device-writeback.md). |
| Group writeback |Read, Create, Update, and Delete group objects in the OU where the distributions groups should be located. |

## Upgrade
When you upgrade from one version of Azure AD Connect to a new release, you need the following permissions:

| Principal | Permissions required | Used for |
| --- | --- | --- |
| User running the installation wizard |Administrator of the local server |Update binaries. |
| User running the installation wizard |Member of ADSyncAdmins |Make changes to Sync Rules and other configuration. |
| User running the installation wizard |If you use a full SQL server: DBO (or similar) of the sync engine database |Make database level changes, such as updating tables with new columns. |

## More about the created accounts
### Active Directory account
If you use express settings, then an account is created in Active Directory that is used for synchronization. The created account is located in the forest root domain in the Users container and has its name prefixed with **MSOL_**. The account is created with a long complex password that does not expire. If you have a password policy in your domain, make sure long and complex passwords would be allowed for this account.

![AD account](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

If you use custom settings, then you are responsible for creating the account before you start the installation.

### Azure AD Connect sync service account
The sync service can run under different accounts. It can run under a **Virtual Service Account** (VSA), a **Group Managed Service Account** (gMSA/sMSA), or a regular user account. The supported options were changed with the 2017 April release of Connect when you do a fresh installation. If you upgrade from an earlier release of Azure AD Connect, these additional options are not available.

| Type of account | Installation option | Description |
| --- | --- | --- |
| [Virtual Service Account](#virtual-service-account) | Express and custom, 2017 April and later | This is the option used for all express installations. For custom it is the default option unless another option is used. |
| [Group Managed Service Account](#group-managed-service-account) | Custom, 2017 April and later | If you use a remote SQL server, then we recommend to use a group managed service account. |
| [User account](#user-account) | Express and custom, 2017 April and later | A user account prefixed with AAD_ is only created during installation when installed on Windows Server 2008. |
| [User account](#user-account) | Express and custom, 2017 March and earlier | A local account prefixed with AAD_ is created during installation. When using custom installation, another account can be specified. |

If you use Connect with a build from 2017 March or earlier, then you should not reset the password on the service account since Windows destroys the encryption keys for security reasons. You cannot change the account to any other account without reinstalling Azure AD Connect. If you upgrade to a build from 2017 April or later, then it is supported to change the password on the service account.

If you originally installed Connect with a build from 2017 April or later, then it is supported to change the account to another account. You can create another type of account and simply change the account in services. The encryption keys used to access the passwords for the other accounts are managed by the server. This continues to be the case even if you change the service account to another account, making this a supported scenario.

> [!Important]
> An upgrade from a build from before 2017 April does not add Virtual Service Accounts and Managed Service Accounts to the supported options. These options are only available on fresh installations.

This is a table of the default and recommended option for the sync service account. Where there are multiple rows in a cell, it lists multiple options.

| | LocalDB</br>Express or Custom | Local SQL</br>Custom | Remote SQL</br>Custom |
| --- | --- | --- | --- |
| **standalone/workgroup machine** | VSA (custom)</br>Local user account (2008)</br>Not supported (express) | VSA</br>Local user account (2008) | Not supported |
| **domain-joined machine** | VSA</br>user account (2008) | VSA </br>user account (2008) | gMSA |
| **Domain Controller** | Domain user account | gMSA | gMSA |

Legend:  
2008 - When installed on Windows Server 2008  
Local user account - Local user account only  
Domain user account - Domain user account only  
user account - Domain or Local user account  

For a full list, see [Supported options for the Azure AD sync service account](#supported-options-for-the-azure-ad-sync-service- account).

#### Virtual service account
A virtual service account is a special type of account that does not have a password and is managed by Windows.

![VSA](./media/active-directory-aadconnect-accounts-permissions/aadsyncvsa.png)

The VSA is intended to be used with scenarios where the sync engine and SQL are on the same server. If you use remote SQL, then we recommend to use a [Group Managed Service Account](#managed-service-account) instead.

This feature requires Windows Server 2008 R2 or later. If you install Azure AD Connect on Windows Server 2008, then the installation falls back to using a [user account](#user-account) instead.

#### Group managed service account
If you use a remote SQL server, then we recommend to use a **Group Managed Service Account**. For more information on how to prepare your Active Directory for Group Managed Service account, see [Group Managed Service Accounts Overview](https://technet.microsoft.com/library/hh831782.aspx).

To use this option, on the [Install required components](active-directory-aadconnect-get-started-custom.md#install-required-components) page, select **Use an existing service account**, and select **Managed Service Account**.  
![VSA](./media/active-directory-aadconnect-accounts-permissions/serviceaccount.png)  
It is also supported to use a [standalone managed service account](https://technet.microsoft.com/library/dd548356.aspx). However, since these can only be used on the local machine, there is no practical benefit to use these over the default virtual service account.

This feature requires Windows Server 2012 or later. If you need to use an older operating system and use remote SQL, then you must use a [user account](#user-account).

#### User account
A local service account is created by the installation wizard (unless you specify the account to use in custom settings). The account is prefixed **AAD_** and used for the actual sync service to run as. If you install Azure AD Connect on a Domain Controller, the account is created in the domain. If you use a remote server running SQL server or if you use a proxy that requires authentication, the **AAD_** service account must be located in the domain.

![Sync Service Account](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

The account is created with a long complex password that does not expire.

This account is used to store the passwords for the other accounts in a secure way. These other accounts passwords are stored encrypted in the database. The private keys for the encryption keys are protected with the cryptographic services secret-key encryption using Windows Data Protection API (DPAPI).

If you use a full SQL Server, then the service account is the DBO of the created database for the sync engine. The service will not function as intended with any other permissions. A SQL login is also created.

The account is also granted permissions to files, registry keys, and other objects related to the Sync Engine.

#### Supported options for the Azure AD sync service account
This table lists all supported options for the sync service account.

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Custom | Remote SQL</br>Custom |
| --- | --- | --- | --- |
| **standalone/workgroup machine** | Not supported | VSA</br>Local user account |  Not supported |
| **domain-joined machine** | VSA</br>Local service account (2008) | VSA</br>user account</br>sMSA,gMSA | Domain user account</br>gMSA |
| **Domain Controller** | VSA</br>Domain user account| VSA</br>Domain user account</br>sMSA,gMSA| Domain user account</br>gMSA |

Legend:  
2008 - When installed on Windows Server 2008  
Local user account - Local user account only  
Domain user account - Domain user account only  
user account - Domain or Local user account  

### Azure AD service account
An account in Azure AD is created for the sync service's use. This account can be identified by its display name.

![AD account](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

The name of the server the account is used on can be identified in the second part of the user name. In the picture, the server name is FABRIKAMCON. If you have staging servers, each server has its own account. There is a limit of 10 sync service accounts in Azure AD.

The service account is created with a long complex password that does not expire. It is granted a special role **Directory Synchronization Accounts** that has only permissions to perform directory synchronization tasks. This special built-in role cannot be granted outside the Azure AD Connect wizard and the Azure portal shows this account with the role **User**.

![AD account Role](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](../active-directory-aadconnect.md).
