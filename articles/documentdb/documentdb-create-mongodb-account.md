---
title: Create a DocumentDB account with protocol support for MongoDB | Microsoft Docs
description: Learn how to create a DocumentDB account with protocol support for MongoDB, now available for preview.
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''

ms.assetid: 413f3ae1-a68e-43be-b0d7-fa2987644f3e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: anhoh

---
# How to create a DocumentDB account with protocol support for MongoDB using the Azure portal
To create an Azure DocumentDB account with protocol support for MongoDB, you must:

* Have an Azure account. You can get a [free Azure account](https://azure.microsoft.com/free/) if you don't have one already.

## Create the account
To create a DocumentDB account with protocol support for MongoDB, perform the following steps.

1. In a new window, sign in to the [Azure Portal](https://portal.azure.com).
2. In the left navigation, click on **NoSQL (DocumentDB)**.

    ![Screen shot of the Portal left navigation, highlighting DocumentDB NoSQL entry](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. Alternatively, click **More services >**, type **DocumentDB** in the top search bar, and click **NoSQL (DocumentDB)**.

    ![Screen shot of the More services blade, searching for DocumentDB NoSQL entry](./media/documentdb-create-mongodb-account/more-services-search.png)

4. On the top of the **NoSQL (DocumentDB) Blade**, click **+ Add** on the top action bar.

    ![Screen shot of the Add button on the DocumentDB NoSQL resource blade](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. In the **DocumentDB account** blade, specify the desired configuration for the account.

   ![Screen shot of the New DocumentDB with protocol support for MongoDB blade](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)

    - In the **ID** box, enter a name to identify the account.  When the **ID** is validated, a green check mark appears in the **ID** box. The **ID** value becomes the host name within the URI. The **ID** may contain only lowercase letters, numbers, and the '-' character, and must be between 3 and 50 characters. Note that *documents.azure.com* is appended to the endpoint name you choose, the result of which will become your account endpoint.

    - For **NoSQL API**, select **MongoDB**. This specifies the communication API you would like to use to interact with your DocumentDB database.

    - For **Subscription**, select the Azure subscription that you want to use for the account. If your account has only one subscription, that account is selected by default.

    - In **Resource Group**, select or create a resource group for the account.  By default, an existing Resource group under the Azure subscription will be chosen.  You may, however, choose to select to create a new resource group to which you would like to add the account. For more information, see [Using the Azure portal to manage your Azure resources](../azure-portal/resource-group-portal.md).

    - Use **Location** to specify the geographic location in which to host the account.

6. Once the new account options are configured, click **Create**.  It can take a few minutes to create the account.

   You can monitor your progress from the Notifications hub.  

   ![Screen shot of the Notifications hub, showing that the DocumentDB account is being created](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. To access your new account, click **DocumentDB (NoSQL)** on the left-hand menu. In your list of regular DocumentDB and DocumentDB with Mongo protocol support accounts, click on your new account's name.
8. It is now ready for use with the default settings.

   ![Screen shot of the default account blade](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## Next steps
* Learn how to [connect](documentdb-connect-mongodb-account.md) to a DocumentDB account with protocol support for MongoDB.
