<properties
	pageTitle="Deploy templates with Visual Studio in Azure Stack | Microsoft Azure"
	description="Learn how to deploy templates with Visual Studio in Azure Stack."
	services="azure-stack"
	documentationCenter=""
	authors="erikje"
	manager="v-kiwhit"
	editor=""/>

<tags
	ms.service="azure-stack"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="erikje"/>

# Deploy templates with Visual Studio in Azure Stack

1.  Open Visual Studio 2015 Update 1.

2.  Click **File**, click **New**, and in the **New Project** dialog box click **Azure Resource Group**.

3.  Enter a **Name** for the new project, and then click **OK**.

4.  In the **Select Azure Template** dialog box, click **Windows Virtual Machine**, and then click **OK**.

    >[AZURE.NOTE] You can see a list of templates available in your new project by expanding the **Templates** node in the **Solution Explorer** pane.

5.  In the **Solution Explorer** pane, right-click the name of your project, click **Deploy**, then click **New Deployment**.

6.  In the **Deploy to Resource Group** dialog box, in the **Subscription** drop-down, select your Microsoft Azure Stack subscription.

7.  In the **Resource Group** drop-down, select or create a resource group into which you want the template to deploy.

8.  In the **Resource group location** drop down, select a location.

9.  Click **Deploy**. In the **Edit Parameters** dialog box, enter values for the parameters (which vary by template), and then click **Save**. Your resource is then created.

## Next Steps

[Deploy templates with the command line](azure-stack-deploy-template-command-line.md)
