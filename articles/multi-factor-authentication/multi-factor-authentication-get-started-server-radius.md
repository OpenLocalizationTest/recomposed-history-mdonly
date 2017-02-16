---
title: RADIUS Authentication and Azure MFA Server | Microsoft Docs
description: This is the Azure Multi-factor authentication page that will assist in deploying RADIUS Authentication and Azure Multi-Factor Authentication Server.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: yossib

ms.assetid: f4ba0fb2-2be9-477e-9bea-04c7340c8bce
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/15/2017
ms.author: kgremban

---
# RADIUS Authentication and Azure Multi-Factor Authentication Server
Use he RADIUS Authentication section of Azure MFA Server to enable and configure RADIUS authentication. RADIUS is a standard protocol to accept authentication requests and to process those requests. The Azure Multi-Factor Authentication Server acts as a RADIUS server. Insert it between your RADIUS client (VPN appliance) and your authentication target, which could be Active Directory (AD), an LDAP directory, or another RADIUS server to add Azure Multi-Factor Authentication. For Azure Multi-Factor Authentication (MFA) to function, you must configure the Azure MFA Server so that it can communicate with both the client servers and the authentication target. The Azure MFA Server accepts requests from a RADIUS client, validates credentials against the authentication target, adds Azure Multi-Factor Authentication, and sends a response back to the RADIUS client. The authentication request only succeeds if both the primary authentication and the Azure Multi-Factor Authentication succeed.

> [!NOTE]
> The MFA Server only supports PAP (password authentication protocol) and MSCHAPv2 (Microsoft's Challenge-Handshake Authentication Protocol) RADIUS protocols when acting as a RADIUS server.  Other protocols such as EAP (extensible authentication protocol) can be used when the MFA server acts as a RADIUS proxy to another RADIUS server which supports that protocol such as Microsoft NPS.
> 
> When using other protocols in this configuration, one-way SMS and OATH tokens will not work since the MFA Server is not able to initiate a successful RADIUS Challenge response using that protocol.

![Radius Authentication](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## Add a RADIUS client
To configure RADIUS authentication, install the Azure Multi-Factor Authentication Server on a Windows server. If you have an Active Directory environment, the server should be joined to the domain inside the network. Use the following procedure to configure the Azure Multi-Factor Authentication Server:

1. Within the Azure Multi-Factor Authentication Server click the RADIUS Authentication icon in the left menu.
2. Check the **Enable RADIUS authentication** checkbox.
3. On the Clients tab change the Authentication port(s) and Accounting port(s) if the Azure Multi-Factor Authentication RADIUS service should bind to non-standard ports to listen for RADIUS requests from the clients that will be configured.
4. Click **Add**.
5. In the Add RADIUS Client dialog box, enter the IP address of the appliance/server that will authenticate to the Azure Multi-Factor Authentication Server, an application name (optional), and a shared secret. 

  The application name appears in Azure Multi-Factor Authentication reports and may be displayed within SMS or Mobile App authentication messages.

  The shared secret needs to be the same on both the Azure Multi-Factor Authentication Server and appliance/server. 

6. Check the **Require Multi-Factor Authentication user match** box if all users have been or will be imported into the Server and subject to multi-factor authentication. If a significant number of users have not yet been imported into the Server and/or will be exempt from two-step verification, leave the box unchecked. 
7. Check the **Enable fallback OATH token** box if users will use mobile apps for verification, and you want to use OATH passcodes as a fallback to the out-of-band phone call, SMS, or push notification.
8. Click **OK**.

Repeat steps 4 through 8 to add as many additional RADIUS clients as you need.

## Configure your RADIUS client

1. Click the **Target** tab.
2. If the Azure MFA Server is installed on a domain-joined server in an Active Directory environment, select Windows domain.
3. If users should be authenticated against an LDAP directory, select **LDAP bind**. 

  To use LDAP bind, click the Directory Integration icon and edit the LDAP configuration on the Settings tab so that the Server can bind to your directory. Instructions for configuring LDAP can be found in the [LDAP Proxy configuration guide](multi-factor-authentication-get-started-server-ldap.md).

4. If users should be authenticated against another RADIUS server, select RADIUS server(s).
5. Click **Add** to configure the server to which the Azure MFA Server will proxy the RADIUS requests.
6. In the Add RADIUS Server dialog box enter the IP address of the RADIUS server and a shared secret. 

  The shared secret needs to be the same on both the Azure Multi-Factor Authentication Server and RADIUS server. Change the Authentication port and Accounting port if different ports are used by the RADIUS server.

7. Click **OK**.
8. You must add the Azure Multi-Factor Authentication Server as a RADIUS client in the other RADIUS server so that it will process access requests sent to it from the Azure Multi-Factor Authentication Server. Use the same shared secret configured in the Azure Multi-Factor Authentication Server.

Repeat these steps to add more RADIUS servers and configure the order in which the Azure MFA Server should call them with the **Move Up** and **Move Down** buttons. 

This completes the Azure Multi-Factor Authentication Server configuration. The Server is now listening on the configured ports for RADIUS access requests from the configured clients.   

## Best practices for RADIUS Client configuration
To configure the RADIUS client, use the guidelines:

* Configure your appliance/server to authenticate via RADIUS to the Azure Multi-Factor Authentication Server’s IP address, which will act as the RADIUS server.
* Use the same shared secret that was configured above.
* Configure the RADIUS timeout to 30-60 seconds so that there is time to validate the user’s credentials, perform the multi-factor authentication, receive their response and then respond to the RADIUS access request.

