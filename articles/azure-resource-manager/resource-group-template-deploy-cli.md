---
title: Deploy resources with Azure CLI and template | Microsoft Docs
description: Use Azure Resource Manager and Azure CLI to deploy a resources to Azure. The resources are defined in a Resource Manager template.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2017
ms.author: tomfitz

---
# Deploy resources with Resource Manager templates and Azure CLI

This topic explains how to use Azure CLI 2.0 with Resource Manager templates to deploy your resources to Azure. The Resource Manager template you deploy can either be a local file on your machine, or an external file that is located in a repository like GitHub. If you are not familiar with the concepts of deploying and managing your Azure solutions, see [Azure Resource Manager overview](resource-group-overview.md).

The examples in this article deploy resources to a resource group in your default subscription. To use a different subscription, see [Manage multiple Azure subscriptions](/cli/azure/manage-azure-subscriptions-azure-cli). You can get the template (storage.json) used in these examples from the [Create your first Azure Resource Manager template](resource-manager-create-first-template.md#final-template) article. To use the template with these examples, create a JSON file and add the copied content.

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

<a id="deploy-local-template" />

## Deploy a template from your local machine

When deploying resources to Azure, you:

1. Log in to your Azure account
2. Create a resource group that serves as the container for the deployed resources
3. Deploy the template that defines the resources to create

A template can include parameters that enable you to customize the deployment. Through parameters, you provide values that are tailored for a particular environment (such as dev, test, and production). If your template provides parameters, you pass in values for those parameters during deployment. To understand how to define parameters in your template, see [Understand the structure and syntax of Azure Resource Manager templates](resource-group-authoring-templates.md).

The following example prompts you to log in to Azure, creates a resource group, and deploys a template from your local machine. It includes template parameters to specify the type storage account to create, and set a name prefix.  

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

The deployment can take a few minutes to complete. When it finishes, you see a message that includes the result:

```azurecli
"provisioningState": "Succeeded",
```

## Deploy a template from an external source

Instead of storing Resource Manager templates on your local machine, you may prefer to store them in an external location. You can store templates in a source control repository (such as GitHub). Or, you can store them in an Azure Storage account for shared access in your organization. You can deploy directly from any external source that is accessible by URI. 

To deploy an external template, use the **template-uri** parameter.
   
```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

The preceding example requires a publicly accessible URI for the template. This approach works for most scenarios because your template should not include sensitive data. If you need to specify sensitive data (like an admin password), you pass that value as a secure parameter. The publicly accessible template includes a parameter for that password but the password itself is not in the template. However, if you decide that you do not want your template to be publicly accessible, you can protect it by storing it in a private storage container. During deployment, you generate a shared access signature (SAS) token for the blob, and include that SAS token in the URI for the template. For information about deploying a template that requires a SAS token, see [Deploy private template with SAS token](resource-manager-cli-sas-token.md).

## Parameter files

The preceding examples show how to pass parameters as inline values during template deployment.  You may find it easier to create a JSON file that contains the parameters to use during deployment. The parameter file must be in the following format:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

Notice that the schema element for the parameter file points to a file named `deploymentParameters.json`. This schema file defines the elements that are available within a parameter file. Also, notice that the parameters section includes two parameters with names that match the parameters defined in your template (storageNamePrefix and storageSKU). The parameter file contains values for both parameters. These values are automatically passed to the template during deployment. You can create multiple parameter files for different deployment scenarios, and then pass in the appropriate parameter file. 

Copy the preceding example and save it as a file named `storage.parameters.json`.

To pass a local parameter file, use `@` to specify a local file named storage.parameters.json.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## Test a template deployment

To test your template and parameter values without actually deploying any resources, use [az group deployment validate](/cli/azure/group/deployment#validate). 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

If no errors are detected with the template or the parameters, the command returns information about the test deployment. In particular, notice that the **error** value is null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

If an error is detected, the command returns an error message. For example, attempting to pass an incorrect value for the storage account SKU, returns the following error:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageSKU' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

If your template has a syntax error, the command returns an error indicating it could not parse the template. The message indicates the line number and position of the parsing error.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

To use complete mode, use the `mode` parameter:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```


## Next steps
* For a complete sample script that deploys a template, see [Resource Manager template deployment script](resource-manager-samples-cli-deploy.md).
* To define parameters in template, see [Authoring templates](resource-group-authoring-templates.md#parameters).
* For tips on resolving common deployment errors, see [Troubleshoot common Azure deployment errors with Azure Resource Manager](resource-manager-common-deployment-errors.md).
* For information about deploying a template that requires a SAS token, see [Deploy private template with SAS token](resource-manager-cli-sas-token.md).
* For guidance on how enterprises can use Resource Manager to effectively manage subscriptions, see [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md).
