<properties 
    pageTitle="Geo-Replication for Azure SQL Database using PowerShell" 
    description="Geo-Replication for Azure SQL Database using PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="09/18/2015"
    ms.author="sstein"/>

# Geo-Replication for Azure SQL Database using PowerShell

**Single database**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


This article shows you how to configure Geo-Replication for SQL Database with PowerShell.



> [AZURE.NOTE] Anything noteworthy?


To configure Geo-Replication you need the following:

- An Azure subscription. If you need an Azure subscription simply click **FREE TRIAL** at the top of this page, and then come back to finish this article.


- Azure PowerShell. You can download and install the Azure PowerShell modules by running the [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). For detailed information, see [How to install and configure Azure PowerShell](powershell-install-configure.md).



## Configure your credentials and select your subscription

First you must establish access to your Azure account so start PowerShell and then run the following cmdlet. In the login screen enter the same email and password that you use to sign in to the Azure portal.


	Add-AzureAccount

After successfully signing in you will see some information on screen that includes the Id you signed in with and the Azure subscriptions you have access to.


### Select your Azure subscription

To select the subscription you need your subscription Id. You can copy the subscription Id from the information displayed from the previous step, or if you have multiple subscriptions and need more details you can run the **Get-AzureSubscription** cmdlet and copy the desired subscription information from the resultset. The following cmdlet uses the subscription Id to set the current subscription:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

After successfully running **Select-AzureSubscription** you are returned to the PowerShell prompt. If you have more than one subscription you can run **Get-AzureSubscription** and verify the subscription you selected shows **IsCurrent : True**.


## Setup the variables for your environment

There are a few variables where you need to replace the example values with the specific values for your database and server.

The server name needs to be a server that currently exists in the subscription selected in the previous step.

The database name is the name you want for ....

    $ServerName = "servername"
    $DatabaseName = "databasename"

## Add secondary database

The command serves to make the local database into a Geo-Replication primary (assuming that it is not already) and begin replicating data from it to a secondary database with the same name on another “partner” server.  

NOTE: If the partner database exists (for example as a result of termination of a previous geo-replication relationship) the command will fail.

This cmdlet replaces Start-AzureRMSqlDatabaseCopy with the –IsContinuous parameter provided.  It will output an AzureRMSqlDatabaseSecondary object that can be used by other cmdlets to clearly identify a specific replication link. This cmdlet will return when the secondary database is created and fully seeded. Depending on the size of the database it may take from minutes to hours.

Auth: To enable a secondary you must be the subscription owner or co-owner. 

### Add readable secondary (single database)

The following command creates a readable secondary of database "mydb" of server “srv2” in resource group "rg2":

    $database = Get-AzureRMSqlDatabase –DatabaseName “mydb”
    $secondaryLink = $database | New-AzureRMSqlDatabaseSecondary –PartnerResourceGroupName “rg2” –PartnerServerName “srv2” -AllowConnections "ReadOnly"



### Add non-readable secondary (single database)

The following command creates a non-readable secondary of database "mydb" of server “srv2” in resource group "rg2":

    $database = Get-AzureRMSqlDatabase –DatabaseName “mydb”
    $secondaryLink = $database | New-AzureRMSqlDatabaseSecondary –PartnerResourceGroupName “rg2” –PartnerServerName “srv2” -AllowConnections "No"




### Add a readable secondary (elastic database)

The following command creates a readable secondary of database "mydb" in the elastic database pool named "ElasticPool1" of server “srv2” in resource group "rg2":

    $database = Get-AzureRMSqlDatabase –DatabaseName “mydb”
    $secondaryLink = $database | New-AzureRMSqlDatabaseSecondary –PartnerResourceGroupName “rg2” –PartnerServerName “srv2” –SecondaryElasticPoolName ”ElasticPool1” -AllowConnections "ReadOnly"



### Add a non-readable secondary (elastic database)

