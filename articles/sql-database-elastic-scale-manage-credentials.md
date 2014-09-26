<properties title="Managing Elastic Scale Credentials" pageTitle="Managing Elastic Scale Credentials" description="Managing Elastic Scale Credentials, shard maps, elastic scale" metaKeywords="Azure SQL Database, elastic scale, about user credentials in elastic scale" services="sql-database" documentationCenter="sql-database" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Managing Elastic Scale Credentials  

The [Elastic Scale Client APIs](http://go.microsoft.com/?linkid=9862605) expects credentials for different types of operations – in particular creating or manipulating a Shard Map Manager, referencing an existing [Shard Map Manager](http://go.microsoft.com/?linkid=9862595) to obtain information about shards, and performing actual connections to shards. Credentials for these types of operations are discussed below. 


* **Management Credentials for Shard Map Access**: The management credentials are used when instantiating a **ShardMapManager** object for applications that manipulate the shard maps. The user of the Elastic Scale APIs has to create the necessary SQL users and SQL logins and make sure they are granted the read/write permissions on the global shard map database and all shard databases as well. These credentials are used to maintain the global shard map and the local shard maps when changes to the shard map are performed. For instance, one would use the management credentials to instantiate the shard map manager object, as the following code shows: 

		// Obtain shard map manager. 
		ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
		); 


	In the above example, the variable **smmAdminConnectionString** is a connection string that contains the management credentials. The user id and password should provide read/write access to both shard map database and individual shards.  For the shard map manager, the management connection string also includes the server name and database name to identify the global shard map database. A typical connection string for that purpose would look as follows:


		"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

* **User Credentials for Shard Map Manager Access**:  When instantiating the Shard Map Manager in an application that is not going to administer shard maps, we suggest using credentials that have read-only permissions on the global shard map. The information retrieved from the global shard map under these credentials will be used for [data-dependent routing](sql-database-elastic-scale-data-dependent-routing) and to populate the shard map cache on the client. The credentials are provided through the same call pattern to **GetSqlShardMapManager** as shown earlier: 
 
		// Obtain shard map manager. 
		ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
		);  

	In the above code sample, note the use of the **smmReadOnlyConnectionString** to reflect the use of different credentials for this access on behalf of non-admin users—these credentials should not provide write permissions on the global shard map. 

* **User Credentials for User Data Access**: Additional credentials are needed when using the **OpenConnectionForKey** method to access a shard associated with a key.  These credentials also need to provide permissions for read-only access to the local shard map tables residing on the shard. This is needed to perform connection validation for data dependent routing on the shard. The following code snippet illustrates the use of the user credentials for user data access in the context of data dependent routing: 
 
		using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
		targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

	In this example, the variable smmUserConnectionString holds the connection string for the user credentials. For Azure SQL DB, a typical connection string for user credentials would look as follows: 

		"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

	Note that the connection string for the user credentials does not contain a server name and database name. That is because the **OpenConnectionForKey** call will automatically direct the connection to the correct shard based on the key. Hence, the database name and server name must not be provided for the **OpenConnectionForKey** call. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]