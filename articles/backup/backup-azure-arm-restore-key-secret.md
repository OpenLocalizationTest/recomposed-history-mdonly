<properties
	pageTitle="Restore Key Vault key and secret using Azure Backup | Microsoft Azure"
	description="Learn how to restore Key Vault key and secret in Azure Backup using PowerShell"
	services="backup"
	documentationCenter=""
	authors="JPallavi"
	manager="vijayts"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/10/2016"
	ms.author="JPallavi" />

# Restore Key Vault key and secret using Azure Backup
This article talks about using Azure VM Backup to perform restore of encrypted Azure VMs, if your key and secret do not exist in the key vault.

## Pre-requisites

1. **Backup of encrypted VMs** - Encrypted Azure VMs have been backed up using Azure Backup. Please refer the article [Deploy and manage backups using Azure PowerShell](backup-azure-vms-automation.md) for details about how to backup encrypted Azure VMs.

2. **Configure Azure Key Vault** – Ensure that key vault to which keys and secrets need to be restored is already present. Please refer the article [Get Started with Azure Key Vault](../key-vault/key-vault-get-started.md) for details about key vault management.

## Restore Azure key vault key and secret
Use the following steps in Azure PowerShell to restore key and secret back to Azure key vault, if they do not exist.

### Login to Azure PowerShell and set subscription context

1. Login to Azure account using the below cmdlet

```
PS C:\> Login-AzureRmAccount
```

2. Once logged in, use the below cmdlet to get the list of your available subscriptions

```
PS C:\> Get-AzureRmSubscription
```

3. Select the subscription in which resources are available

```
PS C:\> Set-AzureRmContext -SubscriptionId "subscription-id"
```

### Set recovery services vault context

1. Use Recovery Services vault where backup was enabled for encrypted VMs

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName “rg-name” -Name “rs-vault-name”| Set-AzureRmRecoveryServicesVaultContext
```

### Get recovery point for backed up encrypted VM

1. Select container in the vault that represents encrypted Azure virtual machine

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "vm-name"
```

2. Using this container, get back up item for the corresponding virtual machine

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

3. Get an array of recovery points for the selected backup item in the variable rp

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

### Restore key

1. The array $rp above, is sorted in reverse order of time with the latest recovery point at index 0. For example: $rp[0] will select the latest recovery point.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

**Note:** After this cmdlet runs successfully, a blob file gets generated in the specified folder on the machine where it is run. This blob file represents Key Encrypted Key in encrypted form.

2. Restore key back to the key vault

```
PS C:\> Restore-AzureKeyVaultKey -VaultName “contosokeyvault” -InputFile "C:\Users\ downloads\key.blob"
```

### Restore secret

1. Get secret data from recovery point obtained above

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

**Note:** The text before vault.azure.net represents key vault name; the text after secrets/ represents secret name. 

2. Get the secret name and value from the output of the cmdlet run above

```
PS C:\> $secretname = “B3284AAA-DAAA-4AAA-B393-60CAA848AAAA”
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

3. Set Tags for the secret, in case VM needs to be restored as well

```
PS C:\> $Tags = @{ 'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP'; 'DiskEncryptionKeyFileName' = ‘B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK’ -DiskEncryptionKeyEncryptionKeyURL' = 'https:// contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

**Note:** Value for DiskEncryptionKeyFileName is same as secret name obtained above. Value for DiskEncryptionKeyEncryptionKeyURL can be obtained from key vault after restoring the keys back and using [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) cmdlet	

4. Set the secret back to the key vault

```
PS C:\> Set-AzureKeyVaultSecret -VaultName “contosokeyvault” -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```


The above PowerShell cmdlets will help you restore key and secret back to the key vault if you had backed up encrypted VMs using Azure VM Backup. After restoring them, please refer the article [Deploy and manage Azure VMs](backup-azure-vms-automation.md) to restore encrypted VMs.
