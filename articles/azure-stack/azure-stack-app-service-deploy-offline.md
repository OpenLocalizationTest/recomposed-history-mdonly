---
title: 'Deploy App Service in an offline environment: Azure Stack | Microsoft Docs'
description: Detailed guidance on how to deploy App Service in a disconnected Azure Stack environment secured by ADFS.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: 

ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg

---
# Add an App Service resource provider to a disconnected Azure Stack environment secured by AD FS

You must add an [Azure App Service resource provider](azure-stack-app-service-overview.md) to your Azure Stack deployment:

* If you're running Azure Stack in an isolated environment secured by Active Directory Federation Services (AD FS). 
* You want to give your tenants the capability to create web, mobile, and API applications and Azure Functions with their Azure Stack subscription. 

To do so, follow these steps.

## Download the required components

1. Download the [App Service on Azure Stack preview installer](http://aka.ms/appsvconmasrc1installer).

2. Download the [App Service on Azure Stack deployment helper scripts](http://aka.ms/appsvconmasrc1helper).

3. Extract the files from the helper scripts zip file. The following files and folder structure appear:

   - Create-AppServiceCerts.ps1
   - Create-IdentityApp.ps1
   - Modules
      - AzureStack.Identity.psm1
      - GraphAPI.psm1

## Create an offline installation package

To deploy App Service in an isolated environment, you must create an offline installation package from a machine that connects to the Internet.

1. Run the App Service on Azure Stack preview installer (AppService.exe) from a machine that's connected to the Internet.

2. Click the **Advanced** tab, and click **Create offline installation package**.

    ![App Service on Azure Stack Create offline installation package][1]

3. The App Service installer creates an offline installation package, displays the path, and gives an option to open the folder.

   ![App Service on Azure Stack Offline installation package][2]

4. Copy the App Service on Azure Stack preview installer (AppService.exe) and the offline installation package to the Azure Stack host machine.

## Create certificates required by App Service on Azure Stack

This first script works with the Azure Stack certificate authority to create three certificates that are needed by App Service. Run the script on the Azure Stack host and ensure that you're running PowerShell as azurestack\administrator.

1. In a PowerShell session running as azurestack\administrator, execute the **Create-AppServiceCerts.ps1** script from the location where you extracted the helper scripts.  The script creates three certificates in the same folder as the create certificates script that are needed by App Service.

2. Enter a password to secure the .pfx files, and make a note of it. You need to enter it in the App Service on Azure Stack installer.

### Create-AppServiceCerts.ps1 parameters

| Parameter | Required/optional | Default value | Description |
| --- | --- | --- | --- |
| pfxPassword | Required | Null | Password used to protect the certificate private key |
| DomainName | Required | local.azurestack.external | Azure Stack region and domain suffix |
| CertificateAuthority | Required | AzS-CA01.azurestack.local | Certificate authority endpoint |

## Complete the offline installation of App Service on Azure Stack

> [!NOTE]
> You *must* use an elevated account (local or domain administrator) to execute the installer. If you sign in as azurestack\azurestackuser, you're prompted for elevated credentials.

1. Run appservice.exe as azurestack\administrator.

2. Click the **Advanced** tab, and click **Complete offline installation**.

    ![App Service on Azure Stack Complete offline installation][3]

3. Specify the location of the offline installation package you previously created, and click **Next**.

    ![App Service on Azure Stack location of offline installation package][4]

4. Review and accept the Microsoft Software Prerelease License Terms, and click **Next**.

5. Review and accept the third-party license terms, and click **Next**.

6. Review the App Service Cloud Service configuration information, and click **Next**.

    ![App Service on Azure Stack cloud configuration][5]

> [!NOTE]
> The App Service on Azure Stack installer provides the default values for a one-node Azure Stack installation. If you customized options when you deployed Azure Stack, for example, the domain suffix, you need to edit the values in this window accordingly. For example, if you use the domain suffix mycloud.com, your admin Azure Resource Manager endpoint needs to change to adminmanagement.[region].mycloud.com.

7. Click the **Connect** button next to the **Azure Stack Subscriptions** box. Enter your admin account, for example, azurestackadmin@azurestack.local. Enter your password, and click **Sign In**.

8. Select your subscription in the **Azure Stack Subscriptions** box.

9. In the **Azure Stack Locations** box, select the location corresponding to the region you're deploying. For example, select **local**. Click **Next**.

    ![App Service on Azure Stack subscription selection][6]

10. Enter the **Resource Group Name** for your App Service deployment. By default it's set to **APPSERVICE-LOCAL**.

11. Enter the **Storage Account Name** you want App Service to create as part of the installation. By default it's set to **appsvclocalstor**.

12. Enter the SQL Server details for the instance that's used to host the App Service resource provider databases. Click **Next**, and the installer validates the SQL connection properties.

13. Click the **Browse** button next to the **App Service default SSL certificate file** box. Go to the **_.appservice.local.AzureStack.external** certificate [created earlier](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps). If you specified a different location and domain suffix when you created the certificate, select the corresponding certificate.

14. Enter the certificate password that you set when you created the certificate.

15. Click the **Browse** button next to the **Resource provider SSL certificate file** box. Go to the **api.appservice.local.AzureStack.external** certificate [created earlier](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps). If you specified a different location and domain suffix when you created the certificate, select the corresponding certificate.

16. Enter the certificate password that you set when you created the certificate.

17. Click the **Browse** button next to the **Resource provider root certificate file** box. Go to the **AzureStackCertificationAuthority** certificate [created earlier](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).

18. Click **Next**. The installer verifies the certificate password provided.

    ![App Service on Azure Stack certificate details][8]

19. Review the App Service role configuration. The defaults are populated with the minimum recommended instance SKUs for each role. A summary of core and memory requirements is provided to help plan your deployment. After you make your selections, click **Next**.

  - **Controller**: By default, one Standard A1 instance is selected. This is the minimum we recommend. The Controller role is responsible for managing and maintaining the health of the App Service cloud.
  - **Management**: By default, one Standard A2 instance is selected. To provide failover, we recommend two instances. The Management role is responsible for the App Service Azure Resource Manager and API endpoints, portal extensions (admin, tenant, Functions portal), and the data service.
  - **Publisher**: By default, one Standard A1 instance is selected. This is the minimum we recommend. The Publisher role is responsible for publishing content via FTP and web deployment.
  - **FrontEnd**: By default, one Standard A1 instance is selected. This is the minimum we recommend. The FrontEnd role is responsible for routing requests to App Service applications.
  - **Shared Worker**: By default, one Standard A1 instance is selected, but you might want to add more. As an administrator, you can define your offering and choose any SKU tier. The tiers must have a minimum of one core. The Shared Worker role is responsible for hosting web, mobile, or API applications and Azure Functions apps.

    ![App Service on Azure Stack role configuration][9]

    > [!NOTE]
    > In the technical previews, the App Service resource provider installer also deploys a Standard A1 instance to operate as a simple file server to support the Azure Resource Manager. This remains for a single-node point of contact. For production workloads, at general availability the App Service installer enables the use of a high-availability file server.

20. Choose your deployment **Windows Server 2016** VM image from those available in the compute resource provider for the App Service cloud. Click **Next**. 

    ![App Service on Azure Stack VM image selection][10]

21. Enter a user name and password for the Worker roles configured in the App Service cloud. Enter a user name and password for all other App Service roles. Click **Next**.

    ![App Service on Azure Stack credential entry][11]

22. On the summary screen, verify the selections you made. To make changes, go back through the screens and modify your selections. If the configuration is how you want it, select the checkbox. To start the deployment, click **Next**. 

    ![App Service on Azure Stack selection summary][12]

23. Track the installation progress. App Service on Azure Stack takes about 45 to 60 minutes to deploy based on the default selections.

    ![App Service on Azure Stack installation progress][13]

24. After the installer successfully completes, click **Exit**.

## Configure an AD FS service principal for virtual machine scale set integration on Worker tiers and SSO for the Azure Functions portal and advanced developer tools

>[!NOTE]
> These steps apply to AD FS secured Azure Stack environments only.

Administrators need to configure SSO to:

* Configure a service principal for virtual machine scale set integration on Worker tiers.
* Enable the advanced developer tools within App Service (Kudu).
* Enable the use of the Azure Functions portal experience. 

Follow these steps:

1. Open a PowerShell instance as azurestack\azurestackadmin.

2. Go to the location of the scripts downloaded and extracted in the [prerequisite step](#Download-Required-Components).

3. [Install and configure an Azure Stack PowerShell environment](azure-stack-powershell-configure.md). Follow the instructions to create an AzureStackAdmin environment, and sign in to the AzureStackAdmin environment.

4. In the same PowerShell session, run the **CreateIdentityApp.ps1** script. When prompted for your Azure Active Directory (Azure AD) tenant ID, enter **ADFS**.

5. In the **Credential** window, enter your AD FS service admin account and password. Click **OK**.

6. Enter the certificate file path and certificate password for the [certificate created earlier](# Create certificates to be used by App Service on Azure Stack). The certificate created for this step by default is sso.appservice.local.azurestack.external.pfx.

7. The script creates a new application in the tenant Azure AD and generates a new PowerShell script.

8. Copy the identity app certificate file and the generated script to the **CN0-VM** by using a remote desktop session.

9. Return to **CN0-VM**.

10. Open an administrator PowerShell window, and browse to the directory where the script file and certificate were copied in step 7.

11. Run the script file. This script file enters the properties in the App Service on Azure Stack configuration and initiates a repair operation on all FrontEnd and Management roles.

| Parameter | Required/optional | Default value | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | Mandatory | Null | Use ADFS for the AD FS environment |
| TenantAzure Resource ManagerEndpoint | Mandatory | management.local.azurestack.external | The tenant Azure Resource Manager endpoint |
| AzureStackCredential | Mandatory | Null | The AD FS service admin account |
| CertificateFilePath | Mandatory | Null | Path to the identity application certificate file generated earlier |
| CertificatePassword | Mandatory | Null | Password used to protect the certificate private key |
| DomainName | Required | local.azurestack.external | Azure Stack region and domain suffix |
| AdfsMachineName | Optional | AD FS machine name, for example, AzS-ADFS01.azurestack.local |


## Validate the App Service on Azure Stack installation

1. In the Azure Stack admin portal, browse to the resource group created by the installer. By default, this group is **APPSERVICE-LOCAL**.

2. Locate the **CN0-VM**. To connect to the VM, click **Connect** on the **Virtual Machine** blade.

3. On the desktop of this VM, double-click **Web Cloud Management Console**.

4. Go to **Managed Servers**.

5. When all the machines except one or more Workers display **Ready**, proceed to step 6.

6. Close the remote desktop machine, and return to the machine where you executed the App Service installer.

    > [!NOTE]
    > You don't need to wait for one or more Workers to display **Ready** to complete the installation of App Service on Azure Stack. However, you need a minimum of one Worker that's ready to deploy a web, mobile, or API app or Azure Functions.
    
    ![App Service on Azure Stack Managed Servers status][14]

## Test drive App Service on Azure Stack

After you deploy and register the App Service resource provider, test it to make sure that tenants can deploy web, mobile, and API apps.

> [!NOTE]
> You need to create an offer that has the Microsoft.Web namespace within the plan. Then you need to have a tenant subscription that subscribes to this offer. For more information, see  [Create offer](azure-stack-create-offer.md) and [Create plan](azure-stack-create-plan.md).
>
>You *must* have a tenant subscription to create applications that use App Service on Azure Stack. The only capabilities that a service admin can complete within the admin portal are related to the resource provider administration of App Service. These capabilities include adding capacity, configuring deployment sources, and adding worker tiers and SKUs.
>
> As of the third technical preview, to create web, mobile, and API apps you must use the tenant portal and have a tenant subscription.  

1. In the Azure Stack tenant portal, click **New** > **Web + Mobile** > **Web App**.

2. On the **Web App** blade, type a name in the **Web app** box.

3. Under **Resource Group**, click **New**. Then type a name in the **Resource Group** box.

4. Click **App Service plan/Location** > **Create New**.

5. On the **App Service plan** blade, type a name in the **App Service plan** box.

6. Click **Pricing tier** > **Free-Shared** or **Shared-Shared** > **Select** > **OK** > **Create**.

7. In under a minute, a tile for the new web app appears on the dashboard. Click the tile.

8. On the **Web App** blade, click **Browse** to view the default website for this app.

## Deploy a WordPress, DNN, or Django website (optional)

1. In the Azure Stack tenant portal, click **+**. Go to the Azure Marketplace, deploy a Django website, and wait for successful completion. The Django web platform uses a file system-based database. It doesn’t require any additional resource providers such as SQL or MySQL.

2. If you also deployed a MySQL resource provider, you can deploy a WordPress website from the Marketplace. When you're prompted for database parameters, enter the user name as *User1@Server1*, with the user name and server name of your choice.

3. If you also deployed a SQL Server resource provider, you can deploy a DNN website from the Marketplace. When you're prompted for database parameters, pick a database in the computer running SQL Server that's connected to your resource provider.

## Next steps

You can also try out other [platform as a service (PaaS) services](azure-stack-tools-paas-services.md).

- [SQL Server resource provider](azure-stack-sql-resource-provider-deploy.md)
- [MySQL resource provider](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step1.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step2.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step3.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step4.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-cloud-configuration.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-subscription-location-populated.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-resource-group-SQL.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-certificates.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-role-configuration.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-vm-image-selection.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-role-credentials.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-selection-summary.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-installation-progress.png
[14]: ./media/azure-stack-app-service-deploy-offline/managed-servers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
