---
title: 'Azure AD Domain Services: Create or select a virtual network | Microsoft Docs'
description: Getting started with Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand

ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu

---
# Create or select a virtual network for Azure AD Domain Services
## Before you begin
Refer to [Networking considerations for Azure AD Domain Services](active-directory-ds-networking.md).
>
>

## Task 2: Create an Azure virtual network
The next configuration task is to create an Azure virtual network and a subnet within it. You enable Azure Active Directory Domain Services (Azure AD DS) in this subnet within your virtual network. If you have an existing virtual network that you’d prefer to use, you can skip this step.

> [!NOTE]
> Make sure that the Azure virtual network you create or choose to use with Azure AD DS belongs to an Azure region that's supported by Azure AD DS. To ascertain the Azure regions in which Azure AD DS is available, see [Azure services by region](https://azure.microsoft.com/regions/#services/).
>
>Note the name of the virtual network to ensure that you select the right virtual network when you enable Azure AD DS in a subsequent configuration step.


To create an Azure virtual network in which you want to enable Azure AD DS, follow these configuration instructions:

1. Go to the [Azure classic portal](https://manage.windowsazure.com).
2. In the left pane, select **Networks**.

    ![Networks node](./media/active-directory-domain-services-getting-started/networks-node.png)  
    The **Virtual Networks** window opens.
3. In the task pane at the bottom of the window, click **New**.

    ![Virtual Networks window](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. Click **Network Services**, and then select **Virtual Network**.
    
    ![Virtual network - quick create](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
5. To create a virtual network, click **Quick Create**.
    
6. Specify a **Name** for your virtual network, and consider doing the following: 
    * You may choose to configure the **Address space** or **Maximum VM count** for this network. 
    * You can leave the **DNS server** setting as **None** for now. You can update the setting after you enable Azure AD DS.
7. In the **Location** drop-down list, select a supported Azure region.  
    To ascertain the Azure regions in which Azure AD DS is available, see [Azure services by region](https://azure.microsoft.com/regions/#services/).
8. To create your virtual network, click **Create a Virtual Network**.

    ![Create a virtual network for Azure AD Domain Services](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. After you've created a virtual network, select the name of the virtual network, and then click the **Configure** tab.

    ![Create a subnet](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. Under **virtual network address spaces**, click **add subnet**, and then specify a subnet with the name **AaddsSubnet**. 

    ![Create a subnet for Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

11. To create the subnet, click **Save**.

## Next steps
Task 3: [Enable Azure AD Domain Services](active-directory-ds-getting-started-enableaadds.md)
