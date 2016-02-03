<properties
	pageTitle="Azure Stack App Service Technical Preview 1 Deployment | Microsoft Azure"
	description="Detailed guidance for deploying Azure Web Apps in Azure Stack"
	services="azure-stack"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="azure-stack"
	ms.workload="app-service"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="chriscompy"/>

# Azure Stack App Service Technical Preview 1 Deployment #

## Overview ##
Azure Stack App Service is the Azure App Service brought to on premise installation.  In this first preview release only the Web Apps aspect of the Azure App Service is being made available.   The current Azure Stack Web App deployment will create an instance of each of the 5 required role types, and a file server.  While you can add more instances for each of the role types please remember that there is not a lot of space for VMs in Technical Preview 1.  The current capabilities for Azure Stack App Service are primarily foundation capabilities that are needed to manage the system and to host web apps.  

There is no support for the App Service preview releases.  Do not put production workloads on this preview release.  There is also no upgrade between Azure Stack App Service preview releases.  The primary purpose being served by these preview releases is to show what we are providing and to obtain feedback.  

The Azure Stack Web Apps resource provider utilizes the same code as used in Azure Web Apps and as a result there are some common concepts that are worth describing.  In Azure Web Apps the pricing container for your web apps is called the App Service Plan.  It represents the set of dedicated virtual machines used to hold your apps.  Within a given subscription you can have multiple App Service Plans.  The same is true in Azure Stack Web Apps as well.  

In Azure there is a Shared worker that supports high density multi-tenant web app hosting and 3 reserved sizes of dedicated workers, Small, Medium, and Large.  The needs of on premise customers though cannot be always so described so in Azure Stack Web Apps the resource provider administrator can define the worker tiers they wish to make available and then define their own pricing SKUs that use those worker tiers.   

## Portal Features ##

As with the back-end the UI used in Azure Stack is the same that is used in Azure.  There are a number of things that are disabled as they are not yet functional in Azure Stack due to Azure specific expectations or services that are not yet available in Azure Stack that those features require.
There are two portals for the Azure Stack App Service, the Resource Provider administration portal and the End user tenant portal.

### Resource Provider administration ###

- Manage roles
- View system properties
- Manage credentials and certs
- Define worker tiers
- Create SKUs with their own quotas and features
- Configure SSL
- Integrate with DNS

### End user ###

- Create empty web app and web app with SQL
- Create Wordpress, Django, Orchard and DNN web apps
- Create multiple App Service Plans like in Azure
- View web app properties

### Installation prerequisites ###

To install Azure Stack Web apps there are a few items that you will need.  Those items are:

