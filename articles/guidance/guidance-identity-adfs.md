<properties
   pageTitle="Implementing ADFS in Azure | Microsoft Azure"
   description="How to implement a secure hybrid network architecture with Active Directory Federation Service authorization in Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
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
   ms.date="10/04/2016"
   ms.author="telmos"/>

# Implementing a secure hybrid network architecture with federated identities in Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

This article describes best practices for implementing a secure hybrid network that extends your on-premises network to Azure, and uses [Active Directory Federation Services (AD FS)][active-directory-federation-services] to perform federated authentication and authorization for components running in the cloud. This architecture extends the structure described by [Extending Active Directory to Azure][implementing-active-directory].

> [AZURE.NOTE] Azure has two different deployment models: [Resource Manager][resource-manager-overview] and classic. This reference architecture uses Resource Manager, which Microsoft recommends for new deployments.

AD FS can run on-premises, but in a hybrid scenario where elements of an application are located in Azure it can be more efficient to implement this functionality in the cloud. Typical use cases for this architecture include:

- Hybrid applications where workloads run partly on-premises and partly in Azure.

- Solutions that utilize federated authorization to expose web applications to partner organizations.

- Systems that support access from web browsers running outside of the organizational firewall.

- Systems that enable users to access to web applications by connecting from authorized external devices such as remote computers, notebooks, and other mobile devices. 

For more information about how AD FS works, see [Active Directory Federation Services Overview][active-directory-federation-services-overview]. Additionally, the article [AD FS deployment in Azure][adfs-intro] contains a detailed step-by-step introduction to implementing AD FS in Azure.

## Architecture diagram

The following diagram highlights the important components in this architecture (*click to zoom in*). For more information about the grayed-out elements, read [Implementing a secure hybrid network architecture in Azure][implementing-a-secure-hybrid-network-architecture], [Implementing a secure hybrid network architecture with Internet access in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access], and [Implementing a secure hybrid network architecture with Active Directory identities in Azure][implementing-active-directory]:

**NOTE: NEED TO UPDATE ADDRESS SPACES IN DIAGRAM TO MATCH SAMPLE, WHEN STABILIZED**

[![0]][0]

>[AZURE.NOTE] This diagram depicts the following use cases:
>
>- Application code running inside a partner organization accesses a web application hosted inside your Azure VNet.
>
>- An external, registered user (with credentials stored inside AD DS) accessing a web application hosted inside your Azure VNet.
>
>- A user connecting to your VNet by using an authorized device and running a web application hosted inside you Azure VNet.
>
>Not all of these use cases might be relevant for your organization or situation.
>
>Additionally, this architecture focuses on passive federation, where the federation servers make the decisions concerning how and when to authenticate; the user is expected to provide sign in information when an application starts running. This is the mechanism most commonly used by web browsers and involves a protocol that redirects the browser to a site where the user can provide their credentials. AD FS also supports active federation whereby an application takes on responsibility for supplying credentials without further user interaction, but this case is outside the scope of this architecture.

- **AD DS subnet.** The AD DS servers are contained within their own subnet. NSG rules help to protect the AD DS servers and can provide a firewall against traffic from unexpected sources.

- **AD DS Servers.** These are domain controllers running as VMs in the cloud. These servers can provide authentication of local identities within the domain.

- **AD FS subnet.** The AD FS servers can be loacted within their own subnet, with NSG rules acting as a firewall.

- **AD FS servers.** The AD FS servers provide federated authorization and authentication. In this architecture, they perform the following tasks:

	- They can receive security tokens containing claims made by a partner federation server on behalf of a partner user. AD FS can verify that these tokens are valid before passing the claims to web application running in Azure. The corporate web application (in Azure) can use these claims to authorize requests. In this scenario, the corporate web application is the *relying party*, and it is the responsibility of the partner federation server to issue claims that will be understood by the corporate web application. The partner federation servers are referred to as *account partners* because they submit access requests on behalf of authenticated accounts in the partner organization.The AD FS servers are called *resource partners* because they provide access to resources (web applications, in this case).

	- They can authenticate (via AD DS and the [Active Directory Device Registration Service][ADDRS]) and authorize incoming requests from external users running a web browser or device that needs access to your corporate web applications. 

	The AD FS servers are configured as a farm, accessed through an an Azure load balancer. This structure helps to improve availability and scalability. Also, note that the AD FS servers are not exposed directly to the Internet, rather all Internet traffic is filtered through AD FS web application proxy servers and a DMZ.

