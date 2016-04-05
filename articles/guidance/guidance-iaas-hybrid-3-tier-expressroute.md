<properties
   pageTitle="Implementing a Hybrid Network Architecture with Azure ExpressRoute | Blueprint | Microsoft Azure"
   description="How to implement a secure site-to-site network architecture that spans an Azure virtual network and an on-premises network connected by using Azure ExpressRoute."
   services=""
   documentationCenter="na"
   authors="JohnPWSharp"
   manager=""
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="roshar"/>

# Azure Blueprints: Implementing a Hybrid Network Architecture with Azure ExpressRoute

This article describes best practices for connecting an on-premises network to networks and services on Azure by using Azure ExpressRoute. ExpressRoute connections are made using a private dedicated connection through a third-party connectivity provider. The private connection extends your on-premises network into Azure providing access to your own IaaS infrastructure in Azure, public endpoints used in PaaS services, and Office365 SaaS services. ExpressRoute connections do not go across the Internet, giving you predictable bandwidth up to 10 Gbps along with faster access compared to a VPN connection. 

> [AZURE.NOTE] Azure has two different deployment models: [Resource Manager][resource-manager-overview] and classic. This blueprint uses Resource Manager, which Microsoft recommends for new deployments.

Typical use cases for this architecture include:

- Hybrid applications where workloads are distributed between an on-premises network and Azure.

- Applications running large-scale, mission-critical workloads that require a high degree of scalability.

- Large-scale backup and restore facilities for data that must be saved off-site.

- Handling Big Data workloads.

- Using Azure as a disaster-recovery site.

> [AZURE.NOTE] The [ExpressRoute technical overview][expressroute-technical-overview] provides an introduction to ExpressRoute.

## Architecture blueprint

The following diagram highlights the important components in this architecture:

![IaaS: hybrid-expressroute](./media/blueprints/arch-iaas-hybrid-expressroute.png)

- **Azure Virtual Networks (VNets).** Each VNet can span multiple tiers, and tiers can be segmented using subnets in each VNet  and/or network security groups (NSGs). The VNets could reside in [multiple subscriptions][expressroute-multiplesubscriptions] in the same [geo-political region][expressroute-locations]. Connections are performed by using **private peering** utilizing addresses which are private to the VNet.

- **Azure public services.** These are Azure services that can be utilized within a hybrid application. These services are also available across the Internet, but accessing them via an ExpressRoute circuit provides low latency and more predictable performance since traffic does not go through the Internet. Connections are performed by using **public peering**, and traffic is routed to [IP address ranges published by Microsoft][datacenter-ip-ranges]. The ExpressRoute circuit performs a NAT translation of on-premises traffic to an endpoint in one of these ranges. Connections over public peering can only be initiated from on-premises.

- **Office 365 services.** These are the publicly available Office 365 applications and services provided by Microsoft. Connections are performed by using **Microsoft peering**, with addresses that are either owned by your organization or supplied by your connectivity provider.

- **On-premises corporate network.** This is a network of computers and devices, connected through a private local-area network running within an organization.

- **Local edge routers.** These are routers that connect the on-premises network to the circuit managed by the provider.

- **Microsoft edge routers.** These are two routers in an active-active highly available configuration. These routers enable a connectivity provider to connect their circuits directly to their datacenter.

- **ExpressRoute circuit.** This is a layer 2 or layer 3 circuit supplied by the connectivity provider that joins the on-premises network with Azure through the edge routers. The circuit uses the hardware infrastructure managed by the connectivity provider.

> [AZURE.NOTE] For more information about circuits and routing, see [ExpressRoute circuits and routing domains][circuits-and-routing-domains].

## Implementing this architecture

> [AZURE.NOTE] ExpressRoute connectivity providers fall into one of the following categories:

> - **Exchange Providers (IXP).** These are OSI Layer 2 (*data link layer*) providers that supply virtual cross-connections to Azure. An IXP provides control access to its network switches that act as simple bridges between your on-premises networks and Azure, giving a direct connection from LAN to LAN. The customer has to provide information to configure these bridges to route requests from one network to another.
>
> - **Network Service Providers (Telco).** These are OSI Layer 3 (*network layer*) providers that supply access to network switches that are configured by the provider to connect to Azure. These switches translate network addresses from your on-premises domain to the Microsoft domain, and route traffic between networks, often using Multiprotocol Label Switching (MPLS) or similar.
>
> To obtain a list of connectivity providers available at your location, use the following Azure PowerShell command:
> ```
> Get-AzureRmExpressRouteServiceProvider
> ```

