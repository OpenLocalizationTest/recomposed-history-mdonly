---
title: 'Azure AD Connect: Pass-through authentication | Microsoft Docs'
description: This article describes Azure Active Directory (Azure AD) pass-through authentication and how it allows Azure AD sign-ins by validating users' passwords against on-premises Active Directory.
services: active-directory
keywords: what is Azure AD Connect Pass-through Authentication, install Active Directory, required components for Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: billmath
---

# User sign-in with Azure Active Directory Pass-through Authentication

## What is Azure Active Directory Pass-through Authentication?

Azure Active Directory (Azure AD) Pass-through Authentication allows your users to sign in to both on-premises and cloud-based applications using the same passwords. This provides a better user experience - one less password for a user to remember, and reduces IT helpdesk costs because users are less likely to forget how to sign in. When users sign in using Azure AD, this feature validates users' passwords directly against your on-premises Active Directory.

This feature is an alternative to [Azure AD Password Hash Synchronization](active-directory-aadconnectsync-implement-password-synchronization.md), which provides the same benefit to organizations. Password Hash Synchronization works by synchronizing users' passwords, in a hashed form, to Azure AD. However, some organizations' security and compliance policies don't allow this. Pass-through Authentication is the right solution for such organizations.

![Azure AD Pass-through Authentication](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

You can combine Pass-through Authentication with the [Seamless Single Sign-on](active-directory-aadconnect-sso.md) feature. This way, when your users are accessing applications on their corporate machines inside your corporate network, they won't even need to type in their passwords to sign in -- a truly integrated experience.

## The key benefits of Azure AD Pass-through Authentication

- Great user experience
  - Users use the same passwords to sign into both on-premises and cloud-based applications.
  - Users spend less time talking to the IT helpdesk resolving password-related issues.
- Easy to deploy & administer
  - No need for complex on-premises deployments or network configuration.
  - Needs just a lightweight agent to be installed on-premises.
  - No management overhead; the agent automatically receives improvements and bug fixes.
- Secure
  - On-premises passwords are never stored in the cloud in any form.
  - The agent only makes outbound connections from within your network. Therefore, there is no requirement to install the agent in a perimeter network, also known as a DMZ.
  - Works seamlessly with Azure Multi-Factor Authentication (MFA).
- Highly available
  - Additional agents can be installed on multiple on-premises servers to provide high availability of sign-in.

## What's available during preview?

>[!NOTE]
>Azure AD pass-through authentication is currently in preview. It is a free feature, and you don't need any paid editions of Azure AD to use it.

The following scenarios are fully supported during preview:

- All web browser-based applications
- Office 365 client applications that support [modern authentication](https://aka.ms/modernauthga)

The following scenarios are _not_ supported during preview:

- Legacy Office client applications and Exchange ActiveSync (that is, native email applications on mobile devices). <br>Organizations are encouraged to switch to modern authentication, if possible. This allows for pass-through authentication support but also helps you secure your identities by using [conditional access](../active-directory-conditional-access.md) features such as Multi-Factor Authentication.
- Azure AD Join for Windows 10 devices.

>[!IMPORTANT]
>As a workaround for scenarios that the pass-through authentication feature doesn't support today (legacy Office client applications, Exchange ActiveSync, and Azure AD Join for Windows 10 devices), password synchronization is also enabled by default when you enable pass-through authentication. Password synchronization acts as a fallback in these specific scenarios only. If you don't need this, you can turn off password synchronization on the [Optional features](active-directory-aadconnect-get-started-custom.md#optional-features) page in the Azure AD Connect wizard.

### Prerequisites

Before you can enable Azure AD pass-through authentication, you need to have the following in place:

- An Azure AD tenant for which you are a global administrator.

  >[!NOTE]
  >We highly recommended that the Global Administrator account be a cloud-only account. This way, you can manage the configuration of your tenant should your on-premises services fail or become unavailable. Learn about [adding a cloud-only Global Administrator account](../active-directory-users-create-azure-portal.md).

- Azure AD Connect version 1.1.486.0 or later. We recommend that you use the [latest version of Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
- A server running Windows Server 2012 R2 or later on which to run Azure AD Connect.
  - This server must be a member of the same AD forest as the users whose passwords need to be validated.
  - Note that a pass-through authentication agent is installed on the same server as Azure AD Connect. Verify that the agent version is 1.5.58.0 or later.

    >[!NOTE]
    >Multi-forest environments are supported if there are forest trusts between the AD forests and if name suffix routing is correctly configured.

- If you want high availability, you will need additional servers running Windows Server 2012 R2 or later to install standalone agents. (The agent version needs to be 1.5.58.0 or later.)
- If there is a firewall between any of the agents and Azure AD:
	- If URL filtering is enabled, ensure that the agents can communicate with the following URLs:
		-  \*.msappproxy.net
		-  \*.servicebus.windows.net
	- Ensure that the agents also make direct IP connections to the [Azure data center IP ranges](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
	- Ensure that the firewall does not perform SSL inspection, because the agents use client certificates to communicate with Azure AD.
	- Ensure that the agents can make outbound requests to Azure AD over ports 80 and 443.
      - If your firewall enforces rules according to originating users, open these ports for traffic coming from Windows services running as a network service.
      - The agents make HTTP requests over port 80 for downloading SSL certificate revocation lists. This is also needed for the automatic update capability to function properly.
      - The agents make HTTPS requests over port 443 for all other operations such as enabling and disabling the feature, registering agents, downloading agent updates, and handling all user sign-in requests.

     >[!NOTE]
     >We have recently made improvements to reduce the number of ports required by the agents to communicate with our service. If you are running older versions of Azure AD Connect and/or standalone agents, you should continue to keep those additional ports (5671, 8080, 9090, 9091, 9350, 9352, 10100-10120) open.

## Enable Azure AD pass-through authentication

Azure AD pass-through authentication can be enabled via Azure AD Connect.

If you are performing a new installation of Azure AD Connect, choose the [custom installation path](active-directory-aadconnect-get-started-custom.md). At the **User sign-in** page, select **Pass-through authentication**. On successful completion, this installs a pass-through authentication agent on the same server as Azure AD Connect. In addition, it enables the pass-through authentication feature on your tenant.

![Azure AD Connect - user sign-in](./media/active-directory-aadconnect-sso/sso3.png)

If you have already installed Azure AD Connect, set it up by using the [express installation](active-directory-aadconnect-get-started-express.md) or the [custom installation](active-directory-aadconnect-get-started-custom.md) path, select **Change user sign-in page** on Azure AD Connect, and click **Next**. Then select **Pass-through authentication** to install a pass-through authentication agent on the same server as Azure AD Connect and enable the feature on your tenant.

![Azure AD Connect - Change user sign-in](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Azure AD pass-through authentication is a tenant-level feature. It affects user sign-in across all managed domains in your tenant. However, users from federated domains will continue to sign in by using Active Directory Federation Services (AD FS) or any other federation provider that you have previously configured. If you convert a domain from federated to managed, all users in that domain automatically start signing in by using pass-through authentication. Cloud-only users are not affected by pass-through authentication.

If you are planning to use pass-through authentication in a production deployment, we highly recommend that you install a second agent on a separate server (other than the one running Azure AD Connect and the first agent). Doing this ensures that you have high availability of sign-in requests. You can install as many additional agents as you need. This is based on the peak and average number of sign-in requests that your tenant handles.

Follow these instructions to deploy a standalone agent.

#### Step 1: Download and install the agent

In this step, you download and install the agent software on your server.

1.	[Download](https://go.microsoft.com/fwlink/?linkid=837580) the latest agent. Verify that the agent version is 1.5.58.0 or later.
2.	Open the command prompt as an administrator.
3.	Run the following command. The **/q** option means "quiet installation"--the installation does not prompt you to accept the End User License Agreement:
`
AADApplicationProxyagentInstaller.exe REGISTERagent="false" /q
`

>[!NOTE]
>You can install only a single agent per server.

#### Step 2: Register the agent with Azure AD for pass-through authentication

In this step, you register the installed agent on your server with our service and make it available to receive sign-in requests.

1.	Open a PowerShell window as an administrator.
2.	Navigate to **C:\Program Files\Microsoft AAD App Proxy agent** and run the script as follows:
`.\Registeragent.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy agent\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.	When prompted, enter the credentials of the Global Administrator account on your Azure AD tenant.

## How does Azure AD pass-through authentication work?

When a user attempts to sign in to Azure AD (and if pass-through authentication is enabled on the tenant), the following occurs:

1. The user enters their username and password into the Azure AD sign-in page. Our service places the username and password (encrypted by using a public key) on a queue for validation.
2. One of the available on-premises agents makes an outbound call to the queue and retrieves the username and password.
3. The agent then validates the username and password against Active Directory by using standard Windows APIs (a similar mechanism to what is used by AD FS). Note that the username can be either the on-premises default username (usually `userPrincipalName`) or another attribute configured in Azure AD Connect (known as `Alternate ID`).
4. The on-premises domain controller then evaluates the request and returns a response (success or failure) to the agent.
5. The agent, in turn, returns this response back to Azure AD.
6. Azure AD then evaluates the response and responds to the user as appropriate. For example, it issues a token back to the application or asks for Multi-Factor Authentication.

The following diagram illustrates the various steps. All requests and responses are made over the HTTPS channel.

![Pass-through authentication](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### Password writeback

In cases where you have configured [password writeback](../active-directory-passwords-update-your-own-password.md) on your tenant and for a specific user, if the user signs in by using pass-through authentication, they will be able to change or reset their passwords as before. The passwords are written back to on-premises Active Directory as expected.

However, if password writeback is not configured on your tenant or the user doesn't have a valid Azure AD license assigned to them, then the user will not be allowed to update their password in the cloud. This is true even if their password has expired. The user instead sees this message: "Your organization doesn't allow you to update your password on this site. Please update it according to the method recommended by your organization, or ask your admin if you need help."

## Next steps
- See how to enable the [Azure AD seamless SSO](active-directory-aadconnect-sso.md) feature on your tenant.
- Read our [troubleshooting guide](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) to learn how to resolve common issues with Azure AD pass-through authentication.

## Feedback
Your feedback is important to us. Use the comments section if you have questions. Use our [UserVoice forum](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) for new feature requests.