- A completed deployment of Azure Stack technical preview 1
- Enough space in your Azure Stack system to deploy a small deployment of Azure Stack Web Apps.  The space required is roughly 20 Gb of Ram
- A SQL Server database
- The DNS name for your Azure Stack deployment
- A storage account created in the "Default Provider Subscription" as the Service Admin (guidance for creating a storage account can be found here: (needs link to real URL for: https://github.com/ErikjeMS/azure-content-pr/blob/master/articles/azure-stack/azure-stack-webapps-deploy.md)
- The key to the storage account

### Steps to install SQL server ###

1 Log in to the POC host:

- check the following path and ensure
\\\\sofs\Share\CRP\GuestArtifactRepository and make sure Microsoft.Powershell.DSC_2.13.2.0.zip exists in the path
- Make sure a vhd image in the path \\\\sofs\Share\CRP\PlatformImages\ meets the requirements for this deployment (.NET 3.5 is preinstalled)

2 Login to the Client VM

- Provision a new VM and install SQL server
- Download and expand the [WebAppsDeployment.zip][WebAppsDeployment] to the client machine
- Run “Deploy-SqlServerDSC.ps1” script to provision a new VM and install SQL server:
**NOTE** The resource group used in the script to provision the SQL vm should be the same resource group used during the WebApps deployment in the next step. The script default for the Resource Group is: WebsitesSQL

Once the deployment completes, navigate to the Resource Group in the Azure Stack portal, select the Sq0-NIC resource, and take note of the Private IP address (it will be something like: 10.0.2.4). This IP address will be used later in this deployment process.

**NOTE** The password for the SA account is the same password used for the VM.


### Azure Web Apps Installation steps ###

The installation experience for Azure Stack Web Apps starts with the download of the appservice.exe installer from [Azure Stack App Service preview installer][Azure_Stack_App_Service_preview_installer]

During this process, the installer will perform the following actions:

1.	Prompt the user to approve of the third party licenses
2.	Collect Azure Stack deployment information
3.	Create a blob container in the Azure Stack storage account specified
4.	Download the files needed to install the Azure Stack WebApp resource provider
5.	Prepare the install to deploy the WebApp resource provider in the Azure Stack environment
6.	Upload the files to the created blob container within Azure Stack storage account specified
7.	Present the information needed to kick off the ARM template

The last item will seem to offer the ability to directly bring up the UI for the ARM template but that capability is not yet operational.  The UI screens for the installer appear as shown:

**NOTE** The installer must be executed with an elevated account (local or domain Administrator). If logged in as azurestack\azuerstackuser, you will be prompted for elevated credentials.

![][1]

![][2]

![][3]

![][4]

In this step, provide the storage account and storage account access key created for this WebApp deployment. The storage account name and key can be copied from the Azure Stack portal, from the storage account resource > Settings > Access keys. The Azure Stack DNS suffix will be the domain for the Azure Stack, in this case: **azurestack.local**

![][5]

![][6]

![][7]


Once all the steps in the installer have completed, click ***Deploy to Azure Stack***. You will be prompted with a dialog box. Click "No". By clicking "No", the following text is copied to your clipboard:

    Azure Stack App Service ARM Template
    Template location:  http://mytp1webapp.blob.azurestack.local/appservice-template/AzureStackAppServiceTemplate.json
    Invoke from Portal: https://portal.azurestack.local/#create/Microsoft.Template/uri/http%3A%2F%2Fmytp1webapp.blob.core.windows.net%2Fappservice-template%2FAzureStackAppServiceTemplate.json

This is the information needed to get and kick off the WebApps ARM template.

Open Notepad and paste the contents of your clipboard immediately.

**NOTE** If this information is lost for some reason, you can still get everything you need by accessing the storage account blob container directly.

### Web App ARM deployment ###

The Azure Stack Web App ARM template will collect information defining the web app resource provider deployment.  There are a few things that need to be noted:

- the storage account name entered will creat a new account
- the environment DNS suffix is the subdomain that is used for web apps created in this environment (example: webapps.azurestack.local)
- the SQL server name is the Private IP address gathered after the SQL Server template deployment (found on Sq0-NIC resource blade, as noted above)
- the SA password is the same as the local SQL admin password used during the deployment of the SQL Server template
- the “number of workers” item will only create Shared workers
- there is not a lot of space for additional VMs in the TP1 POC environment so it is best to just go with 1 instance of each role type
- the resource group used for deploying web apps must be the same as the one used to deploy the SQL server (as noted above, the default Resource Group for the SQL Server template deployment is: WebsitesSQL)

After everything is filled in and the "Create" button is clicked, the VMs will be created for your Azure Stack Web App resource provider and the software will be installed.

This AzureStackAppServiceTemplate.json template can also be deployed via PowerShell, an example deployment is as follows (:

New-AzureRmResourceGroupDeployment -Name "WebAppsDeploy01" -ResourceGroupName "WebsitesSQL" -TemplateFile C:\templates\AzureStackAppServiceTemplate.json `
    -storageAccountNameParameter "webappsstorage" -adminUsername "admin" -adminPassword "myPassword1!" -environmentDnsSuffix webapps.azurestack.local `
    -sqlservername 10.0.2.4 -sqlsysadmin sa -sqlsysadminpwd "myPassword1!" 

![][12]  


### Pre-registration Azure Stack Web Apps configuration steps ###
After deployment is completed there are a few manual steps required before registering the newly deployed web app resource provider.

**Creating DNS Entries**

DNS entries need to be made for the Front End and Management Server VIPs.  To do this you need to first obtain the IP addresses for each of those load balancers.  To do this you will need to go into the Azure Stack portal.  Go to Resource Groups and then into the Resource Group you used when creating your Web Apps resource provider.  From there select Resources.  Both the FrontEndServersLoadBalancer and the ManagementServersLoadBalancer will be listed there.  Click on each and copy down the IP address that is shown for them.  

![][10]

![][11]

Login to the DNS of the POC environment.  This can be done POC host.  If you don't know your DNS server address, use ***ipconfig /all*** to get it.  Once there open up DNS Manager to add entries for the Front End and Management VIPs. Click on AzureStack.local on the left side and then on the right side of the screen click on the right mouse button.  Select ***New Host (A or AAA)...***  

![][13]

Using this you can then create your DNS names.  The DNS name created for the Front End load balancer should match what you used when populating the ARM template for the Web App resource provider.  In this case we had ***webapps.azurestack.local*** so the A record created for it would look something like this:

![][9]

The Management Server DNS name that you create will be used when registering the Web Apps resource provider later.  This is the endpoint which Azure Stack will communicate with.  Don't lose the Management Server IP Address as it is needed a little later when importing the wildcard certificate.

![][8]

**Setting up the wildcard certificates**

To configure your Azure Stack Web Apps deployment with wildcard certificates you need to first get the wildcard certificate to configure the system with.  To do this:

1 Login to the PortalVM

2 Request wildcard certificate from POC CA

- On PortalVM in inetmgr Feature: Server Certificates Action: Create Domain  Certificate
- Fill out the Prompt wizard
- Common name: *.azurestack.local
- Organization: Microsoft        
- Organizational unit: AzureStack
- City/locality: Redmond
- State/province: WA
- Country/region: US

3 Export wildcard certificate

- Open MMC Certificates and find *.azureStack.local.
- Right-click, all tasks, export
- Select Yes, export private key
- Check Export all extended properties.
- Specify suitable password.
- Export to file _.azurestack.local.pfx

4 Import certificates to provider VM

- Copy _.azurestack.local.pfx to the Management VM. To do this RDP to the WebAps Management VM IP Address that was used when configuring the DNS name for the management load balancer.
- Double-click to import to Local Machine.
- Specify same password used to export.
- Check Mark this key as exportable.
- Automatically select the certificate store based on the type of certificate.

Locate the private IP address for the Web Apps controller VM and RDP to that address.  To get the IP address, Browse > All resources > CN0-NIC

![][14]

Once in the Controller VM the following actions will need to be performed

- Configure the certificate for the Management Server role
- Configure the ARM endpoint.  This is needed by the Web App portal extensions
- Configure the Web App extension endpoint names
- Configure the Web App portal extension endpoint names for the manifest
- Repair the Management Server to propagate the changes
- Repair the Front-ends and Worker machines

To perform those actions, execute the following powershell commands

    Import-Module Websites
    Set-WebSitesConfig -Type Global -ManagementServerCertificateFileName "C:\ _.azurestack.local.pfx " -ManagementServerCertificatePassword $password
    Set-WebSitesConfig -Type Global -ArmEndpoint "https://api.azurestack.local"
    Set-WebsitesConfig -Type Global -ArmResourceProviderUri 'https://<load-balanced-endpoint-name>/'
Where:  *<load-balanced-endpoint-name>* is the DNS entry that maps to the VIP of the WebApps management VM
example:   *Set-WebsitesConfig -Type Global -ArmResourceProviderUri 'https://management.azurestack.local/'*
**NOTE:** This is only needed when using a load balancer for several MN (management) servers.use the load-balanced-endpoint-name for the management DNS name

    Get-WebSitesServer -ServerType ManagementServer | Repair-WebSitesServer
    Get-WebSitesServer -ServerType LoadBalancer | Repair-WebSitesServer
    Get-WebSitesServer -ServerType WebWorker | Repair-WebSitesServer

Browse to the portal https://portal.azurestack.local and register the WebApps  manifest endpoint  (https://management.azurestack.local)


<!--Image references-->
[1]: ./media/azure-stack-webapps-deploy/appsvcinstall-1.png
[2]: ./media/azure-stack-webapps-deploy/appsvcinstall-2.png
[3]: ./media/azure-stack-webapps-deploy/appsvcinstall-3.png
[4]: ./media/azure-stack-webapps-deploy/appsvcinstall-4.png
[5]: ./media/azure-stack-webapps-deploy/appsvcinstall-5.png
[6]: ./media/azure-stack-webapps-deploy/appsvcinstall-6.png
[7]: ./media/azure-stack-webapps-deploy/appsvcinstall-7.png
[8]: ./media/azure-stack-webapps-deploy/appsvcinstall-8.png
[9]: ./media/azure-stack-webapps-deploy/appsvcinstall-9.png
[10]: ./media/azure-stack-webapps-deploy/appsvcinstall-10.png
[11]: ./media/azure-stack-webapps-deploy/appsvcinstall-11.png
[12]: ./media/azure-stack-webapps-deploy/appsvcinstall-12.png
[13]: ./media/azure-stack-webapps-deploy/appsvcinstall-13.png
[14]: ./media/azure-stack-webapps-deploy/appsvcinstall-14.png

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]:  http://go.microsoft.com/fwlink/?LinkId=723982