The following high-level steps outline a process for implementing this architecture. Detailed examples using Azure PowerShell commands are described [later in this document](powershell). Note that this process assumes that you have already created a VNet for hosting the cloud application, that you have created the on-premises network, and that your organization has met the [ExpressRoute prerequiste requirements][expressroute-prereqs] for connecting to the Azure. 

- [Create an ExpressRoute circuit][create-expressroute-circuit]. Use the following command:

    ```
     New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> `
        -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```

- Arrange for the ExpressRoute circuit to be provisioned.

	If your service provider is a Telco:

	- Send the `ServiceKey` for the new circuit to the service provider, together with the address of a /28 subnet that is outside the range of you on-premises network(s) and Azure VNet(s).

		> [AZURE.NOTE] The service provider may provide an online portal for you to supply this information.

	- Wait for the provider to provision the circuit. You can verify the provisioning state of a circuit by using the following PowerShell command:

        ```
        Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
        ```

		The `Provisioning state` field in the `Service Provider` section of the output will change from `NotProvisioned` to `Provisioned` when the circuit is ready.

	If your service provider is an IXP:

	- Send the `ServiceKey` for the new circuit to the service provider.

	- Reserve several blocks of IP addresses to configure routing between your network and the Microsoft edge routers. Each peering requires two /30 subnets. For example, if you are implementing a private peering to a VNet and a public peering for accessing Azure services, you will require four /30 subnets. This is for availability purposes; one subnet provides a primary circuit while the other acts as a secondary circuit. The IP prefixes for these subnets cannot overlap with the IP prefixes used by your VNet or on-premises networks. For details, see [ExpressRoute routing requirements][expressroute-routing-requirements].

	- Wait for the provider to provision the circuit.

	- Configure routing for the ExpressRoute circuit.

	If your connectivity provider is a Telco, the provider should configure and manage routing for you; you provide the information necessary to enable the provider to implement the appropriate routes.

	<a name="address-space"></a>If your connectivity provider is an IXP, you will most likely be responsible for configuring routing yourself, using the /30 subnet addresses that you reserved. See [Create and modify routing for an ExpressRoute circuit][configure-expressroute-routing] for details. Use the following PowerShell commands to add a network peering for routing traffic:

	```
	Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
	```

	> [AZURE.NOTE]. The `PeeringType` parameter can be one of `AzurePublicPeering`, `MicrosoftPeering`, or `AzurePrivatePeering`.
	>
	> For more information about primary and secondary peering addresses, see [Availability](#availability)

	Depending on your requirements, you may need to perform the following operations:

	- Configure private peering for connecting between on-premises services and components running in the VNet.

	- Configure public peering for connecting between on-premises services and Azure public services.

	- Configure Microsoft peering for connecting between on-premises services and Office 365 services.


- [Link your private VNet(s) in the cloud to the ExpressRoute circuit][link-vnet-to-expressroute]. Use the following PowerShell commands:

	```
	$circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
	$gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
	New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
	```

>[AZURE.NOTE] For more information about this process, see [ExpressRoute workflows for circuit provisioning and circuit states][ExpressRoute-provisioning].

Note the following points:

- ExpressRoute uses the Border Gateway Protocol (BGP) for exchanging routing information between your network and Azure.

- You can connect multiple VNets located in different regions to the same ExpressRoute circuit as long as all VNets and the ExpressRoute circuit are located within the same geopolitical region.

## Availability

You can configure high availability for your Azure connection in different ways, depending on the type of provider you use, and the number of ExpressRoute circuits and VPN gateway connection you are willing to configure. The points below summarize your availability options:

- ExpressRoute does not support router redundancy protocols such as HSRP and VRRP to implement high availability. Instead, it uses a redundant pair of BGP sessions per peering. To facilitate highly-available connections to your network, Microsoft Azure provisions you with two redundant ports on two routers (part of the Microsoft edge) in an active-active configuration.

- If you are using an IXP connectivity provider, deploy redundant routers in your on-premises network in an active-active configuration. Connect the primary circuit to one router, and the secondary circuit to the other. This will give you a highly available connection at both ends of the connection. This is necessary if you require the ExpressRoute SLA. See [SLA for Azure ExpressRoute][sla-for-expressroute] for details.

	The following diagram shows a configuration with redundant on-premises routers connected to the primary and secondary circuits. Each circuit handles the traffic for a public peering and a private peering (each peering is designated a pair of /30 address spaces, as described in the [previous section](#address-space)).

	![IaaS: hybrid-expressroute](./media/blueprints/arch-iaas-hybrid-expressroute-redundant-routers.png)

- If you are using a Telco connectivity provider, verify that it provides redundant BGP sessions that handle availability for you.

- Configure a Site-to-Site VPN as a failover path for ExpressRoute. This is only applicable to private peering. For Azure and Office 365 services, the Internet is the only failover path.

- A single virtual network can be connected to multiple express route circuits and each of those circuits can be through different service providers. This can be used for high-availability. The throughput would be aggregated across providers.

## Security

You can configure security options for your Azure connection in different ways, depending on your security concerns and compliance needs. The points below summarize your security options.

- ExpressRoute operates in layer 3. Threats in the Application layer can be prevented by using a Network Security Appliance which restricts traffic to legitimate resources. Additionally, ExpressRoute connections can only be initiated from on-premises; this helps to prevent a rogue service from accessing and compromising on-premises data.

- To maximize security, add network security appliances between the on-premises network and the provider edge routers. This will help to restrict the inflow of unauthorized traffic from the VNet:

	![IaaS: hybrid-expressroute-firewalls](./media/blueprints/arch-iaas-hybrid-expressroute-firewalls.png)

- For auditing or compliance purposes, it may be necessary to prohibit direct access from components running in the VNet to the Internet. In this situation, Internet traffic should be redirected back through a gateway or proxy running  on-premises where it can be audited. The gateway can be configured to block unauthorized traffic flowing out, and filter potentially malicious inbound traffic.

	![IaaS: hybrid-expressroute-proxy](./media/blueprints/arch-iaas-hybrid-expressroute-proxy.png)

- By default, Azure VMs expose endpoints used for providing login access for management purposes - RDP and Remote Powershell for Windows VMs, and SSH for Linux-based VMs when deployed through the Azure portal. To maximize security, use NSGs to ensure that these endpoints are not publicly accessible. VMs should only be available using the internal IP address. These addresses can be made accessible through the ExpressRoute network, enabling on-premises DevOps staff to perform any necessary configuration or maintenance.

- If you must expose management endpoints for VMs to an external network, then use access control lists to restrict the visibility of these ports to a whitelist of IP addresses or networks.

## Scalability

To provide scalability for your ExpressRoute connection, consider the following points.

- To minimize financial costs, start with the smallest estimated bandwidth that you expect to require. Depending on availability, it may be possible to switch to a higher bandwidth offering from your provider if necessary. You can change the bandwidth of an existing circuit by using the following command:

	```
	$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

    $ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
	```

	You can increase the bandwidth without loss of connectivity. Downgrading the bandwidth will result in disruption in connectivity. You have to delete the circuit and recreate it with the new configuration.

- Start with the standard SKU of ExpressRoute, and upgrade to ExpressRoute Premium only when required. If necessary, you can also change the pricing plan (metered or unlimited). Switch the SKU and pricing plan by using the following commands (the `Sku.Tier` property can be `Standard` or `Premium`; the `Sku.Name` property can be `MeteredData` or `UnlimitedData`):

	```
	$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

    $ckt.Sku.Tier = "Premium"
    $ckt.Sku.Name = "MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
	```

	You can upgrade the SKU without disruption, but you cannot switch from the unlimited pricing plan to metered. When downgrading the SKU, your bandwidth consumption must remain within the default limit of the standard SKU.

	> [AZURE.NOTE] ExpressRoute offers two pricing plans to customers, based on metering or unlimited data. See [ExpressRoute pricing][expressroute-pricing] for details. Charges vary according to circuit bandwidth. Available bandwidth will likely vary from provider to provider. Use the `azure network express-route provider list` command to see the providers available in your region and the bandwidths that they offer.
	>
	> A single ExpressRoute circuit can support a number of peerings and VNet links. See [ExpressRoute limits][expressroute-limits] for more information.
	>
	> For an extra charge, ExpressRoute Premium Add-on provides:
	>
	> - Up to 10,000 routes per circuit. Without ExpressRoute Premium Add-on, the limit is 4,000 routes per circuit.
	>
	> - Global connectivity, enabling an ExpressRoute circuit located anywhere in the world to access resources in any region rather than just regions in the same continent.
	>
	> - An increase in the number of VNet links per circuit from 10 to a larger limit, depending on the bandwidth of the circuit.

- ExpressRoute circuits are designed to allow temporary network bursts up to two times the bandwidth limit that you procured for no additional cost. However, you should determine whether your connectivity provider supports this feature before depending on it.

## Monitoring and manageability

You can monitor traffic, BGP status, and API calls related to ExpressRoute as explained below. 

- Monitor the traffic and status of the local network switches and routers. This process depends on the features provided by the local routers. It may require using third-party software to monitor BGP sessions handled by routers, and generate alerts if a session fails. For an example, see [How to monitor BGP sessions with Nagios][bgp-monitor-nagios].

- Monitor BGP session status on Azure (also available through the Azure Management service). Specifically, track:

	- BGP session creation.

	- BGP session configuration updates.

- Use Azure Management Services to monitor the API calls made by the ExpressRoute service. In particular, monitor calls for:

	- Circuit creation

	- Gateway creation

	- Circuit link updates

	- Authorizations and updates

	![IaaS: hybrid-expressroute-monitor](./media/blueprints/arch-iaas-hybrid-expressroute-monitor.png)

## Troubleshooting

If a previously functioning ExpressRoute circuit now fails to connect, in the absence of any configuration changes on-premises or within your private VNet, you may need to contact the connectivity provider and work with them to correct the issue. You can also use the following Azure PowerShell commands to perform some limited checking and help determine where problems might lie:

- Verify that the circuit has been provisioned:

	```
	Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
	```

- Check the status of any private VNets to which you are attempting to connect through the ExpressRoute circuit:

	```
	Get-AzureVNetSite <<vnet-name>>
	```

- Examine the configuration of private VNets:

	```
	Get-AzureVNetConfig | fl
	```

- Verify the details for the ExpressRoute circuit:

	```
	Get-AzureDedicatedCircuit -ServiceKey <<service-key>>
	```

- Examine the link state between the ExpressRoute circuit and a VNet:

	```
	Get-AzureDedicatedCircuitLink -ServiceKey <<service-key>> -VNetName <<vnet-name>>
	```

- Get link authorization details:

	```
	Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey <<service-key>>
	```

- Retrieve routing information for the ExpressRoute circuit:

	```
	Get-AzureBGPPeering -ServiceKey <<service-key>>
	```

## Azure PowerShell commands

The [Azure PowerShell][azure-powershell] commands in this section show how to connect an on-premises network to an Azure VNet by using an ExpressRoute connection. This script assumes that you have:

- [Created and prepared your on-premises network][create-on-prem-network] with an address space of 10.10.0.0/16.

- [Created an Azure VNet][create-azure-vnet] named `hybrid-er-vnet` with an address space of 10.20.0.0/16. This VNet holds subnets (called `GatewaySubnet`, `hybrid-er-internal-subnet`, etc).

- Created an Azure resource group named `hybrid-er-rg` which contains the VNet.

```
param(
    [parameter(Mandatory=$true)]
    [ValidateScript({
        try {
            [System.Guid]::Parse($_) | Out-Null
            $true
        }
        catch {
            $false
        }
    })]
    [string]$SubscriptionId,

    [Parameter(Mandatory=$false)]
    [string]$BaseName = "hybrid-er",

    [Parameter(Mandatory=$false)]
    [string]$Location = "Central US",

    [Parameter(Mandatory=$false, ParameterSetName="CreateERCircuit")]
    [ValidateSet("Premium", "Standard")]
    [string]$ExpressRouteSkuTier = "Standard",

    [Parameter(Mandatory=$false, ParameterSetName="CreateERCircuit")]
    [ValidateSet("MeteredData", "UnlimitedData")]
    [string]$ExpressRouteSkuFamily = "MeteredData",

    [Parameter(Mandatory=$true, ParameterSetName="CreateERCircuit")]
    [string]$ExpressRouteServiceProviderName,

    [Parameter(Mandatory=$true, ParameterSetName="CreateERCircuit")]
    [string]$ExpressRoutePeeringLocation,

    [Parameter(Mandatory=$true, ParameterSetName="CreateERCircuit")]
    [string]$ExpressRouteBandwidth,

    [Parameter(Mandatory=$false, ParameterSetName="CreateVNet")]
    [string]$VnetAddressPrefix = "10.20.0.0/16",

    [Parameter(Mandatory=$false, ParameterSetName="CreateVNet")]
    [string]$GatewaySubnetAddressPrefix = "10.20.255.240/28",

    [Parameter(Mandatory=$false, ParameterSetName="CreateVNet")]
    [string]$InternalSubnetAddressPrefix = "10.20.0.0/16",
)

