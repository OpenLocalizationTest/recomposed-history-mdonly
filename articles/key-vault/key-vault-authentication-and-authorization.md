<properties
	pageTitle="Serucre your Key Vault | Microsoft Azure"
	description="Manage access permissions for Key Vault for managing vaults and keys and secrets. Authentication and authorization model for Key Vault and how to secure your key vault"
	services="key-vault"
	documentationCenter=""
	authors="amitbapat"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/07/2016"
	ms.author="ambapat"/>


# Secure your Key Vault

Azure Key Vault is a cloud service that safeguards encryption keys and secrets (such as certificates, connection strings, passwords) for your cloud applications. Since this data is sensitive and business critical, you want to secure access to your key vaults so that only authorized applications and users can access key vault. This article provides an overview of Key Vault access model, explains authentication and authorization, and describes how to secure access to Key Vault for your cloud applications with an example.

## Management plane and data plane

Key Vault is an Azure service available via Azure Resource Manager deployment model. When you create a key vault, you get a virtual container inside which you can create other objects like keys and secrets. Access to Key Vault is controlled through two separate interfaces: **management plane** and **data plane**. Management plane interface is used to manage your key vault itself, such as creating, deleting, updating your key vault and also setting access policies for data plane. Data plane interface is used to access and use the keys and secrets stored in your key vault.

The management plane and data plane interfaces are accessed through different endpoints (see table). The second column in the table describes the DNS names for these endpoints in different Azure environments. The third column describes the operations you can perform from each access plane. Each access plane also has its own access control mechanism: for management plane access control is set using Azure Resource Manager Role-Based Access Control (RBAC), while for data plane access control is set using Key Vault access policy.

| Access plane | Access endpoints | Access permissions |
|--------------|------------------|--------------------|
| Management plane|**Global:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Create/Read/Update/Delete Key Vault <br> Set access policies for Key Vault<br>Set tags for Key Vault<br><br> Access control using Azure Resource Manager Role-Based Access Control (RBAC) |
| Data plane | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Permissions for Keys: Decrypt, Encrypt, UnwrapKey, WrapKey, Verify, Sign, Get, List, Update, Create, Import, Delete, Backup, Restore<br><br> Permissions for Secrets: Get, List, Set, Delete <br><br>Access control using Key Vault Access Policy|

The management plane and data plane access control work independently. For example, if you want to grant an application access to use keys in a key vault, you only need to grant data plane access permissions using Key Vault access policies and no management plane access is needed for this application. And conversely, if you want a user to be able to read vault properties and tags, but not have any access to keys or secrets, you can grant this user, 'read' access using RBAC and no access to data plane is required.

## Key Vault authentication using Azure Active Directory

When you create a key vault in an Azure subscription, it is automatically associated with the parent Azure Active Directory tenant. All callers (users and applications) must be registered in this tenant to access this key vault. An application must  authenticate with Azure Active Directory to access key vault. This applies to both management plane and data plane access. In both cases, an application can access Key Vault in two ways:

-  **user+app access** - usually this is for applications that access key vault on behalf of a signed-in user. Azure PowerShell, Azure Portal are examples of this type of access. There are two ways to grant access to users: one way it to grant access to users so they can access key vault from any application and the other way is to grant a user access to key vault only when they use a specific application. 
-  **app-only access** - for applications that run daemon services, background jobs etc. The application's identity is granted access to the key vault.

In both types of applications, the application authenticates with with Azure Active Directory using any of the [supported authentication methods](../active-directory/active-directory-authentication-scenarios.md) and acquires a token. Authentication method used depend on the application type. Then the application uses this token and sends REST API request to Key Vault. In case of management plane access the requests are routed through Azure Resource Manager endpoint. When accessing data plane, the applications talks directly to a Key Vault endpoint. See more details on the [whole authentication flow](../active-directory/active-directory-protocols-oauth-code.md). 

The resource name for which the application requests a token will be different depending on whether the application is accessing management plane or data plane. Hence the resource name is either management plane or data plane endpoint described in the table earlier, depending on the the Azure environment.

