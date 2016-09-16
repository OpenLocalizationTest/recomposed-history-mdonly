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
   ms.date="09/16/2016"
   ms.author="telmos"/>

# Implementing Azure Active Directory

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

This article describes best practices for integrating on-premises Active Directory (AD) domains and forests with Azure Active Directory to provide cloud-based identity federation.

> [AZURE.NOTE] Azure has two different deployment models: [Resource Manager][resource-manager-overview] and classic. This reference architecture uses Resource Manager, which Microsoft recommends for new deployments.

You can use directory and identity services, such as those provided by AD Directory Services (AD DS) and AD Federation Services (AD FS), to authenticate identities. These identities can belong to users, computers, applications, or other resources that form part of a security domain. You can host directory and identity services on-premises, but in a hybrid scenario where elements of an application are located in Azure it can be more efficient to replicate this functionality to the cloud. This approach can help  reduce the latency caused by sending authentication and local authorization requests from the cloud back to the directory and identity services running on-premises.

Azure provides two solutions for implementing directory and identity services in the cloud:

1. You can use [Azure Active Directory (AAD)][azure-active-directory] to create a new AD domain in the cloud and link it to an on-premises AD domain. Then set up [Azure AD Connect][azure-ad-connect] on-premises to replicate identities held in the the on-premises repository to the cloud. Note that the directory in the cloud is **not** an extension of the on-premises system, rather it's a copy that contains the same identities. Changes made to these identities on-premises will be copied to the cloud, but changes made in the cloud **will not** be replicated back to the on-premises domain. Also, note that the same instance of AAD can be linked to more than one instance of AD DS; AAD will contain the identities of each AD repository to which it is linked.

	>[AZURE.NOTE] Azure Active Directory Premium edition enables write-back of user passwords, enabling your on-premises users to perform self-service password resets.

	It is also important to understand that AAD combines directory and identity services, enabling you to configure single sign-on (SSO) for users running applications accessed through the cloud.

2. You can deploy a VM running AD DS as a domain controller in Azure, extending your existing AD infrastructure from your on-premises datacenter, and you can deploy AD FS servers in the cloud as part of the same domain. This approach is more common for scenarios where the on-premises network and Azure virtual network are connected by a VPN and/or ExpressRoute connection. This solution also supports bi-directional replication enabling you make changes in the cloud and on-premises, wherever it is most appropriate.

