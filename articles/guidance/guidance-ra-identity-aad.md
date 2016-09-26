<properties
   pageTitle="Implementing Azure Active Directory | Microsoft Azure"
   description="How to implement a secure hybrid network architecture using Azure Active Directory."
   services="guidance,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="telmos"/>

# Implementing Azure Active Directory

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

This article describes best practices for integrating on-premises Active Directory (AD) domains and forests with Azure Active Directory to provide cloud-based identity authentication.

> [AZURE.NOTE] Azure has two different deployment models: [Resource Manager][resource-manager-overview] and classic. This reference architecture uses Resource Manager, which Microsoft recommends for new deployments.

You can use directory and identity services, such as those provided by AD Directory Services (AD DS) to authenticate identities. These identities can belong to users, computers, applications, or other resources that form part of a security domain. You can host directory and identity services on-premises, but in a hybrid scenario where elements of an application are located in Azure it can be more efficient to extend this functionality into the cloud. This approach can help  reduce the latency caused by sending authentication and local authorization requests from the cloud back to the directory and identity services running on-premises.

Azure provides two solutions for implementing directory and identity services in the cloud:

1. You can use [Azure Active Directory (AAD)][azure-active-directory] to create a new AD domain in the cloud and link it to an on-premises AD domain. AAD enables you to configure single sign-on (SSO) for users running applications accessed through the cloud. AAD utilizes [Azure AD Connect][azure-ad-connect] running on-premises to replicate objects and identities held in the the on-premises repository to the cloud.

	You can also implement AAD without using an on-premises directory. In this case, AAD acts as the primary source of all identity information rather than containing data replicated from an on-premises directory.

	Note that the directory in the cloud is **not** an extension of an on-premises directory. Rather, it's a copy that contains the same objects and identities. Changes made to these items on-premises will be copied to the cloud, but changes made in the cloud **will not** be replicated back to the on-premises domain.

	>[AZURE.NOTE] The Azure Active Directory Premium editions support write-back of users' passwords, enabling your on-premises users to perform self-service password resets in the cloud. This is the only information that AAD will synchronize back to the on-premises directory. For more information about the different editions of AAD and the features that each provides, see [Azure Active Directory Editions][aad-editions].

2. You can deploy a VM running AD DS as a domain controller in Azure, extending your existing AD infrastructure (including AD DS and AD FS) from your on-premises datacenter. This approach is more common for scenarios where the on-premises network and Azure virtual network are connected by a VPN and/or ExpressRoute connection. This solution also supports bi-directional replication enabling you make changes in the cloud and on-premises, wherever it is most appropriate.