- **AD FS proxy subnet.** The AD FS proxy servers can be contained within their own subnet, with NSG rules providing protection. The servers in this subnet are exposed to the Internet through a set of network virtual appliances that provide a firewall between your Azure virtual network and the Internet.

- **AD FS web application proxy (WAP) servers.** These computers act as AD FS servers for incoming requests from partner organizations and external devices. The WAP servers act as a filter, shielding the AD FS servers from direct access from the public Internet. As with the AD FS servers, deploying the WAP servers in a farm with load balancing gives you greater availability and scalability than deploying a collection of stand-alone servers.

	>[AZURE.NOTE] For detailed information about installing WAP servers, see [Install and Configure the Web Application Proxy Server][install_and_configure_the_web_application_proxy_server]

- **Partner organization.** This is an example partner organization which runs a web application that requests access to the web application running in Azure. The federation server at the partner organization authenticates requests locally, and submits security tokens containing claims to AD FS running in Azure. AD FS in Azure validates the security tokens, and if they are valid it can pass the claims to the web application running in Azure to authorize them.

	>[AZURE.NOTE] You can also configure a VPN tunnel using Azure Gateway to provide direct access to AD FS for trusted partners. Requests received from these partners do not pass through the WAP servers.

## Recommendations

This section summarizes recommendations for implementing AD FS in Azure, covering:

- VM recommendations.
- Networking recommendations.
- Availability recommendations.
- Security recommendations.
- AD FS installation recommendations.
- AD FS Trust recommendations.

### VM recommendations

Create VMs with sufficient resources to handle the expected volume of traffic. Use the size of the existing machines hosting AD FS on premises as a starting point. Monitor the resource utilization; you can resize the VMs and scale down if they are too large.

Follow the recommendations listed in [Running a Windows VM on Azure][vm-recommendations].

### Networking recommendations

Configure the network interface for each of the VMs hosting AD FS and WAP servers with static private IP addresses.

Do not give the AD FS VMs public IP addresses. See [Security considerations][security-considerations] for more details.

Set the IP address of the preferred and secondary DNS servers for the network interfaces for each AD FS and WAP VM to reference the AD DS VMs (which should be running DNS). This step is necessary to enable each VM to join the domain.

### Availability recommendations

Create an AD FS farm with at least two servers to increase availability of the AD FS service.

Use different storage accounts for each AD FS VM in the farm. This approach helps to ensure that a failure in a single storage account does not make the entire farm inaccessible.

Create separate Azure availability sets for the AD FS and WAP VMs. Ensure that there are at least two VMs in each set. Each availability set must have at least 2 update domains and 2 fault domains.

Configure the load balancers for the AD FS VMs and WAP VMs as follows:

- Use an Azure load balancer to provide external access to the WAP VMs, and an internal load balancer to distribute the load across the AD FS servers in the AD FS farm.

- Only pass traffic appearing on port 443 (HTTPS) to the AD FS/WAP servers.

- Give the load balancer a static IP address.

- Create a health probe using the TCP protocol rather than HTTPS. You can ping port 443 to verify that an AD FS server is functioning.

	>[AZURE.NOTE] AD FS servers use the Server Name Indication (SNI) protocol, so attempting to probe using an HTTPS endpoint from the load balancer will fail.

- Add a DNS *A* record to the domain for the AD FS load balancer. 

	Specify the IP address of the load balancer, and give it a name in the domain (such as adfs.contoso.com). This is the name by which clients and the WAP servers will access the AD FS server farm.

### Security recommendations