This architecture focuses on solution 1. For more information about the second solution, see [Federating with a customer's AD FS for multitenant apps in Azure][adfs-multitenant].

>[AZURE.NOTE] You can also implement AAD without using an on-premises directory. In this case, AAD acts as the primary source of all identity information rather than containing data replicated from an on-premises directory.

Typical use cases for this architecture include:

- Situations where the on-premises network and Azure virtual network hosting cloud applications are not directly linked by using a VPN tunnel or ExpressRoute circuit.

- Providing SSO for end users running SaaS and other applications in the cloud, using the same credentials that they specify for on-premises applications.

- Publishing on-premises web applications through the cloud to provide access to remote users who belong to your organization.

- Implementing self-service capabilities for end-users, such as resetting their passwords, and delegating group management. 

	>[AZURE.NOTE] These capabilities can be controlled by an administrator through group policy.

You should note that AAD does not provide all the functionality of AD. For example, AAD currently only handles user authentication rather than computer authentication. Some applications and services, such as SQL Server, may require computer authentication in which case this solution is not appropriate. Additionally, AAD might not be suitable for systems where components could migrate across the on-premises/cloud boundary as this could require reconfiguration of AAD. 
> [AZURE.NOTE] For a detailed explanation of how Azure Active Directory works, watch [Microsoft Active Directory Explained][aad-explained].

## Architecture diagram

The following diagram highlights the important components in this architecture (*click to zoom in*). For more information about the grayed-out elements, read [Running VMs for an N-tier architecture on Azure][implementing-a-multi-tier-architecture-on-Azure]:

> [AZURE.NOTE] For simplicity, this diagram only shows the connections directly related to AAD, and does not depict web browser request redirects or other protocol related traffic that may occur as part of the authentication and identity federation process. For example, a user (on-premises or remote) will typically access a web app through a browser, and the web app may transparently redirect the web browser to authenticate the request through AAD. Once authenticated, the request can be passed back to the web app together with the appropriate identity information.

[![0]][0]

NOTE: NEED TO INCLUDE AD CONNECT SYNC SERVER IN THIS DIAGRAM

- **Azure Active Directory tenant**. This is an instance of AAD created by your organization. It acts as a simple directory service for cloud applications, and can also provide identity services.

- **Cloud Office 365**, **Public Cloud**, **SaaS**, **Azure**. These are examples of cloud-based applications and services for which AAD can act as an identity broker.

- **Web tier subnet**. This subnet holds VMs that implement a custom cloud-based application developed by your organization and for which AAD can act as an identity broker.

- **On-premises web apps**. These are web apps hosted in your organizations on-premises network. AAD can provide remote access to these web apps through the **AAD application proxy**. Your organization must run a **web app connector** on-premises to expose the web app to AAD.

	>[AZURE.NOTE] The web app connector opens an outbound network connection to the application proxy running in AAD. Remote users requests are routed back from AAD through this connection to the web apps. This mechanism removes the need to open inbound ports in the on-premises firewall, reducing the attack surface exposed by your organization.

- **On-premises AD FS server** and **on-premises AD DS server**. Your organization will likely already have an existing directory service such as AD DS, and may implement identity federation through AD FS. You can configure AAD to perform authentication through an on-premises AD FS server, which can, in turn, utilize the on-premises AD DS server.

- **AD Connect agent**. This is a piece of software that runs on-premises and synchronizes the on-premises AD to AAD in the cloud. For example, you can provision or deprovision groups and users on-premises and these changes will be propagated to AAD. The AD Connect Agent communicates with an **AAD Connect Sync server** which passes information to the AAD tenant.

	>[AZURE.NOTE] For security reasons, user's passwords are not stored directly in AAD. Rather, AAD holds a hash each password; this is sufficient to verify a user's password. If a user requires a password reset, this must be performed on-premises and the new hash sent to AAD. AAD Premium includes features that can automate this task to enable users to reset their own passwords.

## Recommendations

**NOTE TO SELF. NEED TO BUILD AND VERIFY THE ABOVE ARCHITECTURE BEFORE CONTINUING WITH THIS SECTION**

This section summarizes recommendations for implementing AAD, covering:

- aaa

### VM recommendations

AAD is a service that does not require any VMs. For recommendations about building the virtual machines for the web tier subnet, providing access to the web application, see [Running VMs for an N-tier architecture on Azure][implementing-a-multi-tier-architecture-on-Azure].

### Networking recommendations

**Does AAD work with Azure Resource Manager VNets? - https://blogs.technet.microsoft.com/markrenoden/2016/06/10/using-azure-active-directory-domain-services-with-arm-vnets/**

### Topology recommendations

**NOTE: DIAGRAMS FOR THIS SECTION TBD**

If you are integrating an on-premises directory with AAD, use Azure AD Connect  to implement a topology that most closely matches the requirements of your organization. AAD supports the following topologies:

- **Single forest, single AAD directory**. In this topology you use AD Connect to synchronize passwords for identities in one or more domains in a single on-premises forest with a single AAD tenant:

	[![1]][1]

	Don't use multiple AD Connect Sync servers to connect different domains in the same on-premises forest to the same AAD tenant unless you are running an AD Connect Sync server in staging mode (see Staging Server below).

- **Multiple forests, single AAD directory**. Use this topology if you have more than one on-premises forest. You can consolidate identity information so that each unique user is represented once in the AAD directory, even if the same user exists in more than one forest. All forests use the same AD Connect sync server. The AD Connect sync server does not have to be part of any domain, but it must be reachable from all forests:

	[![2]][2]

	In this topology, don't use separate AD Connect Sync servers to connect each on-premises forest to a single AAD tenant. This can result in duplicated identity information in AAD if users are present in more than one forest.

- **Multiple forests, separate topologies**. This approach enables you to merge identity information from separate forests into a single AAD tenant. This strategy is useful if you are combining forests from different organizations (after a takeover, for example), and the identity information for each user is held in only one forest:

	[![3]][3]

	If the GALs in each forest are synchronized, then a user in one forest may be present in another as a contact. This can occur if, for example, your organization has implemented GALSync with Forefron Identity manager 2010 or Microsoft Identity Manager 2016. In this scenario, you can specify that users should be identified by their *Mail* attribute. You can also match identities using the *ObjectSID* and *msExchMasterAccountSID* attributes; this is useful if you have one or more resource forests with disabled accounts.

	NOTE: MIGHT NEED TO REVISIT THIS ONE!

- **Staging server**. TBD

	[![4]][4]

- **Multiple AAD directories**. TBD

	[![5]][5]

For more information, see [Topologies for Azure AD Connect][aad-topologies].

DEFAULT CONFIGURATION AND RULES - https://azure.microsoft.com/documentation/articles/active-directory-aadconnectsync-understanding-default-configuration/

### Security recommendations

Use dynamic membership groups - https://youtu.be/Tdiz2JqCl9Q


## Availability considerations

AAD is designed to provide high availability. It is geo-distributed and runs in multiple data centers spread around the world, with automated failover. If a data center becomes unavailable, AAD ensures that your directory data is available for instance access in at least two more regionally dispersed data centers.

>[AZURE.NOTE] The SLA for AAD Basic and Premium services guarantee at least 99.9% availability. There is no SLA for the Free tier of AAD. For more information, see [SLA for Azure Active Directory][sla-aad].

## Security considerations

Track inconsistent access patterns - video 34:15

MFA

## Scalability considerations

You do not have to configure any options to implement scalability. AAD supports scalability based on replicas. AAD implements a single primary replica which handles write operations, and multiple read-only secondary replicas. AAD transparently redirects attempted writes made against secondary replicas to the primary replica. AAD provides eventual consistency; all changes made to the primary replica will be propagated to the secondary replicas. As most operations against AAD will be reads rather than writes, this architecture scales well.

For more information, see [Azure AD: Under the hood of our geo-redundant, highly available, distributed cloud directory][aad-scalability].

## Management considerations

https://azure.microsoft.com/en-gb/documentation/articles/active-directory-aadconnectsync-operations/


## Monitoring considerations

Video - 33:45

Health - https://azure.microsoft.com/en-gb/documentation/articles/active-directory-aadconnect-health/

## Solution components


## Deployment


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
[adfs-multitenant]: ./guidance-multitenant-identity-adfs.md
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[aad-topologies]: https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-topologies/
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/

[0]: ./media/guidance-ra-identity-aad/figure1.png "Cloud identity architecture using Azure Active Directory"
[1]: ./media/guidance-ra-identity-aad/figure2.png "Single forest, single AAD directory topology"
[2]: ./media/guidance-ra-identity-aad/figure3.png "Multiple forests, single AAD directory topology"
[3]: ./media/guidance-ra-identity-aad/figure4.png "Multiple forests, separate topologies"
[4]: ./media/guidance-ra-identity-aad/figure5.png "Staging server topology"
[5]: ./media/guidance-ra-identity-aad/figure6.png "Multiple AAD directories topology"