This architecture focuses on solution 1. For more information about the second solution, see [Federating with a customer's AD FS for multitenant apps in Azure][adfs-multitenant].

Typical use cases for this architecture include:

- Providing SSO for end users running SaaS and other applications in the cloud, using the same credentials that they specify for on-premises applications.

- Publishing on-premises web applications through the cloud to provide access to remote users who belong to your organization.

- Implementing self-service capabilities for end-users, such as resetting their passwords, and delegating group management. 

	>[AZURE.NOTE] These capabilities can be controlled by an administrator through group policy.

- Situations where the on-premises network and Azure virtual network hosting cloud applications are not directly linked by using a VPN tunnel or ExpressRoute circuit.

You should note that AAD does not provide all the functionality of AD. For example, AAD currently only handles user authentication rather than computer authentication. Some applications and services, such as SQL Server, may require computer authentication in which case this solution is not appropriate. Additionally, AAD might not be suitable for systems where components could migrate across the on-premises/cloud boundary rather than just being copied.

> [AZURE.NOTE] For a detailed explanation of how Azure Active Directory works, watch [Microsoft Active Directory Explained][aad-explained].

## Architecture diagram

The following diagram highlights the important components in this architecture (*click to zoom in*). For more information about the grayed-out elements, read [Running VMs for an N-tier architecture on Azure][implementing-a-multi-tier-architecture-on-Azure]:

> [AZURE.NOTE] For simplicity, this diagram only shows the connections directly related to AAD, and does not depict web browser request redirects or other protocol related traffic that may occur as part of the authentication and identity federation process. For example, a user (on-premises or remote) will typically access a web app through a browser, and the web app may transparently redirect the web browser to authenticate the request through AAD. Once authenticated, the request can be passed back to the web app together with the appropriate identity information.

[![0]][0]

- **Azure Active Directory tenant**. This is an instance of AAD created by your organization. It acts as a simple directory service for cloud applications (it holds objects copied from the on-premises AD), and provides identity services.

- **Web tier subnet**. This subnet holds VMs that implement a custom cloud-based application developed by your organization and for which AAD can act as an identity broker.

- **On-premises web apps**. These are web apps hosted in your organizations on-premises network. AAD can provide remote access to these web apps through the **Azure AD application proxy** which runs in the cloud. Your organization must run a **application proxy connector** on-premises to expose the web app to AAD.

	>[AZURE.NOTE] The application proxy connector opens an outbound network connection to the Azure AD application proxy. Remote users requests are routed back from AAD through this connection to the web apps. This mechanism removes the need to open inbound ports in the on-premises firewall, reducing the attack surface exposed by your organization.

- **On-premises AD DS server**. Your organization will likely already have an existing directory service such as AD DS. You can synchronize many of the items in your AD DS directory (such as user and group information) with AAD, to enable AAD to use this information to authenticate identities.

- **Azure AD Connect sync server**. This is an on-premises computer that runs the Azure AD Connect sync service. You install this service by using the Azure AD Connect software. The Azure AD Connect sync service synchronizes information (Users, Groups, Contacts, etc) held in the on-premises AD to AAD in the cloud. For example, you can provision or deprovision groups and users on-premises and these changes will be propagated to AAD. The Azure AD Connect sync service passes information to the AAD tenant.

	>[AZURE.NOTE] For security reasons, user's passwords are not stored directly in AAD. Rather, AAD holds a hash each password; this is sufficient to verify a user's password. If a user requires a password reset, this must be performed on-premises and the new hash sent to AAD. AAD Premium editions include features that can automate this task to enable users to reset their own passwords.

## Recommendations

This section summarizes recommendations for implementing AAD.

### Azure AD Connect sync service recommendations

Synchronization is concerned with ensuring that users identity information in the cloud is consistent with that held on premises. The purpose of the Azure AD Connect sync service is to maintain this consistency. The following points summarize recommendations for implementing the Azure AD Connect sync service:

- Prior to implementing Azure AD Connect sync, you should determine the synchronization requirements of your organization (what to synchronize, from which domains, and how frequently. The article [Determine directory synchronization requirements][aad-sync-requirements] describes the points that you should consider.

- You can run the Azure AD Connect sync service using a VM or a computer hosted on-premises. Depending on the volatility of the information in your AD directory, once the initial synchronization with AAD has been performed the load on the Azure AD Connect sync service is unlikely to be high. Using a VM enables you to more easily scale the server (monitor the activity as described in the [Monitoring considerations](#monitoring-considerations) section to determine whether this is necessary).

- If you have multiple on-premises domains in a forest, you can store and synchronize information for the entire forest to a single AAD tenant (this is the recommended approach). Filter information for identities that occur in more than one domain so that each identity appears only once in AAD rather then being duplicated as this can lead to inconsistencies when data is synchronized. This approach requires implementing multiple Azure AD Connect sync servers. For more information, see the Multiple AAD scenario in the [Topology considerations](#topology-considerations) section. 

- Use filtering to limit the data stored in AAD to only that which is necessary. For example, your organization might not want to store information about inactive or non-personal accounts in AAD. Filtering can be group-based, domain-based, OU-based, or attribute-based, and you can combine filters to generate more complex rules. For example, you could select to synchronize only objects held in a domain that have a specific value in a selected attribute. For detailed information, see [Azure AD Connect sync: Configure Filtering][aad-filtering].

- To implement high availability for the AD Connect sync service, run a secondary staging server. For more information, see the [Topology considerations](#topology-considerations) section.

### Security recommendations

The items below summarize the primary security recommendations for implementing AAD, depending on the requirements of your organization:

- Managing users' passwords.

	If you are using a premium edition of AAD, you can enable password write-back from AAD to your on-premises directory by performing a custom installation of Azure AD Connect:

	[![9]][9]

	This feature enables users to reset their own passwords from within the Azure portal, but should only be enabled after reviewing your organization's password security policy. For example, you can restrict which users can change their passwords, and you can tailor the password management experience. For more information, see [Customizing Password Management to fit your organization's needs][aad-password-management].

- Maintaining protection for on-premises applications that can be accessed externally.

	Use the Azure AD Application Proxy to provide controlled access to on-premises web applications from external users through AAD. This approach protects your applications by not exposing them directly to the Internet. Only users that have valid credentials in you Azure directory will be able to reacch your applications. For more information, see the article [Enable Application Proxy in the Azure portal][aad-application-proxy].

- Actively monitoring AAD for signs of suspicious activity.

	Consider using AAD Premium P2 edition, which includes AAD Identity Protection. Identity Protection uses adaptive machine learning algorithms and heuristics to detect anomalies and risk events that may indicate that an identity has been compromised. For example, it can detect potentially anomalous activity such as irregular sign-in activities, sign-ins from unknown sources or from IP addresses with suspicious activity, or sign-ins from devices that may be infected. Using this data, Identity Protection generates reports and alerts that enables you to investigate these risk events and take appropriate remediation or mitigation action. For more information, see [Azure Active Directory Identity Protection][aad-identity-protection].

	You can also use the reporting feature of AAD in the Azure portal to monitor suspicious and other security-related activities occurring within your system. AAD can generate a series of summary reports:

	[![17]][17]

	For more information about using these reports, see [Azure Active Directory Reporting Guide][aad-reporting-guide].

## Topology considerations

If you are integrating an on-premises directory with AAD, configure Azure AD Connect to implement a topology that most closely matches the requirements of your organization. Topologies that Azure AD Connect supports include the following:

- **Single forest, single AAD directory**. In this topology you use Azure AD Connect to synchronize objects and identity information in one or more domains in a single on-premises forest with a single AAD tenant. This is the default topology implemented by the express installation of Azure AD Connect.

	[![1]][1]

	Don't create multiple Azure AD Connect sync servers to connect different domains in the same on-premises forest to the same AAD tenant unless you are running an Azure AD Connect sync server in staging mode (see Staging Server below).

- **Multiple forests, single AAD directory**. Use this topology if you have more than one on-premises forest. You can consolidate identity information so that each unique user is represented once in the AAD directory, even if the same user exists in more than one forest. All forests use the same Azure AD Connect sync server. The Azure AD Connect sync server does not have to be part of any domain, but it must be reachable from all forests:

	[![2]][2]

	In this topology, don't use separate Azure AD Connect sync servers to connect each on-premises forest to a single AAD tenant. This can result in duplicated identity information in AAD if users are present in more than one forest.

- **Multiple forests, separate topologies**. This approach enables you to merge identity information from separate forests into a single AAD tenant. This strategy is useful if you are combining forests from different organizations (after a takeover, for example), and the identity information for each user is held in only one forest:

	If the GALs in each forest are synchronized, then a user in one forest may be present in another as a contact. This can occur if, for example, your organization has implemented GALSync with Forefront Identity manager 2010 or Microsoft Identity Manager 2016. In this scenario, you can specify that users should be identified by their *Mail* attribute. You can also match identities using the *ObjectSID* and *msExchMasterAccountSID* attributes; this is useful if you have one or more resource forests with disabled accounts.

- **Staging server**. In this configuration, you run a second instance of the Azure AD Connect sync server in parallel with the first. This structure supports scenarios such as:

	- High availability.

	- Testing and deploying a new configuration of the Azure AD Connect sync server.

	- Introducing a new server and decommissioning an old configuration. 

	In these scenarios, the second instance runs in *staging mode*. The server records imported objects and synchronization data in its database, but does not pass the data to AAD. Only when you disable staging mode does the server start writing data to AAD, and also starts performing password write-backs into the on-premises directories where appropriate:

	[![4]][4]

	For more information, see [Azure AD Connect sync: Operational tasks and considerations][aad-connect-sync-operational-tasks].

- **Multiple AAD directories**. It is recommended that you create a single AAD directory for an organization, but there may be situations where you need to partition information across separate AAD directories. In this case, you should ensure that each object from the on-premises forest appears only in one AAD directory, to avoid synchronization and password write-back issues.

	To implement this scenario, configure separate Azure AD Connect sync servers for each AAD directory, and use filtering so each Azure AD Connect sync server operates on a mutually exclusive set of objects: 

	[![5]][5]

For more information about these topologies, see [Topologies for Azure AD Connect][aad-topologies].

## User sign-in considerations

By default, the AAD service assumes that users will log in by providing the same password that they use on-premises, and the Azure AD Connect sync server configures password synchronization between the on-premises domain and AAD. For many organizations, this is appropriate, but you should consider your organization's existing policies and infrastructure. For example:

- The security policy of your organization might prohibit synchronizing password hashes to the cloud.

- You might require that users experience seamless SSO (without additional password prompts) when accessing cloud resources from domain joined machines on the corporate network.

- Your organization might already have AD FS or a 3rd party federation provider deployed. You can configure AAD to use this infrastructure to implement authentication and SSO rather than by using password information held in the cloud.

For more information, see [Azure AD Connect User Sign on options][aad-user-sign-in].

## Object synchronization considerations

The default configuration of Azure AD Connect synchronizes objects from your local AD directory based on the set of rules specified in the article [Azure AD Connect sync: Understanding the default configuration][aad-connect-sync-default-rules]. Only objects that satisfy these rules are synchronized, others are ignored. For example, User objects must have a unique *sourceAnchor* attribute and the *accounEnabled* attribute must be populated. User Objects that do not have a *sAMAccountName* attribute or that start with the text *AAD_* or *MSOL_* are not synchronized. Azure AD Connect applies many other rules to User objects, as well as to Contact, Group, ForeignSecurityPrincipal, and Computer objects. If you need to modify the default set of rules, use the Synchronization Rules Editor installed with Azure AD Connect (also documented in [Azure AD Connect sync: Understanding the default configuration][aad-connect-sync-default-rules]).

You can filter by domain or OU to limit the objects to be synchronized by performing a custom installation of Azure AD Connect:

[![8]][8]

You can also implement complex custom filtering, as described in the article [Azure AD Connect sync: Configure Filtering][aad-filtering].

## Security considerations

Use conditional access control to deny authentication requests from unexpected sources:

- Trigger [Azure Multi-Factor Authentication (MFA)][azure-multifactor-authentication] if a user attempts to connect from a non-trusted location (such as across the Internet) rather than a trusted network.

- Use the device platform type of the user (iOS, Android, Windows Mobile, Windows) to determine access policy to applications and features.

- Record the enabled/disabled state of users' devices, and incorporate this information into the access policy checks. For example, if a user's phone is lost or stolen it should be recorded as disabled to prevent it from being used to gain access.

- Control the level of access to a user based on group membership. Use [AAD dynamic membership rules][aad-dynamic-membership-rules] to simplify group administration. For a brief overview of how this works, see [Introduction to Dynamic Memberships for Groups][aad-dynamic-memberships].

- Use conditional access risk policies with AAD Identity Protection to provide advanced protection based on unusual sign-in activities or other events.

For more information, see [Azure Active Directory conditional access][aad-conditional-access].

## Scalability considerations

Scalability is addressed by the AAD service and the configuration of the Azure AD Connect sync server:

- For the AAD service, you do not have to configure any options to implement scalability. The AAD service supports scalability based on replicas. AAD implements a single primary replica which handles write operations, and multiple read-only secondary replicas. AAD transparently redirects attempted writes made against secondary replicas to the primary replica. AAD provides eventual consistency; all changes made to the primary replica will be propagated to the secondary replicas. As most operations against AAD will be reads rather than writes, this architecture scales well.

	For more information, see [Azure AD: Under the hood of our geo-redundant, highly available, distributed cloud directory][aad-scalability].

- For the Azure AD Connect sync server, you should determine how many objects you are likely to synchronize from your local directory. If you have less then 100,000 objects you can use the default SQL Server Express LocalDB software provided with Azure AD Connect. If you have a larger number of objects, you should install a production version of SQL Server and perform a custom installation of the Azure AD Connect specifying that it should use an existing instance of SQL Server:

	[![6]][6]

## Availability considerations

As with scalability concerns, availability spans the AAD service and the configuration of Azure AD Connect:

- The AAD service is designed to provide high availability. There are no user-configurable availability options. It is geo-distributed and runs in multiple data centers spread around the world, with automated failover. If a data center becomes unavailable, AAD ensures that your directory data is available for instance access in at least two more regionally dispersed data centers.

	>[AZURE.NOTE] The SLA for AAD Basic and Premium services guarantee at least 99.9% availability. There is no SLA for the Free tier of AAD. For more information, see [SLA for Azure Active Directory][sla-aad].

- To increase the availability of the Azure AD Connect sync server you can run a second instance in staging mode, as described in the [Topology considerations](#topology-considerations) section. 

	Additionally, if you are not using the SQL Server Express LocalDB instance that comes with Azure AD Connect, then you should consider high availability for SQL Server. Note that the only high availability solution supported is SQL clustering; solutions such as mirroring and Always On are not supported by Azure AD Connect.

	For additional considerations about maintaining the availaibility of the Azure AD Connect sync server, and how to recover after a failure, see [Azure AD Connect sync: Operational tasks and considerations - Disaster Recovery][aad-sync-disaster-recovery].

## Management considerations

There are two aspects to managing AAD:

1. Administering AAD in the cloud.

2. Maintaining the Azure AD Connect sync servers.

AAD provides the following options for managing domains and directories in the cloud:

- [Azure Active Directory PowerShell Module][aad-powershell]. Use this module if you need to script common Azure AD administrative tasks such as user management, domain management and for configuring single sign-on.

- Azure AD management blade in the Azure portal. This blade provides an interactive management view of the directory, and enables you to control and configure most aspects of AAD:

	[![10]][10]

Azure AD Connect installs the following tools that you use to maintain the Azure AD Connect sync services from your on-premises machines:

- The Microsoft Azure Active Directory Connect console. This tool enables you to modify the configuration of the Azure AD Sync server, customize the way in which synchronization occurs, enable or disable staging mode, and switch the user sign-in mode (you can enable AD FS sign-in using your on-premises infrastructure):

	[![11]][11]

- The Synchronization Service Manager. Use the *Operations* tab in this tool to manage the synchronization process and detect whether any parts of the process have failed. You can trigger synchronizations manually using this tool. 

	[![12]][12]

	The *Connectors* tab enables you to control the connections for the domains (on-premises and in the cloud) to which the synchronization engine is attached:

	[![13]][13]

-  The Synchonization Rules Editor. Use this tool to customize the way in which objects are transformed when they are copied between an on-premises directory and AAD in the cloud. This tool enables you to specify additional attributes and objects for synchronization, and filters to determine which instances should or should not be synchronized.

	[![14]][14]

	For more information, see the Synchronization Rule Editor section in the document [Azure AD Connect sync: Understanding the default configuration][aad-connect-sync-default-rules].

The page [Azure AD Connect sync: Best practices for changing the default configuration][aad-sync-best-practices] contains additional information and tips for managing Azure AD Connect.

## Monitoring considerations

Health monitoring is performed by using a series of agents installed on-premises:

- Azure AD Connect installs an agent that captures information about synchronizations. Use the Azure Active Directory Connect Health blade in the Azure portal to monitor the health and performance of Azure AD Connect:

	[![15]][15]

	For more information, see [Using Azure AD Connect Health for sync][aad-health].

- To monitor the health of the AD DS domains and directories from Azure, install the Azure AD Connect Health for AD DS agent on a machine within the on-premises domain. Use the Azure Active Directory Connect Health blade in the Azure portal to monitor AD DS:

	[![16]][16]

	For more information, see [Using Azure AD Connect Health with AD DS][aad-health-adds]

- Install the Azure AD Connect Health for AD FS agent to monitor the health of AD FS running on on-premises, and use the the Azure Active Directory Connect Health blade in the Azure portal to monitor AD DS.

	For more information, see [Using Azure AD Connect Health with AD FS][aad-health-adfs]

For additional information on installing the AD Connect Health agents and their requirements, see [Azure AD Connect Health Agent Installation][aad-agent-installation].

## Sample solution

This section documents the steps for building a sample solution that you can use to test the configuration of AAD. The solution comprises the following elements:

- An n-tier web application, following the structure described by [Running VMs for an N-tier architecture on Azure][implementing-a-multi-tier-architecture-on-Azure].

- A test client machine; we recommend using another VM for this computer. The configuration of this VM is unimportant, as long as you have administrator access and it can connect to the n-tier web application.

- A simulated on-premises environment that contains its own domain built using AD DS.

The scenarios that these steps demonstrate are:

- Enabling access to the n-tier web application running in the cloud to external users, with AAD providing password authentication.

- Enabling access to the n-tier web application running in the cloud to users running within the organization, with AAD providing password authentication and SSO.

### Prerequisites

The steps that follow assume the following prerequisites:

- You have an existing Azure subscription in which you can create resource groups.

- You have installed the [Azure Command-Line Interface][azure-cli].

- You have downloaded and installed the most recent build. See [here][azure-powershell-download] for instructions.

- You have installed a copy of the [makecert][makecert] utility on the test client computer.

- You have access to a development computer that has Visual Studio installed.

### Create the n-tier web application architecture

1. Create a folder named `Scripts` that contains a subfolder named `Parameters`.

2. Download the [Deploy-ReferenceArchitecture.ps1][solution-script] PowerShell script to the Scripts folder.

3. In the Parameters folder, create another subfolder named Windows.

4. Download the following files to Parameters/Windows folder:

    - [virtualNetwork.parameters.json][vnet-parameters-windows]

    - [networkSecurityGroup.parameters.json][nsg-parameters-windows]

    - [webTierParameters.json][webtier-parameters-windows]

    - [businessTierParameters.json][businesstier-parameters-windows]

    - [dataTierParameters.json][datatier-parameters-windows]

    - [managementTierParameters.json][managementtier-parameters-windows]

5. Edit the Deploy-ReferenceArchitecture.ps1 file in the Scripts folder, and change the following line to specify the resource group that should be created or used to hold the VM and resources created by the script:

    ```powershell
    # PowerShell
    $resourceGroupName = "ra-aad-ntier-rg"
    ```

6. Edit the following files in the Parameters/Windows folder and set the `resourceGroup` value in the `virtualNetworkSettings` section in each of these files to be the same as that you specified in the Deploy-ReferenceArchitecture.ps1 script file.

    - networkSecurityGroup.parameters.json

    - webTierParameters.json

    - businessTierParameters.json

    - dataTierParameters.json

    - managementTierParameters.json

7. Open an Azure PowerShell window, move to the Scripts folder, and run the following command:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows ntier
    ```

    Replace `<subscription id>` with your Azure subscription ID.

    For `<location>`, specify an Azure region, such as `eastus` or `westus`.

8. When the script has completed, use the Azure portal to obtain the public IP address of the web-tier load balancer (*ra-aad-ntier-web-lb*):

	[![18]][18]

9. Log in to your test client computer (or VM), and verify that you can access the web-tier by using Internet Explorer to browse to the public IP address of the web-tier load balancer. The default IIS page should appear:

	[![19]][19]

### Simulate configuration of a public web site

>[AZURE.NOTE] The following steps simulate associating the web-tier with the DNS name www.contoso.com by modifying the hosts file on the test client computer. Additionally, the web-tier VMs are configured with self-signed certificates and a fake hosting authority. This is for testing purposes only and **you should not use these techniques in a production environment**.

1. On your test client computer, edit the file C:\Windows\System32\drivers\etc\hosts by using Notepad, and add an entry that associates the name www.contoso.com with the public IP address of the web-tier load balancer:

	```text
	# Copyright (c) 1993-2009 Microsoft Corp.
	#
	# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
	#
	# This file contains the mappings of IP addresses to host names. Each
	# entry should be kept on an individual line. The IP address should
	# be placed in the first column followed by the corresponding host name.
	# The IP address and the host name should be separated by at least one
	# space.
	#
	# Additionally, comments (such as these) may be inserted on individual
	# lines or following the machine name denoted by a '#' symbol.
	#
	# For example:
	#
	#      102.54.94.97     rhino.acme.com          # source server
	#       38.25.63.10     x.acme.com              # x client host
	
	# localhost name resolution is handled within DNS itself.
	#	127.0.0.1       localhost
	#	::1             localhost
	
	52.165.38.64	www.contoso.com
	```

2. Verify that you can now browse to www.contoso.com from the test client computer. The same default IIS page should appear as before.

3. On the test client computer, open a command prompt as Administrator and use the makecert utility to create a fake root certification authority certificate:

	```
	makecert -sky exchange -pe -a sha256 -n "CN=MyFakeRootCertificateAuthority" -r -sv MyFakeRootCertificateAuthority.pvk MyFakeRootCertificateAuthority.cer -len 2048
	```

	Verify that the following files are created:

	- MyFakeRootCertificateAuthority.cer

	- MyFakeRootCertificateAuthority.pvk

4. Run the following command to install the fake root certification authority authority:

	```
	certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
	```

5. Use the fake certification authority to generate a certificate for www.contoso.com:

	```
	makecert -sk pkey -iv MyFakeRootCertificateAuthority.pvk -a sha256 -n "CN=www.contoso.com" -ic MyFakeRootCertificateAuthority.cer -sr localmachine -ss my -sky exchange -pe
	```
6. Run the `mmc` command, and add the Certificates snap-in for the computer account for the local computer.

7. In the */Certificates (Local Computer)/Personal/Certificate/* store, export the www.contoso.com certificate with its private key to a file named www.contoso.com.pfx:

	[![20]][20]

8. Return to the Azure portal and connect to the management tier VM (*ra-aad-ntier-mgmt-vm1*). The default user name is *testuser* with password *AweS0me@PW*:

	[![21]][21]
	
9. From the management tier VM, connect to each of the web tier VMs in turn with username *testuser* and password *AweS0me@PW*, and perform the following tasks. Note that the VMs have the private addresses IP 10.0.1.4, 10.0.1.5, and 10.0.1.6:

	>[AZURE.NOTE] The web tier VMs only have private IP addresses. You can only connect to them by using the management tier VM.

	1. Copy the files *www.contoso.com.pfx* and *MyFakeRootCertificateAuthority.cer* from the test client computer.
	
	2. Open a command prompt as Administrator, move to the folder holding the files that you just copied, and run the following commands:
	
		```
		certutil.exe -privatekey -importPFX my www.contoso.com.pfx NoExport

		certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
		```

	3. Run the `mmc` command, add the Certificates snap-in for the computer account for the local computer, and verify that the following certificates have been installed:

		- \Certificates (Local Computer)\Personal\Certificates\www.contoso.com

		-  \Certificates (Local Computer)\Trusted Root Certification Authorities\Certificates\MyFakeRootCertificateAuthority

	4. Start the Internet Information Services (IIS) Manager console and navigate to *Sites\Default Web Site* on the computer.

	5. In the *Actions* pane, click *Bindings*, and add an https binding using the www.contoso.com SSL certificate:

		[![22]][22]

10. Return to the test client computer and verify that you can now browse to https://www.contoso.com.

### Create an Azure Active Directory tenant

1. Using the Azure portal, create a new Azure Active Directory tenant such as *myaadname*.onmicrosoft.com, where *myaadname* is a directory name selected by you.

2. Add a user named *admin* with the GlobalAdmin role to the directory. Make a note of the newly generated password.

3. Using Internet Explorer, browse to https://account.activedirectory.windowsazure.com/ and log in as admin@*myaadname*.onmicrosoft.com. Change your password when prompted.

### Create and deploy a test web application

1. Using Visual Studio on the development computer, create a new ASP.NET Web application named ContosoWebApp1 (use the .NET Framework 4.5.2):

	[![23]][23]

2. Select the *MVC* template, change the authentication to *Work and School accounts*, and specify the name of your AAD tenant. Don't create an App Service in the cloud.

	[![24]][24]

3. Build and run the application to test the authentication. In the sign-in page enter the account name admin@*myaadname*.onmicrosoft.com, provide the password, and then click *Sign in*:

	[![25]][25]

4. Verify that AAD asks for permission to sign you in and read your profile, and then starts running the web application with the identity of Admin.

5. Close Internet Explorer and return to Visual Studio.

6. Open the web.config file, and in the `<appSettings>` section, change the value of the *ida:PostLogoutRedirectUri* key to *https://www.contoso.com:443/*. Save the file.

7. In the *Solution Explorer* window, right-click the ContosoWebApp1 project, click *Publish*.

8. In the *Publish Web* window, click *Custom*. Create a new custom profile named *ContosoWebApp1*.

9. On the *Connection* page, set the *Publish method* to *File System* and specify a folder named *ContosoWebApp*, located in a convenient location on your development computer.

10. On the *Settings* page, set the *Configuration* to *Release*.

11. On the *Preview* page, click *Publish*.

12. Connect to each web server in turn (via the management tier VM) and perform the following tasks:

	1. Copy the *ContosoWebApp* folder and its contents from the development computer to the *C:\inetpub* folder.

	2. Using the Internet Information Services (IIS) Manager console, navigate to *Sites\Default Web Site* on the computer.

	3. In the *Actions* pane, click *Basis Settings*, and change the physical path of the web site to *%SystemDrive%\inetpub\ContosoWebApp*:

		[![26]][26]

### Publish the test web application through AAD

1. Login to the Azure portal and navigate to your AAD directory.

2. On the *Applications* tab, click the ContosoWebApp1 application.

3. Verify that your application is successfully added to the directory, and then click the *CONFIGURE* tab.

4. Change the *SIGN-ON URL* to https://www.contoso.com:443, and set the *REPLY URL* to https://www.contoso.com:443 (the same URL).

5. Save the configuration.

6. On the test client computer, navigate to https://www.contoso.com. Verify that AAD prompts you for your credentials, and then log in.

>[AZURE.NOTE] This is the endpoint for the first scenario: Enabling access to the n-tier web application running in the cloud to external users, with AAD providing password authentication.

### Create a simulated on-premises environment with Active Directory

1. In File Explorer, return to the Scripts folder containing the script used to create the N-tier web application.

2. In the Parameters folder, add another sub folder named `Onpremise`.

3. Download the following files to Parameters/Onpremise folder:

    - [add-adds-domain-controller.parameters.json][add-adds-domain-controller-parameters]

    - [create-adds-forest-extension.parameters.json][create-adds-forest-extension-parameters]

    - [virtualMachines-adds.parameters.json][virtualMachines-adds-parameters]

    - [virtualNetwork.parameters.json][virtualNetwork-parameters]

    - [virtualNetwork-adds-dns.parameters.json][virtualNetwork-adds-dns-parameters]

5. Edit the Deploy-ReferenceArchitecture.ps1 file in the Scripts folder, and change the following line to specify the resource group that should be created or used to hold the VM and resources created by the script:

    ```powershell
    # PowerShell
    $resourceGroupName = "ra-aad-onpremise-rg"
    ```

6. Edit the following files in the Parameters/Onpremise folder and set the `resourceGroup` value in the `virtualNetworkSettings` section in each of these files to be the same as that you specified in the Deploy-ReferenceArchitecture.ps1 script file.

    - virtualMachines-adds.parameters.json

    - virtualNetwork.parameters.json

    - virtualNetwork-adds-dns.parameters.json

7. Open an Azure PowerShell window, move to the Scripts folder, and run the following command:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows onpremise
    ```

    Replace `<subscription id>` with your Azure subscription ID.

    For `<location>`, specify an Azure region, such as `eastus` or `westus`.

**TBC - THERE ARE SOME OUTSTANDING QS CONCERNING THE ON-PREMISES SETUP**

## Next steps

<!-- links -->
[resource-manager-overview]: ../resource-group-overview.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[aad-explained]: https://youtu.be/tj_0d4tR6aM
[aad-editions]: https://azure.microsoft.com/documentation/articles/active-directory-editions/
[adfs-multitenant]: ./guidance-multitenant-identity-adfs.md
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[azure-multifactor-authentication]: https://azure.microsoft.com/documentation/articles/multi-factor-authentication/
[aad-conditional-access]: https://azure.microsoft.com/documentation/articles/active-directory-conditional-access/
[aad-dynamic-membership-rules]: https://azure.microsoft.com/documentation/articles/active-directory-accessmanagement-groups-with-advanced-rules/
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-user-sign-in]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-user-signin/
[aad-sync-requirements]: https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-directory-sync-requirements/
[aad-topologies]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-topologies/
[aad-filtering]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnectsync-configure-filtering/
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-connect-sync-default-rules]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnectsync-understanding-default-configuration/
[aad-identity-protection]: https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/
[aad-password-management]: https://azure.microsoft.com/documentation/articles/active-directory-passwords-customize/
[aad-application-proxy]: https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-enable/
[aad-connect-sync-operational-tasks]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnectsync-operations/#staging-mode
[aad-custom-domain]: https://azure.microsoft.com/documentation/articles/active-directory-add-domain/
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-sync-disaster-recovery]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnectsync-operations/#disaster-recovery
[aad-sync-best-practices]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnectsync-best-practices-changing-default-configuration/
[aad-adfs]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-get-started-custom/#configuring-federation-with-ad-fs
[aad-health]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-health-sync/
[aad-health-adds]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-health-adds/
[aad-health-adfs]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-health-adfs/
[aad-agent-installation]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-health-agent-install/
[aad-reporting-guide]: https://azure.microsoft.com/documentation/articles/active-directory-reporting-guide/
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/Deploy-ReferenceArchitecture.ps1
[vnet-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/webTier.parameters.json
[businesstier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/businessTier.parameters.json
[datatier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/dataTier.parameters.json
[managementtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/managementTier.parameters.json
[makecert]: https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/add-adds-domain-controller.parameters.json
[create-adds-forest-extension-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/create-adds-forest-extension.parameters.json
[virtualMachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adds.parameters.json
[virtualNetwork-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork.parameters.json
[virtualNetwork-adds-dns-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork-adds-dns.parameters.json
[0]: ./media/guidance-ra-identity-aad/figure1.png "Cloud identity architecture using Azure Active Directory"
[1]: ./media/guidance-ra-identity-aad/figure2.png "Single forest, single AAD directory topology"
[2]: ./media/guidance-ra-identity-aad/figure3.png "Multiple forests, single AAD directory topology"
[4]: ./media/guidance-ra-identity-aad/figure5.png "Staging server topology"
[5]: ./media/guidance-ra-identity-aad/figure6.png "Multiple AAD directories topology"
[6]: ./media/guidance-ra-identity-aad/figure7.png "Selecting a custom installation of Azure AD Connect Sync with a specific instance of SQL Server"
[7]: ./media/guidance-ra-identity-aad/figure8.png "Specifying the SSO method for user sign-in"
[8]: ./media/guidance-ra-identity-aad/figure9.png "Specifying Domain and OU filtering options"
[9]: ./media/guidance-ra-identity-aad/figure10.png "Enabling password write-back"
[10]: ./media/guidance-ra-identity-aad/figure11.png "The Azure AD management blade in the portal"
[11]: ./media/guidance-ra-identity-aad/figure12.png "The Azure AD Connect console"
[12]: ./media/guidance-ra-identity-aad/figure13.png "The Operations tab in the Synchronization Service Manager"
[13]: ./media/guidance-ra-identity-aad/figure14.png "The Connectors tab in the Synchronization Service Manager"
[14]: ./media/guidance-ra-identity-aad/figure15.png "The Synchronization Rules Editor"
[15]: ./media/guidance-ra-identity-aad/figure16.png "The Azure Active Directory Connect Health blade in the Azure portal showing synchronization health"
[16]: ./media/guidance-ra-identity-aad/figure17.png "The Azure Active Directory Connect Health blade in the Azure portal showing AD DS health"
[17]: ./media/guidance-ra-identity-aad/figure18.png "Security reports available in the Azure portal"
[18]: ./media/guidance-ra-identity-aad/figure19.png "The Azure portal highlighting the public IP address of the web-tier load balancer"
[19]: ./media/guidance-ra-identity-aad/figure20.png "Using Internet Explorer to browse to the public IP address of the web-tier load balancer"
[20]: ./media/guidance-ra-identity-aad/figure21.png "The certificates snap-in showing the www.contoso.com certificate"
[21]: ./media/guidance-ra-identity-aad/figure22.png "Connecting to the management tier VM"
[22]: ./media/guidance-ra-identity-aad/figure23.png "Creating the HTTPS binding for the default web site"
[23]: ./media/guidance-ra-identity-aad/figure24.png "Creating the ContosoWebApp1 web application"
[24]: ./media/guidance-ra-identity-aad/figure25.png "Setting the authentication properties of the ContosoWebApp1 web application"
[25]: ./media/guidance-ra-identity-aad/figure26.png "Signing in to Azure AAD from the ContosoWebApp1 web application"
[26]: ./media/guidance-ra-identity-aad/figure27.png "Changing the folder for the default web site"