Prevent direct exposure of the AD FS servers to the Internet. AD FS are domain-joined computers that have full authorization to grant security tokens. If an AD FS server is compromised, a malicious user has the ability to issue full access tokens to all web applications and to federation servers that are protected by AD FS. If your system must handle requests from external users not necessarily connecting from trusted partner sites, use WAP servers to handle these requests. For more information, see [Where to Place a Federation Server Proxy][where-to-place-an-fs-proxy].

Place AD FS servers and WAP servers in separate subnets with their own firewalls. You can use NSG rules to define  firewall rules. If you require more comprehensive protection you can implement an additional security perimeter around servers by using a pair of subnets and NVAs, as described by the document [Implementing a secure hybrid network architecture with Internet access in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Note that all firewalls should allow traffic on port 443 (HTTPS).

Restrict direct login access to the AD FS and WAP servers. Only DevOps staff should be able to connect.

Do not join the WAP servers to the domain.

### AD FS installation recommendations

The article [Deploying a Federation Server Farm][Deploying_a_federation_server_farm] provides detailed instructions for installing and configuring AD FS. Perform the following tasks before configuring the first AD FS server in the farm:

1. Obtain a publicly trusted certificate for performing server authentication. The *subject name* must contain the name by which clients access the federation service. This can be the DNS name registered for the load balancer, for example, *adfs.contoso.com* (avoid using wildcard names such as **.contoso.com*, for security reasons). You should use the same certificate on all AD FS server VMs. You can purchase a certificate from a trusted certification authority, but if your organization uses Active Directory Certificate Services you can create your own. 

	The *subject alternative name* is used by the DRS to enable access from external devices. This should be of the form *enterpriseregistration.contoso.com*.

	For more information, see [Obtain and Configure an SSL Certificate for AD FS][adfs_certificates].

2. On the domain controller, generate a new root key for the Key Distribution Service. Set the effective time to be the current time minus 10 hours (this configuration reduces the delay that can occur in distributing and synchronizing keys across the domain). This step is necessary to support creating the group service account that will be used to run the AD FS service. The following Powershell command shows an example of how to do this:

	```powershell
	Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
	```

3. Add each AD FS server VM to the domain.

>[AZURE.NOTE] To install AD FS, the domain controller running the PDC emulator FSMO role for the domain must be running and accessible from the AD FS VMs.

### AD FS Trust recommendations

Establish federation trust between your AD FS installation, and the federation servers of any partner organizations. Configure any claims filtering and mapping required. This process requires:

- DevOps staff **at each partner organization** to add add a relying party trust for the web applications accessible through your AD FS servers.

- DevOps staff **in your organization** to configure claims-provider trust to enable your AD FS servers to trust the claims that partner organizations provide.

- DevOps staff **in your organization** to configure AD FS to pass claims on to your organization's web applications.

	For more information see [Establishing Federation Trust][establishing-federation-trust].

Publish your organization's web applications and make them available to external partners by using preauthentication through the WAP servers. For more information, see [Publish Applications using AD FS Preauthentication][publish_applications_using_AD_FS_preauthentication]

Note that AD FS supports token transformation and augmentation. Azure Active Directory does not provide this feature. With AD FS, when you set up the trust relationships, you can:

- Configure claim transformations for authorization rules. For example, you can map group security from a representation used by a non-Microsoft partner organization to something that that AD DS can authorize in your organization.

- Transform claims from one format to another. For example, you can map from SAML 2.0 to SAML 1.1 if your application only supports SAML 1.1 claims. 

## Availability considerations

You can use either SQL Server or the Windows Internal Database (WID) to hold AD FS configuration information. WID provides basic redundancy; changes are written directly to only one of the AD FS databases in the AD FS cluster, while the other servers use pull replication to keep their databases up to date. Using SQL Server can provide full database redundancy and high availability using failover clustering or mirroring.

## Security considerations

AD FS is heavily dependent on the HTTPS protocol, so make sure that the NSG rules for the subnet containing the web tier VMs permit HTTPS requests. These requests can originate from the on-premises network, the subnets containing the web tier, business tier, data tier, private DMZ, and public DMZ, as well as the subnet containing the AD FS servers.

You should also consider using a set of network virtual appliances that logs detailed information on traffic traversing the edge of your virtual network for auditing purposes.

## Scalability considerations

The following considerations, summarized from the document [Plan your AD FS deployment][plan-your-adfs-deployment], give a starting point for sizing AD FS farms:

- If you have fewer than 1000 users, do not create dedicated AD FS servers, but instead install AD FS on each of the AD DS servers in the cloud (make sure that you have at least two AD DS servers, to maintain availability). Create a single WAP server.

- If you have between 1000 and 15000 users, create two dedicated AD FS servers and two dedicated WAP servers.

- If you have between 15000 and 60000 users, create between three and five dedicated AD FS servers, and at least two dedicated WAP servers.

These figures assume you are using dual quad-core VMs (Standard D4_v2, or better) to host the servers in Azure.

Note that if you are using the Windows Internal Database to store AD FS configuration data, you are limited to eight AD FS servers in the farm. If you anticipate needing more, then use SQL Server. For further information, see [The Role of the AD FS Configuration Database][adfs-configuration-database].

## Management considerations

DevOps staff should be prepared to perform the following tasks:

- Managing the federation servers (managing the AD FS farm, managing trust policy on the federation servers, and managing the certificates used by the federation services).

- Managing the WAP servers (managing the WAP farm, managing the WAP certificates).

- Managing web applications (configuring relying parties, authentication methods, and claims mappings).

- Backing up AD FS components.

## Monitoring considerations

The [Microsoft System Center Management Pack for Active Directory Federation Services 2012 R2][oms-adfs-pack] provides both proactive and reactive monitoring of your AD FS deployment for the federation server. This management pack monitors:

- Events that the AD FS service records in the AD FS event logs.

- The performance data that the AD FS performance counters collect. 

- The overall health of the AD FS system and web applications (relying parties), and provides alerts for critical issues and warnings.

## Solution components

A sample solution script, [Deploy-ReferenceArchitecture.ps1][solution-script], is available that you can use to implement the architecture that follows the recommendations described in this article. This script utilizes Azure Resource Manager templates. The templates are available as a set of fundamental building blocks, each of which performs a specific action such as creating a VNet or configuring an NSG. The purpose of the script is to orchestrate template deployment.

The templates are parameterized, with the parameters held in separate JSON files. You can modify the parameters in these files to configure the deployment to meet your own requirements. You do not need to amend the templates themselves. Note that you must not change the schemas of the objects in the parameter files.

When you edit the templates, create objects that follow the naming conventions described in [Recommended Naming Conventions for Azure Resources][naming-conventions].

The sample solution creates and configures the environment in the cloud comprising the AD DS subnet and servers, the AD FS subnet and servers,  AD FS proxy subnet and servers, DMZ, web tier, business tier, and data access tier components, VPN gateway, and management tier. The sample solution also includes an optional configuration for creating a simulated on-premises environment.

The following sections describe the elements of the on-premises and cloud configurations.

### On-premises components

>[AZURE.NOTE] These components are not the main focus of the architecture described in this document, and are provided simply to give you an opportunity to test the cloud environment safely, rather than using a real production environment. For this reason, this section only summarizes the key parameter files. You can modify settings such as the IP addresses or the sizes of the VMs, but it is advisable to leave many of the other parameters unchanged.

This environment comprises an AD forest for a domain named contoso.com. The domain contains two AD DS servers with IP addresses 192.168.0.4 and 192.168.0.5. These two servers also run the DNS service. The local administrator account on both VMs is called `testuser` with password `AweS0me@PW`. Additionally, the configuration sets up a VPN gateway for connecting to the VNet in the cloud. You can modify the configuration by editing the following JSON files located in the [**parameters/onpremise**][on-premises-folder]  folder:

- **[virtualNetwork.parameters.json][on-premises-vnet-parameters]**. This file specifies the network address space for the on-premises environment.

- **[virtualMachines-adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. This file contains the configuration for each of the on-premises VMs hosting AD DS services. By default, the VMs use *Standard-DS3-v2* VMs.

- **[virtualNetworkGateway.parameters.json][on-premises-virtualnetworkgateway-parameters]** and **[connection.parameters.json][on-premises-connection-parameters]**. These files hold the settings for the VPN connection to the Azure VPN gateway in the cloud, including the shared key to be used to protect traffic traversing the gateway.

### Cloud components

These components form the core of this architecture. The [**parameters/azure**][azure-folder] folder contains the following parameter files for configuring these components:

- **[virtualNetwork.parameters.json][vnet-parameters]**. This file defines  structure of the VNet for the VMs and other components in the cloud. It includes settings, such as the name, address space, subnets, and the addresses of any DNS servers required. Note that the DNS addresses shown in this example reference the IP addresses of the on-premises DNS servers, and also the default Azure DNS server. You should modify these addresses to reference your own DNS setup if you are not using the sample on-premises environment:

	**QUESTION FOR HANZ: What does virtualNetwork-with-onpremise-and-azure-dns.parameters.json do?**

	```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "virtualNetworkSettings": {
          "value": {
            "name": "ra-adfs-vnet",
            "resourceGroup": "ra-adfs-network-rg",
            "addressPrefixes": [
              "10.0.0.0/16"
            ],
            "subnets": [
              {
                "name": "dmz-private-in",
                "addressPrefix": "10.0.0.0/27"
              },
              {
                "name": "dmz-private-out",
                "addressPrefix": "10.0.0.32/27"
              },
              {
                "name": "dmz-public-in",
                "addressPrefix": "10.0.0.64/27"
              },
              {
                "name": "dmz-public-out",
                "addressPrefix": "10.0.0.96/27"
              },
              {
                "name": "mgmt",
                "addressPrefix": "10.0.0.128/25"
              },
              {
                "name": "GatewaySubnet",
                "addressPrefix": "10.0.255.224/27"
              },
              {
                "name": "web",
                "addressPrefix": "10.0.1.0/24"
              },
              {
                "name": "biz",
                "addressPrefix": "10.0.2.0/24"
              },
              {
                "name": "data",
                "addressPrefix": "10.0.3.0/24"
              },
              {
                "name": "adds",
                "addressPrefix": "10.0.4.0/27"
              },
              {
                "name": "adfs",
                "addressPrefix": "10.0.5.0/27"
              },
              {
                "name": "proxy",
                "addressPrefix": "10.0.6.0/27"
              }
            ],
            "dnsServers": [
              "192.168.0.4",
              "192.168.0.5",
              "168.63.129.16"
            ]
          }
        }
      }
    }
    ```

- **[virtualMachines-adds.parameters.json ][virtualmachines-adds-parameters]**. This file configures the VMs running AD DS in the cloud. The configuration consists of two VMs. You should change the admin user name and password, and you can optionally modify the VM size to match the requirements of the domain:

	For more information, see [Extending Active Directory to Azure][extending-ad-to-azure].

	```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "virtualMachinesSettings": {
          "value": {
            "namePrefix": "ra-adfs-ad",
            "computerNamePrefix": "aad",
            "size": "Standard_DS3_v2",
            "osType": "Windows",
            "adminUsername": "testuser",
            "adminPassword": "AweS0me@PW",
            "osAuthenticationType": "password",
            "nics": [
              {
                "isPublic": "false",
                "subnetName": "adds",
                "privateIPAllocationMethod": "Static",
                "startingIPAddress": "10.0.4.4",
                "enableIPForwarding": false,
                "dnsServers": [
                ],
                "isPrimary": "true"
              }
            ],
            "imageReference": {
              "publisher": "MicrosoftWindowsServer",
              "offer": "WindowsServer",
              "sku": "2012-R2-Datacenter",
              "version": "latest"
            },
            "dataDisks": {
              "count": 1,
              "properties": {
                "diskSizeGB": 127,
                "caching": "None",
                "createOption": "Empty"
              }
            },
            "osDisk": {
              "caching": "ReadWrite"
            },
            "extensions": [
            ],
            "availabilitySet": {
              "useExistingAvailabilitySet": "No",
              "name": "ra-adfs-as"
            }
          }
        },
        "virtualNetworkSettings": {
          "value": {
            "name": "ra-adfs-vnet",
            "resourceGroup": "ra-adfs-network-rg"
          }
        },
        "buildingBlockSettings": {
          "value": {
            "storageAccountsCount": 2,
            "vmCount": 2,
            "vmStartIndex": 1
          }
        }
      }
    }
	```

- **[virtualMachines-adfs.parameters.json ][virtualmachines-adfs-parameters]** **TBD - WAITING FOR HANZ TO COMPLETE**

- **TODO: ADFS PROXY**

- **[virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. This file contains the settings used to create the Azure VPN gateway in the cloud used to connect to the on-premises network. You should modify the `sharedKey` value in the `connectionsSettings` section to match that of the on-premises VPN device. For more information, see [Implementing a Hybrid Network Architecture with Azure and On-premises VPN][hybrid-azure-on-prem-vpn].

- **[dmz-private.parameters.json][dmz-private-parameters]** and **[dmz-public.parameters.json ][dmz-public-parameters]**. These files configure the inbound (public) and outbound (private) sides of the VMs that comprise the DMZ, protecting the servers in the cloud. For more information about these elements and their configuration, see [Implementing a DMZ between Azure and the Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- **TODO: WEB TIER ETC + LBs, MANAGEMENT TIER**

## Solution deployment

**THIS SECTION TBD**

The solution assumes the following prerequisites:

- You have an existing on-premises infrastructure already configured with a VPN appliance.

- You have an existing Azure subscription in which you can create resource groups.

- You have downloaded and installed the most recent build of Azure Powershell. See [here][azure-powershell-download] for instructions.

To run the script that deploys the solution:

1. Move to a convenient folder on your local computer and create the following two subfolders:

	- Scripts

	- Templates

2. Download the [Deploy-ReferenceArchitecture.ps1][solution-script] file to the Scripts folder

3. Download the [vpn-gateway-vpn-connection-settings.parameters.json][gateway-parameters] file to Templates folder:

4. Edit the Deploy-ReferenceArchitecture.ps1 file in the Scripts folder, and change the following line to specify the resource group that should be created or used to hold the resources created by the script:

	```powershell
	$resourceGroupName = "hybrid-dev-rg"
	```
5. Edit the vpn-gateway-vpn-connection-settings.parameters.json file in the Templates folder to set the parameters for the VNet, virtual network gateway, and connection, as described in the Solution Components section above.

6. Open an Azure PowerShell window, move to the Scripts folder, and run the following command:

	```powershell
	.\Deploy-ReferenceArchitecture.ps1 <subscription id> <location>
	```

	Replace `<subscription id>` with your Azure subscription ID.

	For `<location>`, specify an Azure region, such as `eastus` or `westus`.

<!-- TBD: Add Verification Steps -->

## Next steps

- [Azure Active Directory][aad].

- [Azure Active Directory B2C][aadb2c].
<!-- links -->

[vm-recommendations]: ./guidance-compute-single-vm.md#Recommendations
[solution-script]: .
[gateway-parameters]: .
[naming-conventions]: ./guidance-naming-conventions.md
[implementing-active-directory]: ./guidance-iaas-ra-secure-vnet-ad.md
[resource-manager-overview]: ../resource-group-overview.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[import_server_authentication_certificate]: https://technet.microsoft.com/library/dd807088.aspx
[adfs-configuration-database]: https://technet.microsoft.com/en-us/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]: https://technet.microsoft.com/library/dn486775.aspx
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-aadconnect-azure-adfs/
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adfs/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adfs/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adfs/parameters/onpremise/connection.parameters.json
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adfs/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adfs/parameters/azure/dmz-public.parameters.json
[virtualnetworkgateway-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adfs/parameters/azure/virtualNetworkGateway.parameters.json
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-adds.parameters.json
[extending-ad-to-azure]: ./guidance-iaas-ra-secure-vnet-ad.md
[virtualmachines-adfs-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-adfs.parameters.json

[0]: ./media/guidance-iaas-ra-secure-vnet-adfs/figure1.png "Secure hybrid network architecture with Active Directory"
