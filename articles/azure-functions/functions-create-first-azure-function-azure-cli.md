---
title: Create your first function from the Azure CLI | Microsoft Docs 
description: Learn how to create your first Azure Function for serverless execution using the Azure CLI.
services: functions 
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 04/24/2017
ms.topic: hero-article
ms.service: functions
# ms.custom: can-be-multiple-comma-separated
ms.devlang: azure-cli
manager: erikre
---

# Create your first function using the Azure CLI

This quickstart tutorial walks through how to use Azure Functions to create your first function. You use the Azure CLI to create a function app, which is the serverless infrastructure that hosts your function. The function code itself is deployed from a GitHub sample repository.    

You can follow the steps below using a Mac, Windows, or Linux computer. It should take you only about five minutes to complete all the steps in this topic.

## Before you begin

Before running this sample, you must have an active GitHub account and an Azure subscription.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Also, make sure that the Azure CLI has been installed. For more information, see [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli)

## Log in to Azure

Sign in to your Azure subscription using the [az login](/cli/azure/#login) command and follow the on-screen instructions. 

```azurecli
az login
```

## Create a resource group

Create a resource group with the [az group create](/cli/azure/group#create). An Azure resource group is a logical container into which Azure resources like function apps, databases, and storage accounts are deployed and managed.

The following example creates a resource group named `myResourceGroup`:

```azurecli
az group create --name myResourceGroup --location westeurope
```
## Create an Azure Storage account

Functions uses an Azure Storage account to maintain state and other information about your functions. Create a storage account in the resource group you created by using the [az storage account create](/cli/azure/storage/account#create) command.

In the following command, substitute your own unique storage account name where you see the `<storage_name>` placeholder:

```azurecli
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

After the storage account has been created, the Azure CLI shows information similar to the following example:

```json
{
  "accessTier": null,
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "customDomain": null,
  "encryption": null,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myfunctionappstorage",
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
  "primaryLocation": "westeurope",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

## Create a function app

The function app provides an environment for serverless execution of your function code. Create a function app by using the **az functionapp create** command. 

In the following command, substitute your own unique function app name where you see the `<app_name>` placeholder and the storage account name for  `<storage_name>`. The `<app_name>` is used as the default DNS domain for the function app, and so the name needs to be unique across all apps in Azure. 

```azurecli
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup --consumption-plan-location westeurope
```
By default, a function app is created with the Consumption hosting plan, which means that you only pay when the function is running. After the function app has been created, the Azure CLI shows information similar to the following example:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "quickstart.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    },
    {
      "hostType": "Repository",
      "name": "quickstart.scm.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
  ],
  "hostNames": [
    "quickstart.azurewebsites.net"
  ],
  "hostNamesDisabled": false,
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/quickstart",
  "isDefaultContainer": null,
  "kind": "functionapp",
  "lastModifiedTimeUtc": "2017-04-15T17:21:30.460000",
  "location": "westeurope",
  "maxNumberOfWorkers": null,
  "microService": "false",
  "name": "quickstart",
  "outboundIpAddresses": "104.40.129.119,104.40.129.252,104.40.130.52,104.40.130.72",
  "premiumAppDeployed": null,
  "repositorySiteName": "quickstart",
  "reserved": false,
  "resourceGroup": "myResourceGroup",
  "scmSiteAlsoStopped": false,
  "serverFarmId": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/WestEuropePlan",
  "siteConfig": null,
  "slotSwapStatus": null,
  "state": "Running",
  "suspendedTill": null,
  "tags": null,
  "targetSwapSlot": null,
  "trafficManagerHostNames": null,
  "type": "Microsoft.Web/sites",
  "usageState": "Normal"
}
```

Now that you have a function app, you can deploy the actual function code from the GitHub sample repository.

## Set the deployment source 

There are several ways to create your function code in your new function app. This topic connects to a sample repository in GitHub. As before, in the following code replace the `<app_name>` placeholder with the name of the function app you created. 

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup --repo-url https://github.com/Azure-Samples/functions-quickstart --branch master --manual-integration
```
After the deployment source been set, the Azure CLI shows information similar to the following example:

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/quickstart/sourcecontrols/web",
  "isManualIntegration": true,
  "isMercurial": false,
  "kind": null,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## Test the function

Browse to the deployed function using your web browser, replacing the `<app_name>` placeholder with the name of your function app. Append the query string `&name=<yourname>` to the URL and execute the request. 

```bash
http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```   
![Function response shown in a browser.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)

> [!NOTE]
> The functions in the sample repository are set to allow anonymous access. The _authLevel_ setting of the HTTP trigger binding in the function.json project file controls access. For more information, see the [HTTP trigger reference](functions-bindings-http-webhook.md#httptrigger).     

## Clean up resources

Other quick starts in this collection build upon this quick start. If you plan to continue on to work with subsequent quick starts or with the tutorials, do not clean up the resources created in this quick start. If you do not plan to continue, use the following command to delete all resources created by this quick start:

```azurecli
az group delete --name myResourceGroup
```

## Next steps

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
