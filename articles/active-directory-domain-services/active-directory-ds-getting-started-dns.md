---
title: 'Azure AD Domain Services: Update DNS settings for the Azure virtual network | Microsoft Docs'
description: Getting started with Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand

ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu

---
# Azure AD Domain Services - Update DNS settings for the Azure virtual network
## Task 4: Update DNS settings for the Azure virtual network
In preceding configuration tasks, you have successfully enabled Azure AD Domain Services for your directory. The next task is to ensure that computers within the virtual network can connect and consume these services. Update the DNS server settings for your virtual network to point to the two IP addresses at which Azure AD Domain Services is available on the virtual network.

> [!NOTE]
> Note down the IP addresses for Azure AD Domain Services displayed on the **Configure** tab of your directory, after you have enabled Azure AD Domain Services for the directory.
>
>

Perform the following configuration steps to update the DNS server setting for the virtual network in which you have enabled Azure AD Domain Services.

1. Navigate to the **Azure classic portal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Select the **Networks** node on the left pane.

    ![Virtual networks node](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. In the **Virtual Networks** tab, select the virtual network in which you enabled Azure AD Domain Services to view its properties.
4. Click the **Configure** tab.

    ![Virtual networks node](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. In the **DNS servers** section, enter the IP addresses of Azure AD Domain Services.
6. Ensure that you enter both the IP addresses that were displayed in the **Domain Services** section on the **Configure** tab of your directory.
7. To save the DNS server settings for this virtual network, click **Save** on the task pane at the bottom of the page.

   ![Update the DNS server settings for the virtual network.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> Virtual machines in the network will only get the new DNS settings on a restart. If you need them to get the updated DNS settings right away, trigger a restart either by the portal, PowerShell, or the CLI.
>
>

## Task 5 - Enable password synchronization to Azure AD Domain Services
The next configuration task is to [enable password synchronization to Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).
