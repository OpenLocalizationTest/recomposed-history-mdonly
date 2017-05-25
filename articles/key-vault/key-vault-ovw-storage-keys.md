---
ms.assetid: 
title: Azure Key Vault Storage Account Keys
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/24/2017
---
# Azure Key Vault Storage Account Keys

Before Azure Key Vault Storage Account Keys, developers had to manage thier own Azure Storage Account (ASA) keys and rotate them manually or through an external automation. Now, Azure Key Vault Storage Account Keys are implemented as [Key Vault secrets](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) and are for authenticating with an Azure storage account. 

The Key Vault ASA key feature adds value through managing secret rotation for you. It also removes the need for your direct contact with an Azure Storage Account key by offering shared access signatures (SAS) as a method. 

Also, Key Vault ASA keys are useful beyond Azure Storage Account. You can perform a wide range of operations with a Key Vault ASA key. *(BRP - Such as?)*

For more general information on Azure Storage accounts, see [About Azure storage accounts](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## Further security through access limits
SAS tokens, constructed using Key Vault storage account keys, provide even more controlled access to an Azure storage account. For more information, see [Using shared access signatures](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## What Key Vault manages

Key Vault performs several internal management functions on your behalf when you use Storage Account Keys.

1. Azure Key Vault manages keys of an Azure Storage Account (SAS). 
    - Internally, Azure Key Vault can list (sync) keys with an Azure Storage Account.  
    - Azure Key Vault regenerates (rotates) the keys periodically. 
    - Key values are never returned in response to caller. 
    - Azure Key Vault manages keys of both Storage Accounts and Classic Storage Accounts. 
2. Azure Key Vault allows you, the vault/object owner, to create SAS (account or service SAS) definitions. 
    - The SAS value, created using SAS definition, is returned as a secret via /secrets route.

    *BRP - Is "/secrets route" referring to a general CS method or is this a REST term. Would pathway be a better general term?*

## Supporting interfaces

The Azure Storage Account keys feature is initially available through the follwing interfaces.

- REST 
- .NET / C# 
- PowerShell

## Developer experience 

### Before Azure Key Vault Storage Keys 
Developers used the following practices with a storage account key to get access to Azure storage. 
 
 ```
//create storage account using connection string containing account name 
// and the storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### After Azure Key Vault Storage Keys 

```
//Get sastoken from Key Vault //.... 
 
// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(sasToken); 

// Use credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If Sas token is about to expire then Get sastoken again from Key Vault 
//.... 
 
// and update the accountSasCredential accountSasCredential.UpdateSASToken(sasToken); 
 ```
## Using Key Vault storage account keys

### Naming

Storage account names must be between 3 and 24 characters in length and may contain numbers and lowercase letters only.  
 
A SAS definition name must be 1-102 characters in length containing only 0-9, a-z, A-Z. 

### Developer best practices 

1. Allow only Key Vault to manage your ASA keys. Do not attempt to manage them yourself as your manual management will interfere with Key Vault's processes. 
2. Do not allow ASA keys to be managed by more than one key vault object. 
3. If you need to manually regenerate ASA keys, we recommend you regenerate them via Key Vault. 

### Storage account onboarding 

A key vault object owner adds a storage account object on AzKV to onboard a storage account. During onboarding Key Vault needs to verify that identity onboarding the account has access to list and regenerate the storage keys. Key Vault will get OBO token from EvoSTS with audience as ARM and make a list key call to Storage RP. If list fails, then the Key Vault object creation will fail with Forbidden http status code. The keys listed in this fashion will be cached with key vault entity storage. 

Additionally, it is also required that Key Vault verifies that identity has regenerate permissions before key vault take ownership of regenerating keys. To verify that identity (via OBO token) and as well as key vault first party identity has permission to rotate and list keys, key vault will list RBAC permissions on the storage account resource and validate the response via regular expression matching of actions and not actions. Some resources on how to achieve this as follows: 

- Example 
[VipSwapper](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceMan agerUtil.cs) 
- Example [hasPermission](https://msazure.visualstudio.com/One/_search?type=Code&lp=searchproject&text=hasPermissions&result=DefaultCollection%2FOne%2FAzureUXPortalFx%2FGBdev%2F%2Fsrc%2FSDK%2FFramework.Client%2FTypeScript%2FFxHubs%2FPermissions.ts &filters=ProjectFilters%7BOne%7DRepositoryFilters%7BAzureUX-PortalFx%7D&_a=search) 

If the identity (via OBO token) does not have regenerate permissions or if Key Vault first party identity doesn’t have list or regenerate permission then the onboaring request fails as a bad request with appropriate the error code and message. 

Note that the OBO token will only work when you use first-party, native client applications of PowerShell and CLI.  

### Role based access control permissions

Key Vault needs permissions to list and regenerate keys for a storage account. Follow these steps to set this up. 

1. Get ObjectId of KV through this command: 
`Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`  
 
2. Assign “Storage Key Operator” role to Azure Key Vault Identity: 
`New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'` 

For a classic account set the role parameter to 'Classic Storage Account Key Operator Service Role'. 

Note: The Key Vault’s identity might be invisible in tenants which had vaults created prior to <date>*(BRP - what's the date?)*. As a workaround, you can create a new vault and delete it to make Key Vault’s identity visible in these tenants.