$resourceGroup = "$BaseName-rg"
$vnetName = "$BaseName-vnet"
$expressRouteCircuitName = "$BaseName-erc"
$gatewayPublicIpAddressName = "$BaseName-pip"
$vnetGatewayName = "$BaseName-vgw"
$vpnConnectionName = "$BaseName-vpn"

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

switch($PSCmdlet.ParameterSetName) {
    "CreateERCircuit" {
        New-AzureRmResourceGroup -Name $resourceGroup -Location $Location
        New-AzureRmExpressRouteCircuit -Name $expressRouteCircuitName `
            -ResourceGroupName $resourceGroup -Location $Location -SkuTier $ExpressRouteSkuTier `
            -SkuFamily $ExpressRouteSkuFamily -ServiceProviderName $ExpressRouteServiceProviderName `
            -PeeringLocation $ExpressRoutePeeringLocation -BandwidthInMbps $ExpressRouteBandwidth
    }
    "CreateVNet" {
        $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" `
            -AddressPrefix $GatewaySubnetAddressPrefix
        $internalSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $internalSubnetName `
            -AddressPrefix $InternalSubnetAddressPrefix
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup `
            -Location $Location -AddressPrefix $VnetAddressPrefix `
            -Subnet $gatewaySubnetConfig, $internalSubnetConfig
        $gatewayPublicIpAddress = New-AzureRmPublicIpAddress -Name $gatewayPublicIpAddressName -ResourceGroupName $resourceGroup `
            -Location $Location -AllocationMethod Dynamic
        # We will use the list accessor here, but if the order of the Subnet parameter in the New-AzureRmVirtualNetwork is changed,
        # the index for the SubnetId parameter will change.
        $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwIpConfig `
            -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $gatewayPublicIpAddress.Id
        $vnetGateway = New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName `
            -ResourceGroupName $resourceGroup -Location $Location -IpConfigurations $gatewayIpConfig `
            -GatewayType ExpressRoute -VpnType RouteBased
        $expressRouteCircuit = Get-AzureRmExpressRouteCircuit -Name $expressRouteCircuitName `
            -ResourceGroupName $resourceGroup
        $vpnConnection = New-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName `
            -ResourceGroupName $resourceGroup -Location $Location -VirtualNetworkGateway1 $vnetGateway `
            -PeerId $expressRouteCircuit.Id -ConnectionType ExpressRoute
    }
}