The following command creates a non-readable secondary of database "mydb" in the elastic database pool named "ElasticPool1" of server “srv2” in resource group "rg2":

    $database = Get-AzureRMSqlDatabase –DatabaseName “mydb”
    $secondaryLink = $database | New-AzureRMSqlDatabaseSecondary –PartnerResourceGroupName “rg2” –PartnerServerName “srv2” –SecondaryElasticPoolName ”ElasticPool1” -AllowConnections "No"



## Remove secondary database

The operation permanently terminates the replication to the secondary database and change the role of the secondary to the regular read-write database. If the connectivity to secondary database is broken the command succeeds but the secondary will become read-write after connectivity is restored.  

This cmdlet replaces Stop-AzureRMSqlDatabaseCopy for replication.  This removal is equivalent of a forced termination that removes the replication link and leaves the former secondary as a standalone database that is not fully replicated prior to termination.  All link data will be cleaned up on both the former primary and former secondary, if or when they are available. This cmdlet will return when the replication link is removed. 

Auth: In order to remove secondary, users should have write access to both primary and secondary databases according to RBAC. See Role-based access control for details.

The following command removes replication link of database named “mydb” to server “srv2” of the resource group “rg2”. 

    $database = Get-AzureRMSqlDatabase –DatabaseName “mydb”
    $secondaryLink = $database | Get-AzureRMSqlDatabaseReplicationLink –SecondaryResourceGroup “rg2” –PartnerServerName “srv2”
    $secondaryLink | Remove-AzureRMSqlDatabaseSecondary 




## Initiate a planned failover from the primary database to the secondary database

The secondary database can be switched to primary and vice versa. It is designed for planned failover such as during the DR drills. The command performs the following workflow: 

1. Temporarily switch replication to synchronous mode. This will cause all outstanding transactions to be flushed to the secondary; 

2. Switch the roles of the two databases in the geo-replication relationship.  

This sequence guarantees that no data loos will occur. There is a short period during which both databases are unavailable (on the order of 0 to 25 seconds) while the roles are switched. The entire operation should take less than a minute to complete under normal circumstances. 

NOTE:  If the primary database is unavailable when the command is issued it will fail with the error message indicating that the primary server is not available. In rare cases it is possible that the operation cannot complete and may appear stuck. In this case the user can call the force failover command and accept data loss. 

This cmdlet is designed to perform multiple “alter” operations on the secondary, but at the moment it is limited to initiating failover. This cmdlet will return when the process of switching the secondary database to primary is completed.

The following command switches the roles of the database named “mydb” on the server “srv2” under the resource group “rg2” to primary. The original primary to which “db2” was connected to will switch to secondary after the two databases are fully synchronized.

    $database = Get-AzureRMSqlDatabase –DatabaseName “mydb” –ResourceGroupName “rg2” –ServerName “srv2”
    $database | Set-AzureRMSqlDatabaseSecondary -Failover



## Initiate an unplanned failover from the primary database to the secondary database

### Cleanup

### Allow/disallow data loss

In the case on an outage when the primary is no longer available the secondary database can be switched to primary using forced failover. It is designed for disaster recovery when restoring availability of the database is critical and some data loss is acceptable. 
When forced failover is invoked, the specified secondary database immediately becomes a primary database and begins accepting write transactions. As soon as the original primary database is able to reconnect with this new primary database after the forced failover operation, an incremental backup is taken on the original primary database and it is made into a secondary for the new primary database; subsequently, it is merely a replica of the new primary. If the user wishes to recovery data committed to the old primary database which had not been replicated to the new primary database, then they must use the point-in-time restore feature to restore the old primary database to a time immediately preceding its change into a secondary. It may be difficult for the user themselves to determine what time they should use in order to guarantee that all data is recovered; in the minimum viable product, users will need to engage devops should they wish to retrieve this information.
NOTE:
1.	The user can call the geo-restore API to restore the final backup of the old primary. If Azure storage in the region hosting the old primary was not impacted by the same outage, it is possible that all data will be preserved in the final backup. Note, SQL Database does not automatically take backups of the secondary databases. 
2.	If the command is issued when the both primary and secondary are online the old primary will become the new secondary but data synchronization will not be attempted. So some data loss may occur.
If the primary database has multiple secondaries the command will partially succeed. The secondary on which the command was executed will become primary. The old primary however will remain primary, i.e. the two primaries will end up in inconsistent state and connected by a suspended replication link. The user will have to manually repair this configuration using a “remove secondary” API on either of these primary databases.


