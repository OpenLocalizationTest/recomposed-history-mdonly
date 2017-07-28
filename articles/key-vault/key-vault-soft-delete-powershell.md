---

ms.assetid: 
title: Azure Key Vault - How to use soft delete with PowerShell
description: Use case examples of soft-delete with PSh code snips
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 07/28/2017

---
# How to use Key Vault soft-delete using PowerShell

Azure Key Vault's soft delete feature allows recovery of deleted vaults and vault objects. Specifically, soft-delete addresses the following scenarios:

- Support for recoverable deletion of a key vault
- Support for recoverable deletion of key vault objects; keys, secrets, and, certificates

## Prerequisites

- Azure PowerShell 4.0.0 or later

For Key Vault specific refernece information for PowerShell, see [Azure Key Vault PowerShell reference](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

## Required permissions

Key Vault operations are seperately managed via role-based access control (RBAC) permissions as follows:

- Recover: restores a deleted keyvault. User needs to have ‘Microsoft.KeyVault/vaults/write’ permission. 
- Purge: removes a deleted key vault so that the vault and all its contents are permanently removed. User needs ‘Microsoft.KeyVault/locations/deletedVaults/purge/action’ permission. 
- List: to list deleted key vaults. User needs ‘Microsoft.KeyVault/deletedVaults/read’ permission.


## Enabling soft-delete

To be able to recover a deleted key vault or objects stored in a key vault, you must first enable soft-delete for that key vault.

### Existing vault

For an existing key vault named 'ContosoVault', enable soft-delete as follows.

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### New vault

Enabling soft-delete for a new key vault is done at creation time by adding the soft-delete enable flag to your create command.

```powershell
New-AzureRmKeyVault -VaultName "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### Verify soft-delete enablement

To verify that a key vault has soft-delete enabled, run the appropriate *get* command and look for the 'Soft Delete Enabled?' attribute and its setting, true or false.

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```


## Deleting a vault protected by soft-delete

The command to delete (or remove) a vault remains the same, but its behavior changes depending on whether you have enabled soft-delete or not.

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

> [!IMPORTANT]
>If you run the previous command for a key vault that does not have soft-delete enabled, you will permanently delete this key vault and all its content without any options for recovery.

### How soft-delete protects your key vault

With soft-delete enabled:

- When a key vault is deleted, it is removed from its resource group and placed in a reserved namespace that is only associated with the location where it was created. 
- Objects in a deleted key vault, such as keys and secrets, become inaccessible when its in this state. 
- The DNS name for a key vault in a deleted state is still reserved so, a new vault with same name cannot be created.  

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedStateVault 

Name           : ContosoVault
Location             : westus
Id                   : /subscriptions/xxx/providers/Microsoft.KeyVault/locations/westus/deletedVaults/ContosoVault
Resource ID          : /subscriptions/xxx/resourceGroups/ContosoVault/providers/Microsoft.KeyVault/vaults/ContosoVault
Deletion Date        : 5/9/2017 12:14:14 AM
Scheduled Purge Date : 8/7/2017 12:14:14 AM
Tags                 :
```

The *Resource ID* in the output refers to the original resource ID of this vault. Since this key vault is now in a deleted state, no resource exists with that resource ID. The *Id* field comes can be used to identify the resource when recovering, or purging. The *Scheduled Purge Date* field indicates when the vault will be permanently deleted (purged) if no action is taken for this deleted vault.

## Recovering a vault

To recover a key vault, you need to specify the vault name, resource group, and location. Note the location and the resource group of the deleted vault. You'll need it when recovering.

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

When a vault is recovered, the result is a new resource with the vault's original resource ID. If the resource group where the key vault existed has been removed, a new resource group with same name will need to be recreated before the vault can be recovered. 

## Vault objects and soft-delete

Now that we have seen the complete life cycle of a vault with soft-delete enabled, let's turn our attention to keys and secrets in a vault with soft-delete enabled. I am assuming here that you already know how to create keys and secrets in a key vault. If not, check out [Get started with Azure Key Vault](key-vault-get-started.md).

Let's say you have a key 'ContosoFirstKey' in your vault 'ContosoVault' with soft-delete enabled. Here's how you would delete that key.

`Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey`

With your key vault enabled for soft-delete, a deleted key still appears like it's deleted for the most part, except, when you explicitly list/retrieve deleted keys. Most operations on a key in deleted state will fail except for specifically listing deleted key, recovering it or purging it. For example, if you request to list keys in a key vault with 'Get-AzureKeyVaultKey -VaultName ContosoVault', the deleted key will not show up. To see deleted keys in a vault, you must use the '-InRemovedState' parameter.

### Transition state 

When you delete a key in a vault with soft-delete enabled it may take a few seconds for the transition to complete. During this transition state, it may appear that the key is not in the active state or the deleted state. For example, listing the key, with or without the *-InRemovedState* parameter, will return an empty list.

This command will list all deleted keys in 'ContosoVault'.

```
  Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
  Vault Name           : ContosoVault
  Name                 : ContosoFirstKey
  Id                   : https://ContosoVault.vault.azure.net:443/keys/ContosoFirstKey
  Deleted Date         : 2/14/2017 8:20:52 PM
  Scheduled Purge Date : 5/15/2017 8:20:52 PM
  Enabled              : True
  Expires              :
  Not Before           :
  Created              : 2/14/2017 8:16:07 PM
  Updated              : 2/14/2017 8:16:07 PM
  Tags                 :
```

## Using soft delete with keys and secrets

Just like vaults, a deleted key or secret will remain in deleted state for up to 90 days unless you recover it or purge it. *Scheduled Purge Date* field indicates when a key will be permanently deleted, if no action is taken.

### Keys

To recover a deleted key:

`Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey`

To permanently delete a key:

`Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState`

The **recover** and **purge** actions have their own permissions associated in a key vault access policy. For a user or service principal to be able to execute a **recover** or **purge** action they must have the respective permission for that object (key or secret) in the key vault access policy. By default, the **purge** permission is not added to a vault's access policy when the 'all' shortcut is used to grant all permissions to a user. You must explicitly grant **purge** permission. For example, the following command grants user@contoso.com permission to perform several operations on keys in *ContosoVault* including **purge**.

`Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge`

Note that, if you have an existing vault that has just had soft-delete enabled, you may not have **recover** and **purge** permissions.

### Secrets

Similarly here are related commands for deleting, listing, recovering, and purging secrets respectively.

- Delete a secret named SQLPassword: `Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword`

- List all deleted secrets in a key vault: `Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState`

- Recover a secret in deleted state: `Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword`

- Purge a secret in deleted state: `Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword`

## Purging objects

To purge (permanently delete) a key vault by using the `Remove-AzureRmKeyVault` command with the option `-InRemovedState` and by specifying the location of the deleted key vault with the `-Location location` argument. You can find the location of a deleted vault using the command `Get-AzureRmKeyVault -InRemovedState`.

`Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus`

When a key vault is purged, all of its contents (ex. keys and secrets) are permanently deleted.

- To purge a deleted key vault so that the vault and all its contents are permanently removed, the user needs RBAC permission to perform a *Microsoft.KeyVault/locations/deletedVaults/purge/action* operation. 
- To list the deleted key, the vault a user needs RBAC permission to perform ‘Microsoft.KeyVault/deletedVaults/read’ permission. 
- By default only a subscription administrator has these permissions. 

## See also

For an overview of Key Vault's soft-delete feature, see [Azure Key Vault soft-delete overview](key-vault-ovw-soft-delete.md).

