---
title: App Service on Azure Stack Technical Preview 2 Configure Deployment Sources | Microsoft Docs
description: How to configure deployment sources (Git, GitHub, BitBucket, DropBox and OneDrive) for App Service on Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''

ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: anwestg

---

# App Servuce on Azure Stack Technical Preview 2 Configure Deployment Sources

App Service on Azure Stack supports on-demand deployment from multiple Source Control Providers in Technical Preview Two.  The Source Control Providers supported, in addition to local Git, are:

* GitHub
* BitBucket
* OneDrive
* DropBox

In order for tenants to be able to configure App Service to connect to their repositories, Administrators must configure the integration between App Service on Azure Stack and the Source Control Provider.

## View Deployment Sources in App Service Resource Provider Administration

1. Login to the Azure Stack portal as the service administrator.
2. Browse to **Resource Providers** and select the **App Service Resource Provider Admin**.
3. Click **Source control configuration**.   Here you see the list of all Deployment Sources configured.

## Configure GitHub as a Deployment Sources

> [!NOTE]
> You will require a GitHub account to complete this task.
1. Login to GitHub, browse to https://www.gihub.com/settings/developers and click **Register a new application**
2. Enter an **Application name** for example - App Service on Azure Stack
3. Enter the **Homepage URL** for example - https://portal.azurestack.local
4. Enter an **Application Description**
5. Enter the **Authorization callback URL**.  In a default Azure Stack deployment this is in the form https://portal.azurestack.local/tokenauthorize, if you are running under a different domain please substitute your domain for azurestack.local
6. Click **Register application**.  You will now be presented with a page listing the **Client ID** and **Client Secret** for the application.
7. In a new browser tab or window Login to the Azure Stack Portal as the service administrator.
8. Browse to **Resource Providers** and select the ** App Service Resource Provider Admin**.
9. Click **Source control configuration**
10. Copy and paste the **Client Id** and **Client Secret** into the corresponding input boxes for GitHub.
11. Click **Save**
12. Click **Roles**
13. Click **Management Server**
14. Click **Repair All** and select **Yes**.  This schedules a repair on all Management Servers to complete the integration.  The repair operations are managed to minimise downtime.

## Configure BitBucket as a Deployment Source

> [!NOTE]
> You will require a BitBucket account to complete this task.
1. Login to BitBucket and browse to **Integrations** under your account
2. Click **OAuth** under Access Management and **Add consumer**
3. Enter a **Name** for the consumer, for example App Service on Azure Stack
4. Enter a **Description** for the application
5. Enter the **Callback URL**.  In a default Azure Stack deployment this is in the form https://portal.azurestack.local/tokenauthorize, if you are running under a different domain please substitute your domain for azurestack.local
6. Enter the **URL** - this should be the Azure Stack Portal URL, for example https://portal.azurestack.local
7. Select the **Permissions** required
    .
8. Click **Save**.  You will now see this new application, along with the **Key** and **Secret** under **OAuth consumers**.
9. In a new browser tab or window Login to the Azure Stack Portal as the service administrator.
10. Browse to **Resource Providers** and select the ** App Service Resource Provider Admin**.
11. Click **Source control configuration**
12. Copy and paste the **Key** into the **Client ID** input Box, and **Secret** into the **Client Secret** into the input box for BitBucket.
13. Click **Save**
14. Click **Roles**
15. Click **Management Server**
16. Click **Repair All** and select **Yes**.  This schedules a repair on all Management Servers to complete the integration.  The repair operations are managed to minimise downtime.

## Configure OneDrive as a Deployment Source

> [!NOTED]
> OneDrive for Business Accounts are not currently supported.  You will need to have a Microsoft Account linked to a OneDrive account to complete this task.
1. Browse to https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm and login using your Microsoft Account.
2. Click **Add an app** under **Converged applications**
3. Enter a **Name** for the New Application Registration, for example App Service on Azure Stack, and click **Create Application**
4. The next screen lists the properties of your new application. Record the **Application Id**
5. Under **Application Secrets** click **Generate New Password** and record the **New password generated** - this is your application secret.
6. Under **Platforms** click **Add Platform** and select **Web**
7. Enter the **Redirect URI**.  In a default Azure Stack deployment this is in the form https://portal.azurestack.local/tokenauthorize, if you are running under a different domain please substitute your domain for azurestack.local
8. Set the **Application Permissions**
9. Click **Save**
10. In a new browser tab or window Login to the Azure Stack Portal as the service administrator.
11. Browse to **Resource Providers** and select the **App Service Resource Provider Admin**.
12. Click **Source control configuration**
13. Copy and paste the **Application Id** into the **Client ID** input Box, and **Password** into the **Client Secret** into the input box for OneDrive.
14. Click **Save**
15. Click **Roles**
16. Click **Management Server**
17. Click **Repair All** and select **Yes**.  This schedules a repair on all Management Servers to complete the integration.  The repair operations are managed to minimise downtime.

## Configure DropBox as a Deployment Source

> [!NOTE]
> You will need to have a DropBox account to complete this task.
1. Browse to https://www.dropbox.com/developers/apps and login using your DropBox Account
2. Click **Create app** 
3. Select **DropBox API**
4. Set the access level to **App Folder**
5. Enter a **Name** for your application.
6. Click **Create App**.  You will now be presented with a page listing the settings for the App including **App key** and **App secret**.
7. Set the **OAuth 2 Redirect URI** and click **Add**.  In a default Azure Stack deployment this is in the form https://portal.azurestack.local/tokenauthorize, if you are running under a different domain please substitute your domain for azurestack.local
8. In a new browser tab or window Login to the Azure Stack Portal as the service administrator
9. Browse to **Resource Providers** and select the **App Service Resource Provider Admin**.
10. Click **Source control configuration**
11. Copy and paste the **Application Key** into the **Client ID** input box, and **App secret** into the **Client Secret** input boxes for DropBox
12. Click **Save**
13. Click **Roles**
14. Click **Management Server**
15. Click **Repair All** and select **Yes**.  This schedules a repair on all Management Servers to complete the integration.  The repair operations are managed to minimise downtime.