The forced failover can be executed using the cmdlet Set-AzureRMSqlDatabaseSecondary described in section 4.3.2.  But because it typically done when the primary is unavailable –AllowDataLoss parameter must be specified. 

The following command switches the roles of the database named “mydb” to primary when the primary is unavailable. The original primary to which “mydb” was connected to will switch to secondary after it is back online. At that point the synchronization may result in data loss

    $database = Get-AzureRMSqlDatabase –DatabaseName “mydb” –ResourceGroupName “rg2” –ServerName “srv2”
    $database | Set-AzureRMSqlDatabaseSecondary –Failover -AllowDataLoss



## Monitor Geo-Replication configuration and health

Monitoring tasks include monitoring of the geo-replication configuration and monitoring data replication health.  

The cmdlet Get-AzureRMSqlDatabaseReplicationLink can be used to retrieve the information about the forward replication links visible in the sys.geo_replication_links catalog view.

The following command retrieves status of the replication link between the primary database “mydb” and the secondary on server “srv2” of the resource group “rg2”.

    $database = Get-AzureRMSqlDatabase –DatabaseName “mydb”
    $secondaryLink = $database | Get-AzureRMSqlDatabaseReplicationLink –PartnerResourceGroup “rg2” –PartnerServerName “srv2”



## Copy a database

A new database is created in the same or different logical server using a default or user selected service objective, including a named elastic pool. As on option the user can decide to manually control the synchronization point.

Auth: In order to initiate database copies, users should have write access to both the target server and the source database according to RBAC. See Role-based access control for details.

This cmdlet replaces Start-AzureRMSqlDatabaseCopy for database copies.  It follows the same pattern as New-AzureRMSqlDatabase and will return when the copy operation is completed. Depending on the database size it may take from minutes to hours. 

### Copy a database

The following command creates a database named “db2” in server “srv2”.

    $database = Get-AzureRMSqlDatabase –DatabaseName “db1” –ResourceGroupName “rg1” –ServerName “srv1”
    $databaseCopy= $database | New-AzureRMSqlDatabaseCopy –CopyResourceGroupName “rg2”–CopyServerName “srv2” –CopyDatabaseName ”db2”


### Copy a database into an elastic database pool

The following command creates a database named “db2” in the elastic pool “MyPool” in logical server “srv2”.

    $database = Get-AzureRMSqlDatabase –DatabaseName “db1” –ResourceGroupName “rg1” –ServerName “srv1”
    $databaseCopy= $database | New-AzureRMSqlDatabaseCopy –CopyResourceGroupName “rg2”–CopyServerName “srv2” –CopyDatabaseName ”db2” –ElasticPoolName ”MyPool” 



## Monitor a database copy

Since the database copy process is asynchronous the user can monitor the copy state transitions and the completion time.  

As the copy is created with a synchronous cmdlet New-AzureRMSqlDatabaseCopy no monitoring cmdlet is provided. To monitor the database copy with Transact-SQL, see [Monitor a database copy using Transact-SQL](sql-database-geo-replication-transact-sql.md#monitor-a-database-copy).




## Setup Geo-Replication PowerShell script


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"


    

## Next steps

- [Disaster Recovery Drills](sql-database-disaster-recovery-drills.md)




## Additional resources

- [Business Continuity Overview](sql-database-business-continuity.md)
- [SQL Database documentation](https://azure.microsoft.com/documentation/services/sql-database/)
