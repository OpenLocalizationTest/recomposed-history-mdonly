<properties
	pageTitle="Azure AD Domain Services: Compare Azure AD Domain Services to DIY domain controllers | Microsoft Azure"
	description="Comparing Azure Active Directory Domain Services to DIY domain controllers"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="maheshu"/>

# How to decide if Azure AD Domain Services is right for your use-case

## Differences between Azure AD Domain Services and a 'do-it-yourself' AD domain in Azure

|Feature|Azure AD Domain Services|'Do-it-yourself' AD in Azure VMs|
|---|:---:|:---:|
|[**Managed service**](active-directory-ds-comparison.md#managed-service)|:white_check_mark:|:negative_squared_cross_mark:|
|[**Secure deployments**](active-directory-ds-comparison.md#secure-deployments)|:white_check_mark:|Administrator needs to secure the deployment.|
|[**DNS server**](active-directory-ds-comparison.md#dns-server)|:white_check_mark: (managed service)|:white_check_mark:|
|[**Domain or Enterprise administrator privileges**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|:negative_squared_cross_mark:|:white_check_mark:|
|[**Custom OU structure**](active-directory-ds-comparison.md#custom-ou-structure)|:white_check_mark:|:white_check_mark:|
|[**Schema extensions**](active-directory-ds-comparison.md#schema-extensions)|:negative_squared_cross_mark:|:white_check_mark:|
|[**AD domain/forest trusts**]|:negative_squared_cross_mark:|:white_check_mark:|
|[**LDAP write**](active-directory-ds-comparison.md#ldap-write)|:negative_squared_cross_mark:|:white_check_mark:|
|[**Group Policy**](active-directory-ds-comparison.md#group-policy)|Simple|Full|
|[**Geo-dispersed deployments**](active-directory-ds-comparison.md#geo-dispersed-deployments)|:negative_squared_cross_mark:|:white_check_mark:|

#### Managed service
Azure AD Domain Services domains are managed by Microsoft. You do not have to worry about patching, updates, monitoring, backups, and ensuring availability of your domain. These management tasks are offered as a service by Microsoft Azure for your managed domains.

#### Secure deployments
The managed domain is securely locked down as per Microsoft’s security best practices for AD deployments. These best practices stem from the AD product team's decades of experience engineering and supporting AD deployments. For do-it-yourself deployments, you need to take specific deployment steps to lock down/secure your deployment.

#### Custom OU structure
Members of the 'AAD DC Administrators' group can create custom OUs within the managed domain.

#### DNS server
An Azure AD Domain Services managed domain includes managed DNS services. Members of the 'AAD DC Administrators' group can manage DNS on the managed domain. Members of this group are given full DNS Administration privileges for the managed domain. DNS management can be performed using the 'DNS Administration console' included in the Remote Server Administration Tools (RSAT) package.

#### Domain or Enterprise Administrator privileges
These elevated privileges are not offered on an AAD-DS managed domain. Applications that require these elevated privileges to be installed/run cannot be run against managed domains. A smaller subset of administrative privileges is available to members of the delegated administration group called ‘AAD DC Administrators’. These privileges include privileges to configure DNS, configure group policy, gain administrator privileges on domain-joined machines etc.

#### Schema extensions
You cannot extend the base schema of an Azure AD Domain Services managed domain. Therefore, applications that rely on extensions to AD schema (for example, new attributes under the user object) cannot be lifted and shifted to AAD-DS domains.

#### AD Domain/Forest Trusts
Managed domains cannot be configured to set up trust relationships (inbound/outbound) with other domains. Therefore, scenarios such as resource forest deployments or cases where you prefer not to synchronize passwords to Azure AD cannot use Azure AD Domain Services.

#### LDAP Write
The managed domain is read-only for user objects. Therefore, applications that perform LDAP write operations against attributes of the user object do not work in a managed domain. Additionally, user passwords cannot be changed from within the managed domain. Another example would be modification of group memberships or group attributes within the managed domain, which is not permitted. However, any changes to user attributes or passwords made in Azure AD (via PowerShell/Azure portal) or on-premises AD are synchronized to the AAD-DS managed domain.

#### Group policy
Sophisticated group policy constructs aren’t supported on the AAD-DS managed domain. For example, you cannot create and deploy separate GPOs for each custom OU in the domain or use WMI filtering for GP targeting. There is a built-in GPO each for the ‘AADDC Computers’ and ‘AADDC Users’ containers, which can be customized to configure group policy.

#### Geo-dispersed deployments
Azure AD Domain Services managed domains are available in a single virtual network in Azure. For scenarios that require domain controllers to be available in multiple Azure regions across the world, setting up domain controllers in Azure IaaS VMs might be the better alternative.


## Related Content
- [Features - Azure AD Domain Services](active-directory-ds-features.md)

- [Guidelines for Deploying Windows Server Active Directory on Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx)
