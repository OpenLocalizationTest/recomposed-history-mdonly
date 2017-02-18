---
title: Transition to Azure AD Application Proxy from Microsoft Forefront | Microsoft Docs
description: Covers the basics about Azure AD Application Proxy connectors.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila

ms.assetid:
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: kgremban

---
#Transition to Azure AD Application Proxy from Microsoft Forefront

This article describes how to move from the Microsoft Forefront Threat Management Gateway (TMG) and United Access Gateway (UAG) solutions to these Azuare Active Directory (Azure AD) application proxies: Web Application Proxy and Azure AD Application Proxy.

> [!NOTE]
> Azure AD Application Proxy is a feature that is available only if you upgraded to the Premium or Basic edition of Azure AD. For more information, see [Azure Active Directory editions](active-directory-editions.md).


For detailed information about the transition from Forefront UAG and TMG to the new application proxies, see this [white paper](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx).

## TMG/UAG conversion to Azure AD Application Proxy table

|**TMG/UAG functionality**|**Web Application Proxy/Azure AD Application Proxy**|
|:-----|:-----|
|Selective HTTP publishing for browser apps|Available in Web Application Proxy in Windows Server 2012 R2 (Available in Azure AD Application Proxy today)|
|Active Directory Federation Services (AD FS) integration|Available in Web Application Proxy in Windows Server 2012 R2 (Available in Azure AD Application Proxy today)|
|Rich Protocols Publishing (for example, Citrix, Lync, RDG)|Available in Web Application Proxy in Windows Server 2012 R2 (Available in Azure AD Application Proxy today)|
|Preauthentication for ActiveSync (HTTP Basic) and RDG|Will be available in Web Application Proxy in Windows Server vNext (Will be coming to Azure AD Application Proxy)|
|Portal|Use Intune / System Center for Web Application Proxy (Use Azure AD Access Panel or Office 365 App Launcher available for Azure AD Application Proxy)|
|Endpoint Health Detection|Use Intune / System Center|
|SSL tunneling|Use Windows SSL / VPN capability|
|Layer 2/3 firewall|Use Windows Server capabilities|
|Web application firewall|No current solution from Microsoft|
|Secure web gateway (forward proxy)|No current solution from Microsoft|


## Next steps

[Blog: Web Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[Blog: Azure AD Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)