```

## Next steps

- Read the [Microsoft Azure ExpressRoute guide][expressroute-doc].

<!-- links -->
<!--anoakley:We need to update the acom links to use the ../ syntax-->
[implementing-a-multi-tier-architecture-on-Azure]: ./iaas-multi-tier.md
[microsoft-provider-peers]: https://azure.microsoft.com/documentation/articles/expressroute-locations/
[expressroute-technical-overview]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[circuits-and-routing-domains]: https://azure.microsoft.com/documentation/articles/expressroute-circuit-peerings/
[resource-manager-overview]: ../resource-group-overview.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-virtual-network]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/
[expressroute-prereqs]: https://azure.microsoft.com/documentation/articles/expressroute-prerequisites/
[create-expressroute-circuit]: https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/
[configure-expressroute-routing]: https://azure.microsoft.com/documentation/articles/expressroute-howto-routing-arm/
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute/v1_0/
[datacenter-ip-ranges]: http://www.microsoft.com/download/details.aspx?id=41653
[link-vnet-to-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/
[ExpressRoute-provisioning]: https://azure.microsoft.com/documentation/articles/expressroute-workflows/
[expressroute-overview]: https://azure.microsoft.com/blog/expressroute-an-overview/
[expressroute-routing-requirements]: https://azure.microsoft.com/en-gb/documentation/articles/expressroute-routing/
[expressroute-locations]: https://azure.microsoft.com/en-us/documentation/articles/expressroute-locations/
[create-on-prem-network]: https://technet.microsoft.com/en-us/library/dn786406.aspx
[create-azure-vnet]: https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-create-vnet-arm-ps/
[expressroute-doc]: https://go.microsoft.com/fwlink/p/?LinkId=615122
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#networking-limits
[expressroute-multiplesubscriptions]: https://azure.microsoft.com/en-us/documentation/articles/expressroute-howto-linkvnet-arm/
[bgp-monitor-nagios]: http://xmodulo.com/monitor-bgp-sessions-nagios.html
