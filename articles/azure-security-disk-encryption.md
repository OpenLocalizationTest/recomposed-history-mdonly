<properties
   pageTitle="Getting started with Microsoft Azure security | Microsoft Azure"
   description="This article provides an overview of Microsoft Azure Security capabilities and general considerations for organizations that are migrating their assets to a cloud provider."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/17/2015"
   ms.author="yuridio"/>

**NOTE**: Certain recommendations contained herein may result in increased data, network, or compute resource usage resulting in additional license or subscription costs.

##Introduction

Microsoft Azure is strongly committed to ensuring your data privacy, data sovereignty and enables you to control your Azure hosted data through a range of advanced technologies to encrypt, control and manage encryption keys, control & audit access of data. This provides Azure customers the flexibility to choose the solution that best meets their business needs. In this paper, we will introduce you to a new technology solution “Azure Disk Encryption for Windows and Linux IaaS VM’s” to help protect and safeguard your data to meet your organizational security and compliance commitments. The paper provides detailed guidance on how to use the Azure disk encryption features including the supported scenarios and the user experiences.

##Overview

Azure Disk Encryption is a new capability that lets you encrypt your Windows and Linux IaaS virtual machine disks. Azure Disk Encryption leverages the industry standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) feature of Windows and the [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) feature of Linux to provide volume encryption for the OS and the data disks. The solution is integrated with [Azure Key](https://azure.microsoft.com/documentation/services/key-vault/) Vault to help you control and manage the disk encryption keys and secrets in your key vault subscription, while ensuring that all data in the virtual machine disks are encrypted at rest in your Azure storage.

###Encryption Scenarios

The Azure Disk Encryption solution supports the following 3 customer encryption scenarios:

1.  Enable encryption on new IaaS VM’s created from Customer Encrypted VHD and encryption keys

2.  Enable encryption on new IaaS VM’s created from the Azure Gallery

3.  Enable encryption on existing IaaS VM’s already running in Azure

The solution supports the following for IaaS VMs for public preview release when enabled in Microsoft Azure:

1. Integration with Azure Key Vault

2. Standard [A, D and G series IaaS VMs](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/)

3. Enable encryption on IaaS VMs created using [Azure Resource Manager](resource-group-overview.md) model

4. All Azure public [regions](https://azure.microsoft.com/en-us/regions/)


The solution does not support the following scenarios, features and technology in the public preview release:

1.  Basic VMs and Standard DS (Premium Storage) series IaaS VMs

2.  IaaS VMs created using classic VM creation method

3.  Ability to disable encryption on the IaaS VM, enabled via Azure disk encryption

4.  Integration with your on-premises Key Management Service

5.  Windows Server Technical Preview 3

6.  Red Hat Enterprise Linux**
    **

###Encryption Features

When you enable and deploy Azure disk encryption for Azure IaaS VMs, the following capabilities are enabled, depending on the configuration provided:

1.  Encryption of OS volume to protect boot volume at rest in customer storage

2.  Encryption of Data volume/s to protect the data volumes at rest in customer storage

3.  Safeguarding the encryption keys and secrets in customer Azure key vault subscription

4.  Reporting encryption status of the encrypted IaaS VM

5.  Removal of disk encryption configuration settings from the IaaS virtual machine

The Azure disk encryption for IaaS VMS for Windows and Linux solution includes the disk encryption extension for Windows, disk encryption extension for Linux, disk encryption PowerShell cmdlets, disk encryption CLI cmdlets and disk encryption Azure Resource Manager templates. The Azure disk encryption solution is supported on IaaS VMs running Windows or Linux OS. For more details on the supported OS’es, see pre-requisites section (\#2, \#3) below.

There is no charge for encrypting VM disks with Azure Disk Encryption during the public preview. We also expect this to continue to be the case after Disk Encryption is generally available. However, pricing is subject to change based on market and competitive landscape.

###Value Proposition

The Azure Disk Encryption Management solution enables the following business needs in the cloud:

-   IaaS VM’s are secured at rest using industry standard encryption technology to address organizational security and compliance requirements.

-   IaaS VM’s boot under customer controlled keys and policies, and they can audit their usage in Key Vault.


###Encryption Workflow 
The high level steps required to enable disk encryption for Windows and Linux VM’s are:

1. Customer choose the encryption scenario from the above 3 encryption scenarios

2. Customer opts into enabling disk encryption via the Azure disk encryption ARM template or PS cmdlets or CLI command and specifies the encryption configuration

    - For the customer encrypted VHD scenario, the customer uploads the encrypted VHD to their storage account and encryption key material to their key vault and provide the encryption configuration to enable encryption on a new IaaS VM

    - For the new VM’s created from the Azure gallery and existing VM’s already running in Azure, customer provide the encryption configuration to enable encryption on the IaaS VM

3. Customer grants access to Azure platform to read the encryption key material (BitLocker Encryption Keys for Windows systems and Passphrase for Linux) from their key vault to enable encryption on the IaaS VM

4. Customer provide AAD identity to write the encryption key material to their key vault to enable encryption on the IaaS VM for scenarios 2 and 3 above.

5.  Azure service management updates the VM service model with encryption and key vault configuration and provisions encrypted VM for the customer

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption\disk-encryption-fig1.JPG)

##Prerequisites

The following are prerequisites to enable Azure Disk Encryption on Azure IaaS VMs for the supported scenarios called out in the overview section

- User must have a valid active Azure subscription to create resources in Azure in the regions supported

- Azure Disk Encryption is supported on the following Windows server SKU’s - Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. The solution is not supported on the Windows Server 2008 operating system. Windows Server Technical Preview is not supported in public preview release.

**Note**: For Windows Server2K8R2, .Net framework 4.5 MUST be installed before enabling encryption in Azure. You can install it from Windows update by installing the optional update "Microsoft .NET Framework 4.5.2 for Windows Server 2008 R2 x64-based Systems ([KB2901983](https://support.microsoft.com/kb/2901983))"

- Azure Disk Encryption is supported on the following Linux server SKUs - Ubuntu, CentOS, SUSE and SUSE Linux Enterprise Server (SLES). Red Hat Enterprise Linux is not supported in public preview release.

- All resources (Ex: KeyVault, Storage account, VM, VNet etc..) must belong to the same Azure region and subscription.

**Note** - Creating resources like KeyVault and VMs in different Azure regions is NOT supported and will cause failures in enabling Azure disk encryption feature

- Azure platform needs access to the encryption keys or secrets in customer Azure Key Vault in order to make them available to the VM to boot and decrypt the virtual machine OS volume. To grant permissions to Azure platform to access the Key Vault, **enabledForDiskEncryption** property must be set on the Key Vault for this requirement

- The KeyVault secret and key encryption key (KEK) URLs must be versioned. Azure service management enforces this restriction of versioning. See below examples for valid secret and KEK URL:

	- Example of valid secret URL:

		*https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- Example of valid KRK KEK:

		*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Azure disk encryption does not support port numbers being specified as part of KeyVault secret and KEK URLs. See below examples for supported keyvault URL:

 	- Unaccepted KeyVault URL

		*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- Accepted KeyVault URL:

		*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- To enable Azure Disk Encryption feature, the IaaS VMs must meet the following network endpoint configuration requirements: 

	- The IaaS VM must be able to connect to Azure Active Directory endpoint \[Login.windows.net\] to get a token to connect to Azure key vault

	- The IaaS VM must be able to connect to Azure Key Vault endpoint to write the encryptions keys to customer key vault

	- The IaaS VM must be able to connect to Azure storage endpoint which hosts the Azure extension repository and Azure storage account which hosts the VHD files

- To run any of the Azure disk encryption PowerShell cmdlets, you must first install Azure PowerShell version 1.0.1:

	- To install Azure PowerShell and associate it with your Azure subscription, see [How to install and configure Azure PowerShell](powershell-install-configure.md)

	- If the option above is not updated yet to include Azure PowerShell 1.0.1 release, you can also install Azure PowerShell 1.0.1 directly from [here](https://github.com/Azure/azure-powershell/releases/tag/v1.0.1-November2015)

**Note:** This document assumes that you understand the basic concepts, such as modules, cmdlets, and sessions. For more information, see Getting started with [Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

- To run any of the Azure CLI commands and associate it with your Azure subscription, you must first install Azure CLI version:

	- To install Azure CLI and associate it with your Azure subscription, see [How to install and configure Azure CLI](xplat-cli-install.md)

	- Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Manager, see [here](azure-cli-arm-commands.md)

- Azure disk encryption solution use BitLocker external key protector for Windows IaaS VMs. If your VMs are domain joined, do not push any group policies that enforce TPM protectors. Refer to [this article](https://technet.microsoft.com/en-us/library/ee706521) for details on the group policy for “Allow BitLocker without  a compatible TPM”. 

###Terminology

Use the terminology table as reference to understand some of the common terms used by this technology:


| Terminology           | Definition                                                                                                                                                                                                                                   |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AAD                   | AAD is [Azure Active Directory](https://azure.microsoft.com/en-us/documentation/services/active-directory/). AAD account is a pre-requisite for authenticating, storing, and retrieving secrets from the Key Vault.                                                                                                        |
| Azure Key Vault [AKV] | Azure Key Vault is a cryptographic key management service based on FIPS-validated Hardware Security Modules to safeguard your cryptographic keys and sensitive secrets securely.,Refer to [Key Vault](https://azure.microsoft.com/en-us/services/key-vault/) documentation for more details.          |
| ARM                   | Azure Resource Manager                                                                                                                                                                                                                       |
| BitLocker             | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) is an industry recognized Windows volume encryption technology used to enable disk encryption on Windows IaaS VMs                                                                                                                  |
| BEK                   | BitLocker Encryption Keys are used to encrypt the OS boot volume and data volumes. The BitLocker keys are safeguard in customer’s Azure key vault as secrets.                                                                              |
| CLI                   | [Azure Command-Line Interface](xplat-cli-install.md)                                                                                                                                                                                                                 |
| DM-Crypt              | [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) is the Linux-based transparent disk encryption subsystem used to enable disk encryption on Linux IaaS VMs                                                                                                                           |
| KEK                   | Key Encryption Key is the asymmetric key (RSA 2048) used to protect or wrap the secret if desired. You can provide an HSM-protected key or software-protected key. For more details, refer to [Azure Key Vault](https://azure.microsoft.com/en-us/services/key-vault/) documentation for more details |
| PS cmdlets            | [Azure PowerShell cmdlets](https://azure.microsoft.com/en-us/documentation/articles/powershell-install-configure/)                                                                                                                                                                                                                                             |

##Disk Encryption deployment scenarios and user experiences

There are many scenarios that you can enable disk encryption and the steps may vary according to the scenario. The sections that follows will cover in more details these scenarios.

###Enable encryption on new IaaS VM’s created from the Azure Gallery

Disk encryption can be enabled on new IaaS Windows VM from Azure gallery in Azure using the ARM template published [here](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image). Click on “Deploy to Azure” button on the Azure quickstart template, input encryption configuration in the parameters blade and click OK. Select the subscription, resource group, resource group location, legal terms and agreement and click Create button to enable encryption on a new IaaS VM.

**Note:** This template creates a new encrypted Windows VM using the Windows Server 2012 gallery image.

You can see the ARM template parameters details for new VM from Azure gallery scenario using AAD Client ID in the table below:

| Parameter                        | Description|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName                 | Admin user name for the virtual machine                                                                                                                           |
| adminPassword                 | Admin user password for the virtual machine                                                                                                                       |
| newStorageAccountName         | Name of the storage account to store OS and data vhds                                                                                                             |
| vmSize                        | Size of the VM. Currently, only Standard A, D and G series are supported                                                                                          |
| virtualNetworkName            | Name of the VNet to which the VM NIC should belong to.                                                                                                            |
| subnetName                    | Name of the subnet in the vNet to which the VM NIC should belong to                                                                                               |
| AADClientID                   | Client ID of the AAD app that has permissions to write secrets to Key Vault                                                                                       |
| AADClientSecret               | Client Secret of the AAD app that has permissions to write secrets to Key Vault                                                                                   |
| keyVaultResourceID,ResourceID | Identify the key vault resource in ARM. You can get it using the PowerShell cmdlet: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).ResourceId               |
| keyVaultURL                   | URL of the Key Vault to which BitLocker key should be uploaded to. You can get it using the cmdlet: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI |
| keyEncryptionKeyURL           | URL of the Key Encryption Key that's used to encrypt the generated BitLocker key. This is optional.                                                               |
| vmName                        | Name of the VM on which encryption operation is to be performed 


**Note:** KeyEncryptionKeyURL is an optional parameter. You can bring your own KEK to further safeguard the data encryption key (Passphrase secret) in Key Vault.

###Enable encryption on new IaaS VM’s created from Customer Encrypted VHD and encryption keys

In this scenario you can enable encrypting by using the ARM template, [PowerShell cmdlets](https://msdn.microsoft.com/en-us/library/azure/mt603746.aspx) or CLI commands. The sections below will explain in more details the ARM template and CLI commands.

####Arm Template

Disk encryption can be enabled on customer encrypted VHD using the ARM template published [here](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm). Click on “Deploy to Azure” button on the Azure quickstart template, input encryption configuration in the parameters blade and click OK. Select the subscription, resource group, resource group location, legal terms and agreement and click Create button to enable encryption on new IaaS VM.

The ARM template parameters details for customer encrypted VHD scenario are described in the table below:

| ​Parameter               | ​Description                                                                                                                                                                                                    
|
| newStorageAccountName | Name of the storage account to store encrypted OS vhd. This storage account should have already been created in the same resource group and same location as the VM                                                     |
| osVhdUri              | URI of OS vhd from storage account                                                                                                                                                                                      |
| osType                | OS product type (Windows/Linux)                                                                                                                                                                                         |
| virtualNetworkName    | Name of the VNet to which the VM NIC should belong to. This should have been already created in the same resource group and same location as the VM                                                                     |
| subnetName            | Name of the subnet in the vNet to which the VM NIC should belong to                                                                                                                                                     |
| vmSize                | Size of the VM. Currently, only Standard A, D and G series are supported                                                                                                                                                |
| keyVaultResourceID    | ResourceID identifying the key vault resource in ARM. You can get it using the PowerShell cmdlet: (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId |
| keyVaultSecretUrl     | ​URL of the disk encryption key provisioned in key vault                                                                                                                                                                |
| keyVaultKekUrl        | URL of the Key Encryption Key that’s to encrypt the generated disk encryption key                                                                                                                                       |
| ​vmName               | ​Name of the IaaS VM   


####CLI Commands

Follow the steps below to enable disk encryption for this scenario using CLI commands:

1. Set access policies on KeyVault:
	- Set ‘EnabledForDiskEncryption’ flag: “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Set permissions to AAD app to write secrets to KeyVault: “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]”
2. To enable encryption on an existing/running VM, type: 
	*azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Get encryption status: *“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”* 
4. To enable encryption on a new VM from customer encrypted VHD, use the below parameters with “azure vm create” command:
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


###Enable encryption on existing/running IaaS Windows VM in Azure

In this scenario you can enable encrypting by using the ARM template, PowerShell cmdlets or CLI commands. The sections below will explain in more details each one of them.

####Using ARM template

Disk encryption can be enabled on existing/running IaaS Windows VM in Azure using the ARM template published [here](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm). Click on “Deploy to Azure” button on the Azure quickstart template, input encryption configuration in the parameters blade and click OK. Select the subscription, resource group, resource group location, legal terms and agreement and click Create button to enable encryption on existing/running IaaS VM.

The ARM template parameters details for existing/running VM scenario using AAD Client ID are available in the table below:

| Parameter                 | Description|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID            | ​Client ID of the AAD app that has permissions to write secrets to Key Vault                                                                                                                                              |
| AADClientSecret         | ​Client Secret of the AAD app that has permissions to write secrets to Key Vault                                                                                                                                          |
| **​**keyVaultResourceID | ResourceID identifying the key vault resource in ARM. You can get it using the PowerShell cmdlet: (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId   |
| ​keyVaultURL            | ​URL of the Key Vault to which BitLocker key should be uploaded to. You can get it using the cmdlet: (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;). VaultURI |
| ​ keyEncryptionKeyURL   | ​URL of the Key Encryption Key that's used to encrypt the generated BitLocker key. This is optional                                                                                                                       |
| ​volumeType             | ​Type of the volume on which encryption operation is performed. Valid values are "OS", "Data" , "All"                                                                                                                     |
| sequenceVersion         | Sequence version of the BitLocker operation. Increment this version number every time a disk encryption operation is performed on the same VM                                                                             |
| ​vmName                 | ​Name of the VM on which encryption operation is to be performed


**Note:** KeyEncryptionKeyURL is an optional parameter. You can bring your own KEK to further safeguard the data encryption key (BitLocker encryption secret) in Key Vault.

###Enable encryption on existing/running IaaS Linux VM in Azure:

Disk encryption can be enabled on existing/running IaaS Linux VM in Azure using the ARM template published  here. Click on “Deploy to Azure” button on the Azure quickstart template, input encryption configuration in the parameters blade and click OK. Select the subscription, resource group, resource group location, legal terms and agreement and click Create button to enable encryption on existing/running IaaS VM.

The ARM template parameters details for existing/running VM scenario using AAD Client ID are described in the table below:

| Parameter                 | Description                                                                                                                                                          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID            | ​Client ID of the AAD app that has permissions to write secrets to Key Vault                                                                                                                                              |
| AADClientSecret         | ​Client Secret of the AAD app that has permissions to write secrets to Key Vault                                                                                                                                          |
| **​**keyVaultResourceID | ResourceID identifying the key vault resource in ARM. You can get it using the PowerShell cmdlet: (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId   |
| ​keyVaultURL            | ​URL of the Key Vault to which BitLocker key should be uploaded to. You can get it using the cmdlet: (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;). VaultURI |
| ​ keyEncryptionKeyURL   | ​URL of the Key Encryption Key that's used to encrypt the generated BitLocker key. This is optional                                                                                                                       |
| ​volumeType             | ​Type of the volume on which encryption operation is performed. Valid supported value is "Data". Linux VM do not support enabling encryption on OS volume on running Linux VM
| sequenceVersion         | Sequence version of the BitLocker operation. Increment this version number every time a disk encryption operation is performed on the same VM                                                                             |
| passPhrase              | Type a strong passphrase as the data encryption key                                                                                                                                                                       |
| ​vmName                 | ​Name of the VM on which encryption operation is to be performed                                                                                                               
                                                                                                                                                                                                                                                      
                                                                                                                                                            
**Note:** KeyEncryptionKeyURL is an optional parameter. You can bring your own KEK to further safeguard the data encryption key (Passphrase secret) in Key Vault.

####PowerShell
Disk encryption can be enabled on customer encrypted VHD using the PS cmdlets published [here](https://msdn.microsoft.com/en-us/library/azure/mt622707.aspx). You can use *get-command diskencryption* to confirm the Azure Disk Encryption cmdlets are installed. You should see an output containing the Disk Encryption cmdlets.

**Note:** Make sure that you have [Azure PowerShell](powershell-install-configure.md) installed.

####CLI Commands

Disk encryption can be enabled on customer encrypted VHD using the CLI command installed from [here](https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/). Follow the CLI command guidance to enable encryption in appendix section G.

1. Set access policies on KeyVault:
	- Set ‘EnabledForDiskEncryption’ flag: “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Set permissions to AAD app to write secrets to KeyVault: “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]”
2. To enable encryption on an existing/running VM, type:
	*azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. Get encryption status: “azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json” 
4. To enable encryption on a new VM from customer encrypted VHD, use the below parameters with “azure vm create” command.
	- *disk-encryption-key-vault-id <disk-encryption-key-vault-id>*
	- *disk-encryption-key-url <disk-encryption-key-url>*
	- *key-encryption-key-vault-id <key-encryption-key-vault-id>*
	- *key-encryption-key-url <key-encryption-key-url>*

###Get encryption status of an encrypted IaaS VM

You can get encryption status using Azure Management (Preview) portal, [PowerShell cmdlets](https://msdn.microsoft.com/en-us/library/azure/mt622700.aspx) or CLI commands. The sections below will explain how to use the Azure Management (Preview) portal and CLI commands to get the encryption status.

####Get encryption status of an encrypted IaaS VM using Azure Management Preview portal

You can get the encryption status of the IaaS VM from Azure management preview portal. Logon to Azure preview portal at https://portal.azure.com/, click on virtual machines link in the left menu to see summary view of the virtual machines in your subscription. You can filter the virtual machines view by selecting the subscription name from the subscription dropdown. Click on columns located at the top of the virtual machines page menu. Select Disk Encryption column from the choose column blade and click update. You should see the disk encryption column showing the encryption state “Enabled” or “Not Enabled” for each VM as shown in the figure below.

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption\disk-encryption-fig2.JPG)

####Get encryption status of the IaaS VM from disk encryption CLI command

You can get the encryption status of the IaaS VM from disk encryption CLI command *azure vm show-disk-encryption-status*. To get the encryption settings for your VM, type in your Azure CLI session:
 
*azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json*  


##Appendix 

###Setting and Configuring Azure Key Vault for Azure disk encryption usage

Azure disk encryption safeguards the disk encryption keys and secrets in your Azure Key Vault. Follow the steps on each one of the sections below to setup Key Vault for Azure disk encryption usage.
 
**Note:** The Key Vault MUST be in the same region as the VMs.

###Create a New Key Vault
To create a new Key Vault, use one of the two options listed below. Once you create the Key Vault, proceed to step 2.3.1:

- Use the "101-Create-KeyVault" ARM template located [here](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json) 
- Use the Azure PowerShell Key Vault cmdlets as described [here](key-vault-get-started.md)

**Note:** If you already have a Key Vault setup for your subscription, please proceed to next section.

###Provisioning a Key Encryption Key (optional)

If you wish to use a Key Encryption Key (KEK) for an additional layer of security to wrap the BitLocker encryption keys, you should add a KEK to your key vault for use in the provisioning process.  Use the [Add-AzureKeyVaultKey](https://msdn.microsoft.com/en-us/library/dn868048.aspx) cmdlet to create a new Key Encryption Key in Key Vault. For more details, see [Key Vault documentation](https://azure.microsoft.com/en-us/documentation/services/key-vault/).

###Set Key Vault permissions to allow the Azure platform access to the keys and secrets

The Azure platform needs access to the encryption keys or secrets in your Azure Key Vault in order to make them available to the VM to boot and decrypt the volumes. To grant permissions to the Azure platform so that it can access the Key Vault, the *enabledForDiskEncryption* property must be set on the Key Vault.

####Setting “enabledForDiskEncryption” property on the Key Vault using Azure Resource Explorer

You must set the *enabledForDiskEncryption* property on your Key Vault as mentioned before. You can set the property by visiting https://resources.azure.com and follow the steps below:

1. Click on subscriptions
2. Click on <subscription name>
3. Click on resource groups
4. Click on <ResourceGroup name>
5. Click on Providers
6. Click on Microsoft.KeyVault
7. Click on <KeyVaultName> 
8. Edit the vault properties in your subscription by: 
	- Selecting the vault
	- Setting the mode to Read/Write
	- Clicking on Edit and adding [“enabledForDiskEncryption”: true,] and then posting it

Regardless of the method chosen to deploy the Key Vault, ensure that the properties detailed above are set correctly otherwise the deployment will fail.

###Setup the AAD Application in Azure Active Directory

When encryption needs to be enabled on a running VM in Azure, Azure disk encryption generates and writes the encryption keys to your Key Vault. Managing encryption keys in Key Vault needs AAD authentication.
 
For this purpose, an AAD application should be created. Detailed steps for registering an application can be found here, in the section “Get an Identity for the Application” section.  This post also contains a number of helpful examples on provisioning and configuring your Key Vault. For authentication purposes, either client secret based authentication or client certificate-based AAD authentication can be used. 

####Client secret based authentication for AAD:

The sections that follows have the necessary steps to configure a client secret based authentication for AAD.

#####Create a new AAD app using Azure PowerShell

Use the PowerShell cmdlet below to create a new AAD app:

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**Note:** $azureAdApplication.ApplicationId is the AAD ClientID and $aadClientSecret  is the client Secret that you should use later to enable ADE.

#####Provisioning the AAD client ID and secret from the Azure Classic (Azure Service Management) Portal

AAD Client ID and secret can also be provisioned using the Azure Classic (Azure Service Management) portal at https://manage.windowsazure.com, follow the steps below to perform this task:

1. Click the Active Directory tab as shown in Figure below:

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption\disk-encryption-fig3.JPG)

2. Click Add Application and type the application name as shown below:

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption\disk-encryption-fig4.JPG)

3. Click the arrow button and configure the app's properties as shown below:

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption\disk-encryption-fig5.JPG)

4. Click the check mark in the lower left corner to finish. The app's configuration page appears. Notice the AAD Client ID is located in the bottom of the page as shown in figure below.
 
![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption\disk-encryption-fig6.JPG)

5. Save the AAD client secret by click in the Save button. Click the save button and note the secret from the keys textbox, this is the AAD client secret.

![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption\disk-encryption-fig7.JPG)


**Note:** this flow above is not supported in the Preview Portal.

#####Use an existing app

In order to execute the commands below you need the Azure AD PowerShell module, which can be obtained from [here](https://technet.microsoft.com/library/jj151815.aspx).

**Note:** the commands below must be executed from a new PowerShell window. Do NOT use Azure PowerShell or the Azure Resource Manager window to execute these commands. The reason for this recommendation is because these cmdlets are in the MSOnline module or Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’ 
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

####Certificate based authentication for AAD

The sections that follows have the necessary steps to configure a certificate based authentication for AAD.

#####Create a new AAD app

Execute the PowerShell cmdlets below to create a new AAD app:

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Once you finish this step, upload a .pfx file to Key Vault and enable the access policy needed to deploy that certificate to a VM.

#####Use an existing AAD app
If you are configuring certificate based authentication for an existing app, use the PowerShell cmdlets below. Make sure to execute them from a new PowerShell window.

    $clientSecret = ‘<yourAadClientSecret>’ 
    $aadClientID = '<Client ID of your AAD app>'

    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret
 

#####Upload a PFX file to KeyVault
You can read this [blog post](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) for detail explanation on how this process works. However, the PowerShell cmdlets below are all you need for this task. Make sure to execute them from Azure PowerShell console:

    $certLocalPath = 'C:\certs\myaadapp.pfx' 
    $certPassword = "yourPassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
    Set-AzureKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

#####Deploy a certificate in Key Vault to an existing VM
After finishing uploading the PFX, use the steps below to deploy a certificate in Key Vault to an existing VM:

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’

    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureVM -VM $vm  -ResourceGroupName $resourceGroupName

###Setting Key Vault Access policy for the AAD Application

Your AAD application needs rights to access the keys or secrets in the vault. Use the [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) cmdlet to grant permissions to the application, using the Client Id (which was generated when the application was registered) as the –ServicePrincipalName parameter value. You can read [this blog post](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) for some examples on that. Below you also have an example of how to perform this task via PowerShell:

    $keyVaultName = ‘yourKeyVaultName’
    $aadClientID = '<youAadAppClientID>'
    Set-AzureKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys all -PermissionsToSecrets all

###Preparing a pre-encrypted Windows VHD
The sections that follows are necessary in order to prepare a pre-encrypted Windows VHD for deployment as an encrypted VHD in Azure IaaS. The steps are used to prepare and boot a fresh windows VM (vhd) on Hyper-V or Azure.

####Update group policy to allow non-TPM for OS protection
You need to configure the BitLocker Group Policy setting called BitLocker Drive Encryption, located under Local Computer Policy \Computer Configuration\Administrative Templates\Windows Components. Change this setting to: *Operating System Drives - Require additional authentication at startup - Allow BitLocker without a compatible TPM* as shown in the figure below:
 
![Microsoft Antimalware in Azure](./media/azure-security-disk-encryption\disk-encryption-fig8.JPG)

####Install BitLocker feature components
For Windows Server 2012 and above use the below command:
 
    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

For Windows Server 2008 R2 use the below command:

    ServerManagerCmd -install BitLockers

####Prepare OS volume for BitLocker using bdehdcfg

Execute the command below to compress the OS partition and prepare the machine for BitLocker.

    bdehdcfg -target c: shrink -quiet

####Using BitLocker to protect the OS volume
Use the [manage-bde](https://technet.microsoft.com/library/ff829849.aspx) command to enable encryption on the boot volume using an external key protector and place the external key (.bek file) on the external drive or volume. Encryption will be enabled on the system/boot volume after the next reboot.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Note:** The VM needs to be prepared with a separate data/resource vhd for getting the external key using BitLocker.

####Preparing a pre-encrypted Linux VHD

#####Ubuntu 14.

1.Create a file under /usr/local/sbin/azure_crypt_key.sh, with the content in the script below. Pay attention to the KeyFileName, because it is the passphrase file name put by Azure.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    sleep 2
    OPENED=0
    for SFS in /sys/block/sd*; do
        DEV=`basename $SFS`
        F=$SFS/${DEV}1/dev
        echo "> Trying device: $DEV ..." >&2
        mount /dev/${DEV}1 $MountPoint -t vfat -r >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2.Change the crypt config in */etc/crypttab*. It should look like this:

    Sda5_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3.If you are editing the *azure_crypt_key.sh* in Windows and copied it to Linux, do not forget to run *dos2unix /usr/local/sbin/azure_crypt_key.sh*.
4.Run *update-initramfs -u -k all* to update the initramfs to make the keyscript take effect.

#####openSUSE 13.2.

1.Edit the /etc/dracut.conf add_drivers+="vfat nls_cp437 nls_iso8859-1"
2.Comment out these lines by the end of the file “/usr/lib/dracut/modules.d/90crypt/module-setup.sh”:

    #    inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator


3.Append DRACUT_SYSTEMD=0 at the beginning of the file “/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh” and change all “if [ -z "$DRACUT_SYSTEMD" ]; then” to “if [ 1 ]; then”
4.Edit /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh and append this after the “# Open LUKS device”

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    for SFS in /dev/sd*; do
       echo "> Trying device:$SFS..." >&2
       mount ${SFS}1 $MountPoint -t vfat -r >&2
       if [ -f $MountPoint/$KeyFileName ]; then
          echo "> keyfile got..." >&2
          luksfile=$MountPoint/$KeyFileName
          break
       fi
    done

4.Run the “dracut –f -v” to update the initrd

#####CentOS 7
1.Edit the /etc/dracut.conf add_drivers+=" vfat nls_cp437 nls_iso8859-1"
2.Comment out these lines by the end of the file “/usr/lib/dracut/modules.d/90crypt/module-setup.sh”:

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3.Append DRACUT_SYSTEMD=0 at the beginning of the file “/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh” and change all “if [ -z "$DRACUT_SYSTEMD" ]; then” to “if [ 1 ]; then”
4.Edit /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh and append this after the “# Open LUKS device”
5.Run the “/usr/sbin/dracut -f -v” to update the initrd.

###Upload encrypted VHD to an Azure storage account
The disk encryption secret obtained needs to be uploaded as a secret in Key Vault. 

####Disk encryption secret not encrypted with a KEK
Use Set-AzureKeyVaultSecret to provision the secret in key vault. In case of a Windows virtual machine, the bek file is encoded as a base64 string and then uploaded to key vault using the Set-AzureKeyVaultSecret cmdlet. For Linux, the passphrase is encoded as a base64 string and then uploaded to Key Vault. In addition, make sure that the following tags are set while creating the secret in key vault.

    "tags":
    {
       “DiskEncryptionKeyEncryptionAlgorithm”: “RSA-OAEP (optional)”
       "DiskEncryptionKeyFileName": "Bek file name (windows) or Passphrase filename (linux)"
    }

Make sure to also use the  following code:

    param(
      [Parameter(Mandatory=$True)]
      [String]$BekFilePath = "C:\vm\nbox\2640EE52-41B3-426C-87B9-484232452CE4.BEK",
      [String]$VaultName = "DiskEncryptionTestAus",
      [String]$SecretName = "BitLockerKey"
      ) 

    #"EAN//ojeIQk="
    $bekFileName = split-path $BekFilePath -leaf
    echo "Bek file name = $bekFileName"

    $secretBytes = [System.IO.File]::ReadAllBytes($BekFilePath);
    $secret = [Convert]::ToBase64String($secretBytes);
    echo "Secret = $secret"

    $secureSecret = ConvertTo-SecureString $secret -AsPlainText -Force
    $tags = @{"DiskEncryptionKeyFileName" = "$bekFileName"} 

    echo "Tags = $tags"
    echo "Vault = $VaultName"
    echo "Secret name = $SecretName"
    echo "Adding secret to Key vault"

    Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secureSecret -tags $tags


####Disk encryption secret encrypted with a KEK

The secret can optionally be encrypted with a Key Encryption Key before uploading to Key vault. Use the wrap [API](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx) to first encrypt the secret using the Key Encryption Key. The output of this wrap operation is a base64 URL encoded string which is then uploaded as a secret using the [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) cmdlet.