Having one single mechanism for authentication to both management and data plane has its own benefits:

- Orgnizations can centrally crontrol access to all key vaults in their organization
- If a user leaves, they instantly lose access to all key vaults in the organization
- Organizations can customize authentication via the options in Azure Active Directory (for example, enabling multi-factor authentication for added security)



## Management plane access control

The management plane consists of operations that affect the key vault itself. For example, you can create or delete a key vault. Get a list of vaults in a subscription. Retrieve key vault properties (such as SKU, tags) and also set key vault access policies that control the users and applications that can access keys and secrets in the key vault. Management plane access control uses RBAC.

### Role-based Access Control (RBAC)
Each Azure subscription has an Azure Active Directory. Users, groups, and applications from this directory can be granted access to manage resources in the Azure subscription that use the Azure Resource Manager deployment model. This type of access control is referred to as Role-Based Access Control (RBAC). To manage this access, you can use the [Azure portal](https://portal.azure.com/), the [Azure CLI tools](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md), or the [Key Vault Management REST APIs](https://msdn.microsoft.com/library/azure/mt620024.aspx).

With the Azure Resource Manager model, you create your key vault in a resource group and control access to the management plane of this key vault by using Azure Active Directory. For example, you can give specific users ability to set key vault access policy, while other users can only view and list key vaults in this resource group, but not change key vault access policy.

You can grant access to users, groups and applications at a specific scope by assigning appropriate RBAC roles. For example, to grant access to a user to manage key vaults you would assign a predefined role 'Key Vault Contributor' to this user at a specific scope. The scope in this case would be either a subscription, a resource group, or just a specific key vault. A role assigned at subscription level applies to all resource groups and resources within that subscription. A role assigned at resource group level applies to all resources in that resource group. A role assigned for a specific resource only applies to that resource. There are several predefined roles (see [RBAC: Built-in roles](../active-directory/role-based-access-built-in-roles.md)), and if the predefined roles do not fit your needs you can also define your own roles .

>[AZURE.IMPORTANT] Note that if a user has Contributor permissions (RBAC) to a key vault management plane, she can grant herself access to data plane, by setting Key Vault access policy. Therefore it is recommended to tightly control who has 'Contributor' access to your key vaults to ensure only authorized persons can access and manage your key vaults, keys, and secrets.

## Data plane access control

The Key Vault data plane consists of operations that affects the objects inside (such as keys, secrets and certificates) a key vault. This includes key operations such as create, import, update, list, backup, and restore keys, cryptographic operations such as sign, verify, encrypt, decrypt, wrap, and unwrap, and also set tags and other attributes for keys. Similarly for secrets it includes, get, set, list, delete.

Data plane access is granted by setting access policies for a key vault. A user, group, or an application must have Contributor permissions (RBAC) for management plane for a key vault to be able to set access policies for that key vault. A user, group, or application can be granted access to perform specific operations for keys or secrets in a key vault. Key Vault support up to 16 access policy entries for a key vault. Create an Azure Active Directory security group and add users to that group to grant data plane access to several users to a key vault.

### Key Vault Access Policies

Key Vault access policies grant permissions to keys, secrets and certificates separately. For example, you can give a user access to only keys, but no permissions for secrets. However permissions to access keys or secrets or certificates are at the vault level. In other words, Key Vault access policy does not support object level permissions. You can use [Azure portal](https://portal.azure.com/), the [Azure CLI tools](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md), or the [Key Vault Management REST APIs](https://msdn.microsoft.com/library/azure/mt620024.aspx) to set access policies for a key vault.

>[AZURE.IMPORTANT] Note that Key Vault access policies apply at the vault level. For example, when a user is granted permission to create and delete keys, she can perform those operations on all keys in that key vault.

## Example

Let's say you are developing an application that uses a certificate for SSL, Azure storage for storing data, and also uses an RSA 2048-bit key for sign operations. Let's say this application is running in a VM (or a VM Scale Set). You can use key vault to store all the application secrets, and also use key vault to store the bootstrap certificate that is used by the application to authenticate with Azure Active Directory.

So here's a summary of all the keys and secrets to be stored in a key vault.

- **SSL Cert** - used for SSL
- **Storage Key** - used to get access to Storage account
- **RSA 2048-bit key** - used for sign operations
- **Bootstrap certificate** - used to authenticate to Azure Active Directory, to get access to Key Vault to fetch the storage key and use the RSA key for signing.

Now let's meet the people who are managing, deploying and auditing this application. We'll use three roles in this example.

- **Security team** - These are typically IT staff from the 'office of the CSO (Chief Security Officer)' or equivalent, responsible for the proper safekeeping of secrets such as SSL certificates, RSA keys used for signing.
- **Developers/operators** - These are the folks who develop this application and then deploy it in Azure. Typically they are not part of the security team, and hence they should not have access to any sensitive data, such as SSL certificates, RSA keys, but the application they deploy should have access to those.
- **Auditors** - This is usually a different set of people, isolated from the developers and general IT staff. Their responsibility is to review proper use and maintenance of certificates, keys, etc. and also ensure compliance with data security standards. 

There is one more role that is outside the scope of this application, but relevant here to be mentioned, and that would be the subscription (or resource group) administrator. Subscription administrator sets up initial access permissions for the security team. Here we assume that the subscription administrator has granted access to the security team to a resource group in which all the resources needed for this application reside.

Now let's see what actions each role performs in the context of this application.

- **Security team**
	- Create Key Vaults
	- Turns on Key Vault logging
	- Add keys/secrets
	- Create backup of keys for disaster recovery
	- Set key vault access policy to grant permissions to users and applications to perform specific operations
	- Periodically roll keys/secrets
- **Developers/operators**
    - Get references to bootstrap and SSL certs (thumbprints), storage key (secret URI) and signing key (Key URI) from security team
    - Develop and deploy application that accesses keys and secrets programmatically
- **Auditors**
    - Review usage logs to confirm proper key/secret use and compliance with data security standards

Now let's see what access permissions to key vault are needed by each role (and also the application) to perform their assigned tasks. 

| User Role    | Management plane permissions | Data plane permissions |
|--------------|------------------------------|------------------------|
|Security Team|Key Vault Contributor|Keys: backup, create, delete, get, import, list, restore <br> Secrets: all|
|Developers/Operator| Key Vault deploy permission so that the VMs they deploy can fetch secrets from the key vault | None |
|Auditors| None | Keys: list<br>Secrets: list|
|Application| None | Keys: sign<br>Secrets: get |

>[AZURE.NOTE] Auditors need list permsion for keys and secrets so they can inspect attributes for keys and secrets that are not emitted in the logs, such as tags, activation and expiration dates.

Besides permission to key vault, all three roles also need access to other resources. For example, to be able to deploy VMs (or Web Apps etc.) Developers/Operators also need 'Contributor' access to those resource types. Auditors need read access to the storage account where the key vault logs are be stored.

Since the focus of this article is securing access to Key Vault, we only illustrate the relevant portions pertaining to this topic and skip details regarding deploying certificates, accessing keys and secrets programmatically etc. Those details are already covered elsewhere. Deploying certificates stored in key vault to VMs is covered in a [blog post](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/), and there is [sample code](https://www.microsoft.com/download/details.aspx?id=45343) available that illustrates how to use bootstrap certificate to authenticate to Azure AD to get access to key vault.

Most of the access permissions can be granted using Azure portal, but to grant granular permissions you may need to use Azure PowerShell (or Azure CLI) to achieve the desired result. 

The following PowerShell snippets assume:

- The Azure Active Directory administrator has created security groups that represent the three roles, namely Contoso Security Team, Contoso App Devops, Contoso App Auditors. The administrator has also added users to the groups they belong.

- **ContosoAppRG** is the resource group where all the resources reside. **contosologstorage** is where the logs are stored. 

- Key vault **ContosoKeyVault** and storage account used for key vault logs **contosologstorage** must be in the same Azure location


First the subscription administrator assigns 'Key Vault Contributor' role to the security team.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "Key Vault Contributor" -ResourceGroupName ContosoAppRG
```

The following script illustrates how the security team can create a key vault, setup logging, and set access permissions for other roles and the application. 

```
# Create Key Vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionToKeys backup,create,delete,get,import,list,restore -PermissionToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from Key Vault"
$devlopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $role

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionToKeys list -PermissionToSecrets list

```

The custom role defined, is only assinable to the subscription where the ContosoAppRG resource group is created. If the same custom roles will be used for other projects in other subscriptions, it's scope could have more subscriptions added.

The custom role assignment for the developers/operators for the "deploy/action" permission is scoped to the resource group. This way only the VMs created in the resource group 'ContosoAppRG' will get the secrets (SSL cert and bootstrap cert). Any VMs that a member of dev/ops team creates in other resource group will not be able to get these secrets even if they knew the secret URIs.

This example depicts a simple scenario. Real life scenarios may be more complex and you may need to adjust permissions to your key vault based on your needs. For example, in our example, we assume that security team will provide the key and secret references (URIs and thumbprints) that developers/operators team need to reference in their applications. Hence they don't need to grant developers/operators any data plane access. But if you want the developers/operators to be able to pick keys from Azure Portal while enabling encryption at rest for an Azure workload, they will need 'list' permission for keys and secrets. Also note that this example focuses on securing your key vault. Similar consideration should be given to [securing your VMs](https://azure.microsoft.com/services/virtual-machines/security/), [storage accounts](../storage/storage-security-guide.md) and other Azure resources too.

>[AZURE.NOTE] Note: This example shows how key vault access will be locked down in production. The developers should have their own subscription or resourcegroup where they have full permissions to manage their vaults, VMs and storage account where they develop the application.


## Resources

-   [Azure Active Directory Role-based Access Control](../active-directory/role-based-access-control-configure.md)

    This article explains the Azure Active Directory Role-based Access Control and how it works.

-   [RBAC: Built in Roles](../active-directory/role-based-access-built-in-roles.md)

    This article details all the built-in roles available in RBAC.

-   [Understanding Resource Manager deployment and classic deployment](../resource-manager-deployment-model.md)

    This article explains the Resource Manager deployment and classic deployment models, and explains the benefits of using the Resource Manager and resource groups

-    [Manage Role-Based Access Control with Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)

     This article explains how to manage role-based access control with Azure PowerShell

-   [Managing Role-Based Access Control with the REST API](../active-directory/role-based-access-control-manage-access-rest.md)

	This article shows how to use the REST API to manage RBAC.

-   [Role-Based Access Control for Microsoft Azure from Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    This is a link to a video on Channel 9 from the 2015 MS Ignite conference. In this session, they talk about access management and reporting capabilities in Azure, and explore best practices around securing access to Azure subscriptions using Azure Active Directory.

-   [Authorize access to web applications using OAuth 2.0 and Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)

    This article describes complete OAuth 2.0 flow for authenticating with Azure Active Directory.

-   [Key Vault Management REST APIs](https://msdn.microsoft.com/library/azure/mt620024.aspx)

	This document is the reference for the REST APIs to manage your key vault programmatically, including setting Key Vault access policy.

-   [Key Vault REST APIs](https://msdn.microsoft.com/library/azure/dn903609.aspx)

	Link to Key Vault REST API reference documentation.

-   [Key access control](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

    Link to Key access control reference documentation.

-   [Secret access control](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

    Link to Key access control reference documentation.

-   [Set](https://msdn.microsoft.com/library/mt603625.aspx) and [Remove](https://msdn.microsoft.com/library/mt619427.aspx) Key Vault access policy using PowerShell

    Links to reference documentation for PowerShell cmdlets to manage key vault access policy.

## Next Steps

For a getting started tutorial for an administrator, see [Get Started with Azure Key Vault](key-vault-get-started.md).

For more information about usage logging for Key Vault, see [Azure Key Vault Logging](key-vault-logging.md).

For more information about using keys and secrets with Azure Key Vault, see [About Keys and Secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx).

If you have questions about Key Vault, visit the [Azure Key Vault Forums](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
