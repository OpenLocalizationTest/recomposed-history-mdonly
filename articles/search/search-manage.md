<properties 
	pageTitle="Service administration for Azure Search in the Azure portal" 
	description="Manage Azure Search, a hosted cloud search service on Microsoft Azure, using the Azure portal." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="jhubbard" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/23/2016" 
	ms.author="heidist"/>

# Service administration for Azure Search in the Azure portal
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [REST API](search-get-started-management-api.md)

Azure Search is a fully-managed, cloud-based search service used for building a rich search experience into custom apps. This article covers the *service administration* tasks that you can perform in in the [Azure Portal](https://portal.azure.com) for a search service that you've already provisioned. *Service administration* is light-weight by design, limited to the following tasks:

- Authentication: Managing and securing access to the *api-keys* used for read or write access to your service.
- Scale: Adjusting service capacity by allocating partitions and replicas.
- Resource burn rate: Monitoring resource usage, relative to maximum limits of your service tier.

**Not in scope** 

*Content management* (or index management) refers to operations such as analyzing search traffic to understand query volume, discover which terms people search for, and how successful search results are in guiding customers to specific documents in your index. Content management is beyond the scope of this article. See [Search Traffic Analytics for Azure Search](search-traffic-analytics.md) for instructions on how to gain insights into internal operations at the index level.

*Query performance* is also beyond the scope of this article. See [Performance and optimization in Azure Search](search-performance-optimization.md) for more information.

> [AZURE.NOTE] Azure Search does not provide built-in solutions for disaster recovery or backup-and-restore. For customers who push objects to their service, the source code for creating and populating an index is the de facto restore option. For disaster recovery, customers opt for redundancy via an additional service in a different regional data center.

<a id="admin-rights"></a>
## Administrator rights in Azure Search

Provisioning or decommissioning the service itself can be done by an Azure subscription administrator or a subscription contributor who created the service (also referred to as a service owner).

Within a service, anyone with access to the service URL and an admin api-key has read-write access to the service, or the ability to add, delete, or modify server objects such as api-keys, indexes, indexers, data sources, schedules, and role assignments as implemented through [RBAC-defined roles](#rbac).

<a id="sys-info"></a>
## Logging in Azure Search and system information

Azure Search does not expose log files for an individual service either through the portal or programmatic interfaces. At the Basic tier and above, Microsoft monitors all Azure Search services for 99.9% availability per service level agreements (SLA). If the service is slow or request throughput falls below SLA thresholds, support teams will address the issue and review log files available to them.

In terms of general information about your service, you can obtain information in the following ways:

- In the portal, on the service dashboard, through notifications, properties, and status messages.
- Using [PowerShell](search-manage-powershell.md) or the [Management REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx) to [get service properties](https://msdn.microsoft.com/library/azure/dn832694.aspx), or status on index resource usage.
- Using [search traffic analytics](search-traffic-analytics.md), as noted previously.

<a id="manage-keys"></a>
## Manage the api-keys

All requests to a search service will need an api-key that was generated specifically for your service. This api-key is the sole mechanism for authenticating access to your search service endpoint. 

An api-key is string composed of randomly generated numbers and letters. It is generated exclusively by your service. Through RBAC permissions, you can delete or read them, but you can't specify a user-defined string (specifically, if you have passwords that you routinely use, you can't override the generated value with a user-defined password). 

Two types of keys are used to access your search service:

+	Admin (valid for any read-write operation against the service)
+	Query (valid for read-only operations such as queries against an index)

An admin api-key is created when the service is provisioned. There are two admin keys. Each one is designated as *primary* and *secondary* to keep them straight, but in fact they are interchangeable. Each service has two admin keys so that you can roll one over without losing access to your service. You can regenerate either admin key, but you cannot add to the total admin key count. There is a maximum of two admin keys per search service.

Query keys are designed for client applications that call Search directly. You can create up to 50 query keys. In application code, you specify the search URL and a query api-key to allow read-only access to the service.

To get or regenerate api-keys, open the service dashboard. Click **KEYS** to slide open the key management page. Commands for regenerating or creating keys are at the top of the page.

 ![][9]

<a id="rbac"></a>
## Set RBAC roles on administrative access for Azure Search

Azure provides a [global role-based authorization model](../active-directory/role-based-access-control-configure.md) for all services managed through the Portal or ARM APIs. Owner, Contributor, and Reader roles determine the level of service administration for Active Directory users, groups, and security principals assigned to each role. 

In terms of Azure Search, role-based access controls determine the following administrative tasks:

Role|Task
---|---
Owner|Create or delete the service or any object on the service, including api-keys, indexes, indexers, indexer data sources, and indexer schedules.<p>View service status, including counts and storage size.<p>Add or delete role membership (only an Owner can manage role membership).<p>Subscription administrators and service owners have automatic membership in the Owners role.
Contributor|Has the same level of access as Owner, except for RBAC role management. For example, a Contributor can view and regenerate `api-key`, but he or she cannot modify role memberships.
Reader|View service status and query keys. Members of this role cannot change service configuration, nor can they view admin keys.

Note that roles do not grant access rights to the service endpoint. Search service operations, such as index management, index population, and queries on search data, are controlled through api-keys, not roles. See "Authorization for management versus data operations" in [Role-based access control in Azure Portal](../active-directory/role-based-access-control-configure.md) for more information.

Roles provide access control after the service is created. Only subscription administrators can add a Search service to a subscription.

<a id="secure-keys"></a>
## Secure the api-keys

Securing keys is a function of limiting access to the keys via the portal or Azure Resource Management (ARM) interfaces. As noted, subscription administrators and service owners can view and regenerate admin and query api-keys. As a precaution review role assignments to understand who has access to the admin keys.

1. In the service dashboard, click the Access icon to slide open the Users blade.
   ![][7]
2. In Users, review existing role assignments. As expected, Subscription admins already have full access to the service via the Owner role.
3. To drill further, click **Subscription admins** and then expand the role assignment list to see who has co-administration rights on your search service.

Another way to view access permissions is to click **Roles** on the Users blade. Doing so displays available roles and the number of users or groups assigned to each role.

<a id="sub-5"></a>
## Monitor resource usage

In the dashboard, resource monitoring is limited to the information shown in the service dashboard and a few metrics that you can obtain by querying the service. On the service dashboard, in the Usage section, you can quickly determine whether partition resource levels are adequate for your application.

Using the Search Service API, you can get a count on documents and indexes. There are hard limits associated with these counts based on the pricing tier. See [Search service limits](search-limits-quotas-capacity.md) for details. 

+	[Get Index Statistics](http://msdn.microsoft.com/library/dn798942.aspx)
+	[Count Documents](http://msdn.microsoft.com/library/dn798924.aspx)

> [AZURE.NOTE] Caching behaviors can temporarily overstate a limit. For example, when using the shared service, you might see a document count over the hard limit of 10,000 documents. The overstatement is temporary and will be detected on the next limit enforcement check. 


<a id="scale"></a>
## Scale up or down

Every search service starts with a minimum of one replica and one partition. If you signed up for dedicated resources using the [Basic or Standard pricing tiers](search-limits-quotas-capacity.md), you can click the **SCALE** tile in the service dashboard to adjust the number of partitions and replicas used by your service.

When you add capacity through either resource, the service uses them automatically. No further action is required on your part, but there will be a slight delay before the impact of the new resource is realized. It can take 15 minutes or more to provision additional resources.

 ![][10]

### Add replicas

Increasing queries per second (QPS) or achieving high availability is done by adding replicas. Each replica has one copy of an index, so adding one more replica translates to one more index available for handling service query requests. Currently, the rule of thumb is that you need at least 3 replicas for high availability (see [Capacity Planning](search-capacity-planning.md) for details).

A search service having more replicas can load balance query requests over a larger number of indexes. Given a level of query volume, query throughput is going to be faster when there are more copies of the index available to service the request. If you are experiencing query latency, you can expect a positive impact on performance once the additional replicas are online.

Although query throughput goes up as you add replicas, it does not precisely double or triple as you add replicas to your service. All search applications are subject to external factors that can impinge on query performance. Complex queries and network latency are two factors that contribute to variations in query response times.

### Add partitions

Most service applications have a built-in need for more replicas rather than partitions. For those cases where an increased document count is required, you can add partitions if you signed up for Standard service. Basic tier does not provide for additional partitions.

At the Standard tier, partitions are added in multiples of 12 (specifically, 1, 2, 3, 4, 6, or 12). This is an artifact of sharding; an index is created in 12 shards, which can all be stored on 1 partition or equally divided into 2, 3, 4, 6, or 12 partitions (one shard per partition).

### Remove replicas

After periods of high query volumes, you will most likely reduce replicas after search query loads have normalized (for example, after holiday sales are over).

To do this, you just move the replica slider back to a lower number. There are no further steps required on your part. Lowering the replica count relinquishes virtual machines in the data center. Your query and data ingestion operations will now run on fewer VMs than before. The minimum limit is one replica.

### Remove partitions

In contrast with removing replicas, which requires no extra effort on your part, you might have some work to do if you are using more storage than can be reduced. For example, if your solution is using three partitions, downsizing to one or two partitions will generate an error if the new storage space is less than required. As you might expect, your choices are to delete indexes or documents within an associated index to free up space, or keep the current configuration.

There is no detection method that tells you which index shards are stored on specific partitions. Each partition provides approximately 25 GB in storage, so you will need to reduce storage to a size that can be accommodated by the number of partitions you have. If you want to revert to one partition, all 12 shards will need to fit.

To help with future planning, you might want to check storage (using [Get Index Statistics](http://msdn.microsoft.com/library/dn798942.aspx)) to see how much you actually used. 

<a id="advanced-deployment"></a>
## Best practices on scale and deployment (video)

This 30-minute video reviews best practices for advanced deployment scenarios, including geo-distributed workloads. You can also see [Performance and optimization in Azure Search](search-performance-optimization.md) for help pages that cover the same points.

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<a id="next-steps"></a>
## Next steps

Once you understand the kinds of operations pertaining to service administration, consider the various approaches for service management:

- [PowerShell](search-manage-powershell.md)
- [Management REST API](search-get-started-management-api.md)

Also, if you haven't done so already, look at the [performance and optimization article](search-performance-optimization.md), and optionally watch the video noted in the in the previous section for more depth and demonstrations of recommended techniques.


<!--Image references-->
[7]: ./media/search-manage/rbac-icon.png
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png


 
