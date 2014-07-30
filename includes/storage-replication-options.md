Data in your storage account is replicated to ensure durability that is also highly available, meeting the [Azure Storage SLA ](/en-us/support/legal/sla/) even in the face of transient hardware failures. Azure Storage is deployed in 15 regions around the world and also includes support for replicating data between regions. You have several options for replicating the data in your storage account:

- *Locally redundant storage (LRS)* is replicated three times within a single facility in a single region. LRS protects your data from normal hardware failures.

	Locally redundant storage is offered at a discount. For enhanced durability, we recommend that you use zone-redundant storage or geo-redundant storage, both described below.  

- *Zone-redundant storage (ZRS)* is replicated three times across two to three facilities, either within a single region or across two regions, providing higher durability than LRS. In the event of a failure at the primary facility, Azure Storage will failover to another facility.

	ZRS provides a higher level of durability than LRS; however, for maximum durability, we recommend that you use geo-redundant storage, described below.

	> [WACOM.NOTE] ZRS is currently available only for block blobs. Note that once you have created your storage account and selected zone-redundant replication, you cannot convert it to use to any other type of replication, or vice versa.

- *Geo-redundant storage (GRS)* is the default replication option when you create a new storage account. With GRS, your data is replicated three times within the primary region, and is also replicated three times to a second region hundreds of miles away from the primary region, providing the highest level of durability. In the event of a failure at the primary region, Azure Storage will failover to the secondary region. 

	> [WACOM.NOTE] GRS is recommended over ZRS or LRS for maximum durability.

- *Read-access geo-redundant storage (RA-GRS)* provides all of the benefits of geo-redundant storage noted above, and also allows read access to data at the secondary region in the event that the primary region becomes unavailable. Read-access geo-redundant storage is recommended for maximum availability in addition to durability.  

For more details about replication options, see the [Azure Storage Team Blog](http://blogs.msdn.com/b/windowsazurestorage/) and [Azure Storage Redundancy Options](http://msdn.microsoft.com/en-us/library/azure/dn727290.aspx).
	
The pricing differences between the various replication options can be found on the [Storage Pricing Details](/en-us/pricing/details/storage/) page.
