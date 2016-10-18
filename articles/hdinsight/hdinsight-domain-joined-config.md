<properties
   	pageTitle="Configure Domain-joined HDInsight clusters| Microsoft Azure"
   	description="Learn how to set up and configure Domain-joined HDInsight clusters"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/12/2016"
   	ms.author="jgao"/>

# Configure Domain-joined HDInsight clusters

Learn how to set up an Azure HDInsight cluster with Azure Active Directory(Azure AD) and [Apache Ranger](http://hortonworks.com/apache/ranger/) to take advantage of strong authentication and rich role-based access control(RBAC) policies.  Domain-joined HDInsight can only be configured on Linux-based clusters. For more information, see [Introduce Domain-joined HDInsight clusters](hdinsight-domain-joined-introduction.md).

This article is the first tutorial of a series:

- Create an HDInsight cluster connected to Azure AD (via the Azure Directory Domain Services capability) with Apache Ranger enabled.
- Create and apply Hive policies through Apache Ranger, and allow users (for example, data scientists) to connect to Hive using ODBC-based tools, for example Excel, Tableau etc. Microsoft is working on adding other workloads, such as HBase, Spark, and Storm, to Domain-joined HDInsight soon.

An example of the final topology looks as follows:

![Domain-joined HDInsight topology](.\media\hdinsight-domain-joined-config\hdinsight-domain-joined-topology.png)

Because Azure AD currently only supports classic virtual networks (VNets) and Linux-based HDInsight clusters only support Azure Resource Manager based VNets, HDInsight Azure AD integration requires two VNets and a bridge between them. For the comparison information between the two deployment models, see [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../resource-manager-deployment-model.md)

Azure service names must be globally unique.  The following names are used in this tutorial. Contoso is a fictitious name. You need to replace *contoso* with a different name when you go through the tutorial.
	
**Names:**

- Azure AD VNet: contosoaadvnet
- Azure AD Virtual Machine (VM): contosoaadadmin. This VM is used to configure organization unit and reverse DNS zone.
- Azure AD directory: contosoaaddirectory
- Azure AD domain name: contoso158 (contoso158.onmicrosoft.com)

- HDInsight VNet: contosohdivnet
- HDInsight VNet resource group: contosohdirg
- HDInsight cluster: contosohdicluster

This tutorial provides the steps for configuring a domain-joined HDInsight cluster. Each section has links to other articles with more background information.

## Prerequisites:

- Familiarize yourself with [ADDS](https://azure.microsoft.com/services/active-directory-ds/) its [pricing](https://azure.microsoft.com/pricing/details/active-directory-ds/) structure.
- Ensure that your subscription is whitelisted for this public preview. You can do so by sending an email to hdipreview@microsoft.com with your subscription ID.
- [Azure PowerShell](../powershell-install-configure.md) on a Windows workstation. Most of the steps use the Azure portal or Azure classic portal. Only a few steps in this tutorial require Azure PowerShell. Azure PowerShell can only be installed on Windows workstations currently.

## Procedures

1. Create an Azure classic VNet for your Azure AD.  
2. Create and configure Azure Active Directory Domain Services.
3. Add a VM to the classic VNet for creating organizational unit . 
4. Create an organizational unit.
5. Create an HDInsight VNet in the Azure resource management mode.
6. Peer the two VNets.
7. Create an HDInsight cluster.

> [AZURE.NOTE] This tutorial does not assume that you have an Azure AD. If you have an Azure AD, you can skip step 2.

Step 3 to 7 can be done using an Azure PowerShell script, see [Configure Domain-joined HDInsight clusters using Azure PowerShell](hdinsight-domain-joined-config-powershell.md).
	
## Create an Azure classic VNet

In this section, you create a classic VNet using the Azure classic portal. In the next section, you enable the Azure Active Directory Domain Services for your Azure AD in the classic VNet. For additional information about the following procedure and using other VNet creation methods, see [Create a virtual network (classic) by using the Azure portal](../virtual-network/virtual-networks-create-vnet-classic-portal.md).

**To create a classic VNet**

1. Sign on to the [Azure classic portal](https://manage.windowsazure.com).
2. Click **New** > **Network Services** > **Virtual Network** > **Custom Create**.
3. Enter or select the following values:

	- **Name**: contosoaadvnet
	- **Location**: (Select a region. This is the region where you want to create your HDInsight cluster.)
	- **Subscription**: (Select a subscription. You will also use this subscription to create your HDInsight cluster.).
4. Click **Next**.
4. On the **DNS Servers and VPN Connectivity** page, click **Next**. If you do not specify a DNS server, the VNet uses the internal naming resolution provided by Azure. For this scenario, you don't need to configure DNS servers.
5. Configure **Address Space** with **10.1.0.0/16**, and **Subnet-1** with **10.1.0.0/24** as shown below:

	![Configure Domain-joined HDInsight clusters Azure Active directory virtual network](.\media\hdinsight-domain-joined-config\hdinsight-domain-joined-aad-vnet-setting.png)
	
6. Click **Complete** to create the VNet.

## Create and configure Azure Active Directory Domain Services for your Azure AD

In this section, you will:

1. Create an Azure AD.
2. Create Azure AD users. These are domain users. The first user will be used to configure the Azure AD.  The other two users are optional for this tutorial.  They will be used in [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md) when you configure Ranger policies.
3. Create the AAD DC Administrators group and add the Azure AD user to the group. You use this user to create the organizational unit.
4. Enable Azure AD Domain Service for the Azure AD.
5. Update the DNS setting for the VNet - Use the Azure AD domain controllers for domain name resolution.
6. Configure DNS for the VNet.
7. Configure LDAPS for the Azure AD. The Lightweight Directory Access Protocol (LDAP) is used to read from and write to Active Directory.

If you prefer to use an existing Azure AD, you can skip step 1.

**To create an Azure AD**

1. From the [Azure classic portal](https://manage.windowsazure.com), click **New** > **App Services** > **Active Directory** > **Directory** > **Custom Create**. 
3. Enter or select the following values:

	- **Name**: contosoaaddirectory
	- **Domain name**: contoso158.  This name must be globally unique. Replace the number in the name with a different number until it is validated successfully.
	- **Country or region**: Select your country or region.
4. Click **Complete**.


**Create an AAD user**

[jgao: use lower case for the usernames. Nitya has logged this bug.]

1. From the [Azure classic portal](https://manage.windowsazure.com), click **Active Directory** -> **contosoaaddirectory**. 
3. Click **Users** from the top menu.
4. Click **Add User**.
4. Enter **User Name**, and then click **Next**. 
5. Configure user profile; In **Role**, select **Global Admin**; and then click **Next**.  The Global Admin role is needed to create organizational units.
6. Click **Create** to get a temporary password.
7. Make a copy of the password - Zoma7815, and then click **Complete**. jgao@contoso158.onmicrosoft.com. Later in this tutorial, you will use this global admin user to sign on to the admin VM for creating an organization unit and configuring reverse DNS.


Follow the same procedure to create two more users with the **User** role. The following users will be used in [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md).

- hiveuser1, Luro0720
- hiveuser2, Kuxo9074
- hiveuser3, Xuwa9360
- hiveuser4, Duro5276


**To create the AAD DC Administrators' group, and add an Azure AD user**

1. From the [Azure classic portal](https://manage.windowsazure.com), click **Active Directory** > **contosoaaddirectory**. 
3. Click **Groups** from the top menu.
4. Click **Add a Group** or **Add Group**.
5. Enter or select the following values:

	- **Name**: AAD DC Administrators.  Don't change the group name.
	- **Group Type**: Security.
6. Click **Complete**.
7. Click **AAD DC Administrators** to open the group.
8. Click **Add Members**.
9. Select the first user you created in the previous step, for example jgao@contoso158.onmicrosoft.com, and then click **Complete**.

	- Members of this group will be granted administrative privileges on the domain-joined VM you will set up.	
	- After domain joined, this group will be added to the Administrators group on the domain-joined virtual machines.

10. Repeat the same steps to create another group called **HiveUsers**, and add the four Hiver users to the group.

For more information, see [Azure AD Domain Services (Preview) - Create the 'AAD DC Administrators' group](../active-directory-domain-services/active-directory-ds-getting-started.md).

**To enable Azure AD Domain service for your Azure AD**

1. From the [Azure classic portal](https://manage.windowsazure.com), click **Active Directory** > **contosoaaddirectory**. 
3. Click **Configure** from the top menu.
4. Scroll down to **Domain Services**, and set the following values:

	- **Enable domain services for this directory**: Yes.
	- **DNS domain name of domain services**: This shows the default DNS name of the Azure directory. For example, contoso158.onmicrosoft.com.
	- **Connect domain services to this virtual network**: Select the classic virtual network you created earlier, i.e. **contosoaadvnet**.
	
6. Click **Save** from the bottom of the page. You will see **Pending ...** next to **Enable domain services for this directory**.  
7. Wait until **Pending ...** disappears, and **IP Address** gets populated. Two IP addresses will get populated. These are the IP addresses of the domain controllers provisioned by Domain Services. Each IP address will be visible after the corresponding domain controller is provisioned and ready. Write down the two IP addresses. You will need them later.

For more information, see [Azure AD Domain Services (Preview) - Enable Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-enableaadds.md).

**To update DNS settings for the classic VNet** [jgao: Saurin requested to remove this procedure.  I like to confirm with him.]

1. From the [Azure classic portal](https://manage.windowsazure.com), click **Networks** > **contosoaadvnet**. 
3. Click **Configure** from the top menu.
4. In the **DNS Servers** section, enter:

	- "contosoaaddns1" - 10.1.0.4
	- "contosoaaddns2" - 10.1.0.5
5. Click **Save** from the bottom of the page.
6. Click **Yes** to confirm.	

For more information, see [Azure AD Domain Services (Preview) - Update DNS settings for the Azure virtual network](../active-directory-domain-services/active-directory-ds-getting-started-dns.md).

You can skip the next step in this tutorial. In your real implementation, you might need it: [jgao: Saurin requested to remove this procedure.  I like to confirm with him.]

- (Optional) [Enable password synchronization to AAD domain services for a cloud-only Azure AD directory](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).
	

**To configure LDAPS for the Azure AD**

1. Get a SSL certificate that is signed by a signing authority for your domain. If you don't have one, you can create a self-signed certificate using the following script from an elevated Windows PowerShell console. The self-signed certificate is not recommended to be used in production.

		$certFile = "c:\Tutorials\cert\SecureLdapCert.pfx" # this must be an existing folder
		$certPassword = "Pass@word123"
		$certName = "*.contoso158.onmicrosoft.com" # this must match the Azure AD DNS name

		$lifetime=Get-Date
		$cert = New-SelfSignedCertificate `
			-Subject $certName `
			-NotAfter $lifetime.AddDays(365) `
			-KeyUsage DigitalSignature, KeyEncipherment `
			-Type SSLServerAuthentication `
			-DnsName $certName 

		$certThumbprint = $cert.Thumbprint
		$cert = (Get-ChildItem -Path cert:\LocalMachine\My\$certThumbprint)

		$certPasswordSecureString = ConvertTo-SecureString $certPassword -AsPlainText -Force
		Export-PfxCertificate -Cert $cert -FilePath $certFile -Password $certPasswordSecureString

1. From the [Azure classic portal](https://manage.windowsazure.com), click **Active Directory** > **contosoaaddirectory**. 
3. Click **Configure** from the top menu.
4. Scroll to **domain services**.
5. Click **Configure certificate**.
6. Follow the instruction to specify the certificate file and the password. You will see **Pending ...** next to **Enable domain services for this directory**.  
7. Wait until **Pending ...** disappears, and **Secure LDAP Certificate** got populated.  This can take up 10 minutes or more.
 
Note – If some background tasks are being run on the Azure AD Domain service, you may see an error while uploading certificate - <i>There is an operation being performed for this tenant. Please try again later</i>.  In case you experience this error, please try again after some time. The second domain controller may take up to 3 hours to be provisioned.

For more information, see [Configure Secure LDAP (LDAPS) for an Azure AD Domain Services managed domain](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).


> [AZURE.NOTE] The rest of the procedures can be done automatically using an Azure PowerShell script.  See [Configure Domain-joined HDInsight clusters using Azure PowerShell](hdinsight-domain-joined-config-powershell.md).

## Configure an organizational unit

In this section, you add a virtual machine to the Azure AD VNet, and install administrative tools on this VM so you can configure Azure AD organizational unit.

**To create a virtual machine into the virtual network**

1. From the [Azure classic portal](https://manage.windowsazure.com), click **New** > **Compute** > **Virtual Machine** > **From Gallery**.
3. Select an image, and then click **Next**.  If you don't know which one to use, select the default, **Windows Server 2012 R2 Datacenter**.
4. Enter or select the following values:

	- Virtual Machine Name: **contosoaadadmin**
	- Tier: **Basic**
	- New User Name: **jgao**
	- Password: **Pass@word123**
	
	Please note the username and the password is the local admin.
	
5. Click **Next**
6. In **Region/Virtual Network**, select the new virtual network you created in the last step (contosoaadvnet), and then click **Next**.
7. Click **Complete**.

**To RDP to the VM**

1. From the [Azure classic portal](https://manage.windowsazure.com), click **Virtual Machines** > **contosoaadadmin**.
3. Click **Dashboard** from the top menu.
4. Click **Connect** from the bottom of the page.
5. Follow the instruction and use the local admin username and password to connect.

**To join VM to the Azure AD domain**

1. From the RDP session, click **Start**, and then click **Server Manager**.
2. Click **Local Server** from the left menu.
3. From Workgroup, click **Workgroup**.
4. Click **Change**.
5. Click **Domain**, enter **contoso158.onmicrosoft.com**, and then click **OK**.
6. Enter the domain user credentials, and then click **OK**.
7. Click **OK**.
8. Click **OK** to agree to reboot the computer.
9. Click **Close**.
10. Click **Restart Now**.

For more information, see [Join a Windows Server virtual machine to a managed domain](../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm.md).

**To install Active Directory administration tools and DNS tools**

1. RDP into **contosoaadadmin** using the Azure AD user account.
2. Click **Start**, and then click **Server Manager**.
3. Click **Dashboard** from the left menu.
4. Click **Manage**, and then click **Add roles and features**.
5. Click **Next**.
6. Select **Role-based or feature-based installation**, and then click **Next**.
7. Select the current virtual machine from the server pool, and click **Next**.
8. Click **Next** to skip roles.
9. Expand **Remote Server Administration Tools**, expand **Role Administration Tools**, select **AD DS and AD LDS Tools** and **DNS Server Tools**, and then click **Next**. 
10. Click **Next**
10. Click **Install**.

For more information, see [Install Active Directory administration tools on the virtual machine](../active-directory-domain-services/active-directory-ds-admin-guide-administer-domain.md#task-2---install-active-directory-administration-tools-on-the-virtual-machine).


**To configure reverse DNS**

1. RDP to contosoaadadmin using the Azure AD user account.
2. Click **Start**, click **Administrative Tools**, and then click **DNS**. 
3. Click **No** to skip adding ContosoAADAdmin.
4. Select **The following computer**, enter the IP address of the first DNS server you configured earlier, and then click **OK**.  The IP address should be 10.1.0.4. You shall see the DC/DNS is added to the left pane.
3. Expand the DC/DNS server, right-click **Reverse Lookup Zones**, and then click **New Zone**. The New Zone Wizard opens.
4. Click **Next**.
5. Select **Primary zone**, and then click **Next**.
6. Select **To all DNS servers running on domain controllers in this domain**, and then click **Next**.
6. Select **IPv4 Reverse Lookup Zone, and then click **Next**.
7. In **Network ID**, enter **10**, and then click **Next**.
8. Click **Next**.
9. Click **Next**.
10. Click **Finish**.



The organization unit you create next will be used when creating the HDInsight cluster. The Hadoop system users and computer accounts will be placed in this OU.

**Create an Organizational Unit (OU) on an Azure AD Domain Services managed domain**

1. RDP into **contosoaadadmin** using the domain account that is in the **AAD DC Administrators** group.
2. Click **Start**, click **Administrative Tools**, and then click **Active Directory Administrative Center**.
5. Click the domain name in the left pane. For example, contoso158.
6. Click **New** under the domain name in the **Task** pane, and then click **Organizational Unit**.
7. Enter a name, for example **HDInsightOU**, and then click **OK**. 


For more information, See [Create an Organizational Unit (OU) on an AAD Domain Services managed domain](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).


## Create an Azure Resource Manager virtual network for HDInsight cluster

In this section, you will create an Azure Resource Manager VNet that will be used for the HDInsight cluster. A Resource Manager template is used in this tutorial to create the VNet.  For more information on creating Azure VNET using other methods, see [Create a virtual network](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

After creating the VNet, you will configure the Resource Manager VNet to use the same DNS servers as for the Azure AD VNet. If you follow the steps in this tutorial to create the classic VNet and the Azure AD, the DNS servers are 10.1.0.4 and 10.1.0.5.

**To create a Resource Manager VNet**

1. Click the following image to open a Resource Manager template in the Azure portal. The Resource Manager template is located in a public blob container. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-vnet-for-domain-joined-hdinsight-clusters.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

	If you are interested in understanding the template, please pay attention to the following section in the cluster configuration:

		"securityProfile": {
			"directoryType": "ActiveDirectory",
			"domain": "[parameters('domainName')]",
			"organizationalUnitDN": "[parameters('organizationalUnitDN')]",
			"ldapsUrls": "[parameters('ldapUrls')]",
			"domainUsername": "[parameters('domainAdminUsername')]",
			"domainUserPassword": "[parameters('domainAdminPassword')]",
			"clusterUsersGroupDNs": "[parameters('clusterUsersGroupDNs')]"
		},

2. Enter or select the following values:

	- **Subscription**: (Select your Azure subscription.)
	- **Resource group**: contosohdirg
	- **Location**: (Select the same location as the Azure AD VNet, i.e. contosoaadvnet.)
	- **Cluster Name**: (Enter a cluster name)
    - **VNetName**:  contosohdivnet.
	- **VNetAddressPrefix**: 10.2.0.0/16
	- **SubNet1Prefix**: 10.2.0.0/24
	- **SubNetName**: Subnet1
	- **I agree to the terms and conditions stated above**: (select)
	- **Pin to dashboard**: (select)
6. Click **Purchase**. You will see a new tile titled **Submitting deployment for Template deployment**. 

**To configure DNS for the Resource Manager VNet**

1. From the [Azure portal](https://portal.azure.com), click **More services** -> **Virtual networks**. Ensure not to click **Virtual networks (classic)**.
2. Click **contosohdivnet**.
4. Click **DNS servers** from the left side of the new blade.
6. Click **Custom**, and then enter the following values:

	- 10.1.0.4
	- 10.1.0.5

	These DNS server IP addresses must match to the DNS servers in the Azure AD VNet (classic VNet).
7. Click **Save**.

























## Peer the Azure AD VNet and the HDInsight VNet

**To peer the two VNet**

1. Sign on to the [Azure portal](https://portal.azure.com).
2. Click **More services** from the left menu.
3. Click **Virtual Networks**. Don't click **Virtual networks (classic)**.
4. Click **contosohdivnet**.  This is the HDInsight VNet.
5. Click **Peerings** on the left menu of the blade.
6. Click **Add** from the top menu. It opens the **Add peering** blade.
7. On the **Add peering** blade, set or select the following values:

	- **Name**: ContosoAADHDIVNetPeering
	- **Virtual network deployment model**: Classic
	- **Subscription**: Select your subscription name used for the classic (Azure AD) vnet.
	- **Virtual network**: contosoaadvnet.
	- **Allow virtual network access**: (Check)
	- **Allow forwarded traffic**: (Check). Leave the other two checkboxes unchecked.

8. Click **OK**.



## Create HDInsight cluster


In this section, you will create a Linux-based Hadoop cluster in HDInsight using [Azure Resource Manager template](../resource-group-template-deploy.md). The Azure Resource Manager template experience is not required for following this tutorial. For other cluster creation methods and understanding the settings, see [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md). For more information about using Resource Manager template to create Hadoop clusters in HDInsight, see [Create Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**To create a Domain-joined HDInsight cluster using the portal**

1. Sign on to the [Azure portal](https://portal.azure.com).
2. Click **New**, **Intelligence + analytics**, **HDInsight**.
3. From the **Basic** blade, enter or select:

	- Cluster Name: Enter a name for the Domain-joined HDInsight cluster.
	- Subscription: Select a subscription used for creating this cluster.
	- Cluster Type: Select the tier, the type, and the version. Only Linux is supported for the Domain-joined HDInsight.
	- Cluster Login Username: Use the default cluster login username or enter a different one.
	- Cluster Login Password: Enter a password.
	- Resource Group: Select the Azure resource group that you created earlier in the tutorial.
	- Location: The location should be automatically popularted based on the resource group location.

4. Click **Next**.
5. From the **Storage Options** blade, specify an existing Azure storage account or a new storage account.
6. Click **Next**.
7. Click the **Edit** next to **Advanced settings**, and specify the virtual network you created earlier.
8. Click **Next** to save the changes.
9. Click **Create**.

If you prefer to use an Azure Resource Group template instead of the portal, use the following procedure:

**To create a Domain-joined HDInsight cluster**

1. Click the following image to open a Resource Manager template in the Azure portal. The Resource Manager template is located in a public blob container. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. From the **Parameters** blade, enter the following values:

	- Subscription: (Select your Azure subscription).
	- Resource group: Click **Use existing**, and specify the same resource group you have been using.  For example contosohdirg. 

	- **ClusterVNetID**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>
	- **ClusterVNetSubNetID**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>/subnets/Subnet1
    - **ClusterName**: Enter a name for the Hadoop cluster that you will create. For example contosohdicluster
    - **Cluster login name and password**: The default login name is **admin**.
    - **SSH username and password**: The default username is **sshuser**.  You can rename it. 

	- DomainName: contoso158.onmicrosoft.com
	- OrganizationUnitDN: OU=Hadoop System Users,DC=contoso158,DC=onmicrosoft,DC=com
	- LDAPUrls: ["ldaps://40.84.54.252:636"], ["ldaps://10.1.0.4:636","ldaps://10.1.0.5:636"]
	- DomainAdminUserName: jgao@contoso158.onmicrosoft.com
	- DomainAdminPassword: Zoma7815

	- **I agree to the terms and conditions stated above**: (Check)
	- **Pin to dashboard**: (Check)

	The organizational unit is what you created earlier in the article.

    Other properties have been hard-coded into the template. For example:
    
    - Location: East US 2
    - Cluster worker node count: 2
    - Default storage account: <Cluster Name>store
    - Virtual network name: <Cluster Name>-vnet
    - Virtual network address space: 10.2.0.0/16
    - Subnet name: default
    - Subnet address range: 10.2.0.0/24


6. Click **Purchase**. You will see a new tile titled **Deploying Template deployment**. It takes about around 20 minutes to create a cluster. Once the cluster is created, you can click the cluster blade in the portal to open it.

After you complete the tutorial, you might want to delete the cluster. With HDInsight, your data is stored in Azure Storage, so you can safely delete a cluster when it is not in use. You are also charged for an HDInsight cluster, even when it is not in use. Since the charges for the cluster are many times more than the charges for storage, it makes economic sense to delete clusters when they are not in use. For the instructions of deleting a cluster, see [Manage Hadoop clusters in HDInsight by using the Azure portal](hdinsight-administer-use-management-portal.md#delete-clusters).


## Test the connection between the two VNets

[This part only tests the network connectivity and domain name resolution. Do I need to add another procedure for validating the AAD configuration, for example, verify domain users are populated when creating a Ranger policy.]

To test the connection between the two VNets, you will ping one of the cluster nodes from the Windows VM in the Classic VNet.

**To find the cluster node IP addresses**

1. From the [Azure portal](https://portal.azure.com), open the cluster. For example: contosohdicluster.
3. Click **Dashboard**.
4. Sign in to Ambari using the Hadoop HTTP username and password.
5. Click **Hosts** from the top. You will see a list of the Hadoop nodes.  Write down the IP address and the domain name of one of the nodes.

**To ping a cluster node**

1. RDP into **contosoaadadmin** using the domain account that is in the **AAD DC Administrators** group.
2. Click **Start**, and then click **Windows PowerShell**.
3. Ping the IP address and the domain name you wrote down in the last procedure.




## Next steps:

- [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md)
- [Configure Domain-joined HDInsight clusters using Azure PowerShel](hdinsight-domain-joined-config-powershell.md)


