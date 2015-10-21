    <properties 
	pageTitle="Create a DevTest Lab | Microsoft Azure" 
	description="Create a new DevTest Lab lab for virtual machines" 
	services="devtest-lab,virtual-machines" 
	documentationCenter="na" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="devtest-lab" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="10/07/2015" 
	ms.author="patshea"/>

# Create an Azure DevTest Lab

## Prerequisites

To create a DevTest Lab, you will need: 

- An Azure subscription. To learn about Azure purchase options, see [How to buy Azure](http://azure.microsoft.com/pricing/purchase-options/) or [Free one-month trial](https://azure.microsoft.com/pricing/free-trial/). You must be the owner of the subscription to create the lab.
- An Azure Resource Group for the lab. See [Azure Resource Manager Overview](resource-group-overview.md) and [Managing and Auditing Access to Resources](./azure-portal/resource-group-rbac.md).


## Create a lab

1. On the Azure preview portal [Startboard](http://portal.azure.com), choose the **Marketplace** tile.

    ![Marketplace tile](./media/devtest-lab-create-lab/start-marketplace-tile.png)   

2. On the **Marketplace** blade, select **Everything** and enter **devtest lab** in the search box.

    ![Search the Marketplace for DevTest Lab](./media/devtest-lab-create-lab/search-marketplace-everything-for-devtestlab.png)

3. Choose the **DevTest Lab** item to display the **DevTest Lab** blade.

    ![Choose DevTest Lab](./media/devtest-lab-create-lab/choose-devtestlab-after-search.png)

4. On the **DevTest Lab** blade, choose **Create**.

    ![Choose Create](./media/devtest-lab-create-lab/devtestlab-blade-create-button.png)

5. On the **Create a DevTest Lab** blade:

    1. Enter a **Lab Name** for the new lab.
    1. Select the **Subscription** to associate with the lab.
    1. Choose the **Resource Group** to contain the lab.
    1. Choose a **Location** to store the lab.
    1. Click **Create**.

    ![Create a DevTest Lab blade](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

## What happens

Azure creates the lab and opens the blade for the lab.


## Next steps

Let users that are not members of your subscription securely access the the lab. See [Secure access to a DevTest Lab](devtest-lab-secure-user-access.md).

