<properties
   pageTitle=""
   description=""
   services="virtual-machines"
   documentationCenter=""
   authors="rgardler"
   manager="nepeters"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Containers, Micro-services, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="home-page"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="rogardle"/>
   
# Creating a Service using the Azure Portal
 
To access Azure Container Service, you will need an Azure subscription. If you don't have one then sign up for a [free trial](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AA4C1C935).
 
Select one of the following templates to deploy a Mesos or Docker Swarm cluster:
 
* Mesos: https://github.com/rgardler/azure-quickstart-templates/tree/acs/acs-mesos-full-template
* Swarm: https://github.com/rgardler/azure-quickstart-templates/tree/acs/acs-swarm-full-template
 
This will take you to the portal and present a form that looks something like this:
 
 ![Create deployment](media/create-mesos-params.png)
 
To complete the form:

Field           | Description
----------------|-----------
ADMINUSERNAME   | This is the username for your user account on each of the Virtual Machines that will be created
ADMINPASSWORD   | This is the password used for the user account on each Virtual Machine created. It is recommended that you use SSH keys for production environments. See below.
DNSNAMEPREFIX   | This must be a world unique value. It will be used to create DNS names for each of the key parts of the service. More information below.
AGENTCOUNT      | This is the number of Virtual Machines to create in your cluster that will host containers
MASTERCOUNT     | This is the number of Virtual Machines to configure as masters for your cluster. You can select 1, but this will not provide any resilience in your cluster and is only recommended for testing. The recommended number for a production cluster would be 3 or 5. 
SSHRSAPUBLICKEY	| It is required that you use SSH for authentication against the Virtual Machines. This is where you add your public key. If you need to create an SSH key you can find guidance for [windows](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-use-ssh-key/) and [Linux](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-use-ssh-key/) on the Azure documentation site.
  
Once you have confirgure your parameters, click OK. The parameters blade will disappear.
 
Next we need to provide a resource group name, select a region, and review and agree to the legal terms. 
 
![Select resource group](media/resourcegroup.png) 
 
Finally, click "Create". You will return to your dashboard and, assuming you did not uncheck "Pin to dashboard" on the deployment blade, you will see an animated tile that looks something like this:

![deploying](media/deploy.png) 
 
Now sit back and relax while the cluster is created. Once complete, you will see some blades that show the resources just created:
 
![Finished](media/final.png) 

# Creating a Service using the Azure CLI

To access Azure Container Service using the command line, you will need an Azure subscription. If you don't have one then you can sign up for a free trial. You will also need to have installed and configured the Azure CLI.
 
Select one of the following templates to deploy a Mesos or Docker Swarm cluster:
 
* Mesos: https://github.com/rgardler/azure-quickstart-templates/tree/acs/acs-mesos-full-template
* Swarm: https://github.com/rgardler/azure-quickstart-templates/tree/acs/acs-swarm-full-template
 
## Using the Azure CLI (Cross Platform)

First make sure that the Azure CLI has been connected to an Azure subscription. This can be done using the following command.
```bash
Azure account show
```
If an Azure account is not returned, use the following to log the CLI into Azure.
 
```bash
azure login -u user@domain.com
```

Next, make sure to configur the Azure CLI tools to use Azure Resource manager.
 
```bash
azure config mode arm
```
 
If you want to create your cluster in a new Resource Group, you must first create the Resource Group. Use this command, where `GROUP_NAME` is the name of the resource group you want to create, and `REGION` is the region where you want to create the Resource Group.
 
```bash
azure group create GROUP_NAME REGION
```
 
Once you have a Resource Group you can create your cluster this this command, where:

- **RESOURCE_GROUP** is the name of the Resource Group you want to use for this service.
- **DEPLOYMENT_NAME** is the name of this deployment.
- **TEMPLATE_URI** is the location of the deployment file. **Note** - this must be the RAW file, not a pointer to the GitHub UI. To find this URL select the azuredeploy.json file in GitHub and click the RAW button:
 
```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```
 
### Providing a Parameters
 
This version of the command requires the user to define parameters interactively. If you want to provide parameters as a json formatted string you can do so with the `-p` switch. For example:
 
 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

Alternativley you can provide a json formatted parameters file using the `-e` switch:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON'
 ```
 
There is an example parameters file (called 'azuredeploy.parameters.json') in GitHub alongside each template.
 
# Creating a Service using PowerShell

These instructions are based on the 1.0 version of the [Azure PowerShell cmdlets](https://azure.microsoft.com/en-gb/blog/azps-1-0/). 

Before creating a cluster in your Azure subscription, verify that your PowerShell session has been logged into Azure. This can be completed with the `Get-AzureRMSubscription` command.

```powershell
Get-AzureRmSubscription

Get-AzureRmSubscription : Run Login-AzureRmAccount to login.
At line:1 char:1
+ Get-AzureRmSubscription
+ ~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (:) [Get-AzureRmSubscription], PSInvalidOperationException
    + FullyQualifiedErrorId : InvalidOperation,Microsoft.Azure.Commands.Profile.GetAzureRMSubscriptionCommand
```

To log into Azure, use the `Login-AzureRMAccount command.

```powershell
Login-AzureRmAccount

Environment           : AzureCloud
Account               : azureuser@contoso.com
TenantId              : 00000000-0000-0000-0000-000000000000
SubscriptionId        : 00000000-0000-0000-0000-000000000000
CurrentStorageAccount :
```
 
Before creating a cluster in a new Resource Group, you must first create the Resource Group. Use this command, Where `GROUP_NAME` is the name of the resource group you want to create, and `REGION` is the region where you want to create the Resource Group.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Once you have a Resource Group, you can create your cluster with the following command.
 
```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```
 
###Dynamic Template Parameters
 
If you are familiar with PowerShell, you know that you can cycle through the available parameters for a cmdlet by typing a minus sign (-) and then pressing the TAB key. This same functionality also works with parameters that you define in your template. As soon as you type the template name, the cmdlet fetches the template, parses it, and adds the template parameters to the command dynamically. This makes it very easy to specify the template parameter values. And, if you forget a required parameter value, PowerShell prompts you for the value.
 
Below is the full command with parameters included. You can provide your own values for the names of the resources.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -param1 value1 -param2 value2 …..
```
 
## Next Steps
 
Now that you have a cluster up and running you can take one of two recommended paths:
 
- [Connect with the ACS cluster](./container-service-connect.md)
- [Working with ACS and Mesos](./container-service-mesos-marathon-rest.md)
- [Working with ACS and Docker Swarm](./container-service-docker-swarm.md)

 