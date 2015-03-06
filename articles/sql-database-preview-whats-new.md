<properties 
	pageTitle="What's new in SQL Database V12" 
	description="Describes the latest enhancements to Azure SQL Database that were added in December 2014 or later." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


# What's new in SQL Database V12


<!--
GeneMi , 2015-March-05 Thursday 20:06pm
Remove 'Azure' from topic title, append 'V12' (per J.G.).
-->



Version V12 of Azure SQL Database was promoted to General Availability (GA) in January 2015. With this major update, SQL Database now provides nearly complete compatibility with the Microsoft SQL Server engine. SQL Database now brings more Premium performance to customers. These enhancements help to streamline SQL Server application migrations to SQL Database, and help customers who have heavier database workloads.


A couple of geographic regions are still at preview status and are not yet at GA. Find your region on the [GA-per-region table](../sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable). Until your region is at GA, V12 is best suited to test databases rather than production databases.


**[Sign up](https://portal.azure.com) for SQL Database to take advantage of this new generation on Microsoft Azure. You first need a subscription to Microsoft Azure. You can sign up for a [free Azure trial](http://azure.microsoft.com/pricing/free-trial) and review [pricing](http://azure.microsoft.com/pricing/details/sql-database) information.**


Your path to planning and upgrading your V11 databases to V12 starts at [Plan and prepare to upgrade to the Latest SQL Database Update V12 (preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


### Key highlights


Key highlights of Azure SQL Database V12 include the following:


- **Security** enjoys the new feature of [users in contained databases](../sql-database-preview-whats-new/#UsersInContainedDatabases). Two other features are [row-level security](../sql-database-preview-whats-new/#RowLevelSecurity) and [dynamic data masking](../sql-database-preview-whats-new/#DynamicDataMasking), although these are still at preview and are not yet at GA.


- **Easier management** of large databases to support heavier workloads with parallel queries (Premium only), [table partitioning](http://msdn.microsoft.com/library/ms187802.aspx), [online indexing](http://msdn.microsoft.com/library/ms188388.aspx), worry-free large index rebuilds with 2GB size limit removed, and more options on the [alter database](http://msdn.microsoft.com/library/bb522682.aspx) statement.


- **Support for key programmability functions** to drive more robust application design with [CLR integration](http://msdn.microsoft.com/library/ms189524.aspx), T-SQL [window functions](http://msdn.microsoft.com/library/ms189461.aspx), [XML indexes](http://msdn.microsoft.com/library/bb934097.aspx), and [change tracking](http://msdn.microsoft.com/library/bb933875.aspx) for data.


- **Breakthrough performance** with support for in-memory [columnstore](http://msdn.microsoft.com/library/gg492153.aspx) queries (Premium tier only) for data mart and smaller analytic workloads.


- **Monitoring and troubleshooting** are improved with visibility into over 100 new table views in an expanded set of Database Management Views ([DMVs](http://msdn.microsoft.com/library/ms188754.aspx))


- **New S3 performance level in the Standard tier:** offers more pricing flexibility between Standard and Premium. S3 will deliver more DTUs (database throughput units) and all the features available in the Standard tier.


## 1. Details of the new V12 enhancements explained


This section names and explains the new features in each category.


### 1.1 Category: Expanded database management


| Feature | Description |
| :--- | :--- |
| . | ***December 2014:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Users in contained databases | You can now create users in your contained database without having a corresponding login in the master database. This makes it much simpler to move your database to another server. The connection code in your client applications is the same whether you use contained database users or not.<br/><br/>Use of this feature might be required for customers who want to benefit from higher guaranteed service level agreements.<br/><br/>We generally encourage customers to consider using this feature. However, some customers might have specific scenarios that make the traditional *login+user* pair the better choice for you at this time.<br/><br/>For more information, see:<br/>- [Azure SQL Database Security Guidelines and Limitations](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Managing Databases and Logins in Azure SQL Database](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [Contained Databases](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Table partitioning | Previous limitations on [table partitioning](http://msdn.microsoft.com/library/ms190787.aspx) are eliminated. |
| Larger transactions | With V12 you are no longer limited to a maximum of 2 GB of data modifications in a single transaction. <br/><br/> One benefit is that rebuilding a large index is no longer limited by 2 GB transaction size limit. For general information, see [Azure SQL Database Resource Limits](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Online index build and rebuild | Before V12, Azure SQL Database generally supported the (ONLINE=ON) clause of the ALTER INDEX statement, but this was not supported for indexes on a BLOB type column. Now V12 does support (ONLINE=ON) even for indexes on BLOB columns.<br/><br/> The ONLINE feature enables queries to benefit from an index even while the index is being rebuilt. |
| CHECKPOINT support | With V12 you can issue the T-SQL CHECKPOINT statement for your database. |
| ALTER TABLE enhancement | Allows many alter column actions to be performed while the table remains available. For more information, see [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| TRUNCATE TABLE enhancement | Allows truncation of specific partitions. For more information, see [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx). |
| More options on ALTER DATABASE | V12 supports more of the options that are available on the ALTER DATABASE statement. <br/><br/> For more information, see [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx) or [Azure SQL Database Transact-SQL Reference](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| More DBCC commands | Several more DBCC commands are now available in V12. For details see [Azure SQL Database Transact-SQL Reference](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


### 1.2 Category: Programming and application support


| Feature | Description |
| :--- | :--- |
| . | ***February 2015:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Dynamic data masking preview | When a rowset is generated from a query, an established data masking policy can replace parts of the data with 'X' characters to overlay and protect sensitive information. After the masking operation completes, the modified rowset is sent to the client.<br/><br/>One example use might be to mask all but the last few digits of a credit card number.<br/><br/>**NOTE:** This feature is at the preview status, and has not yet been announced for general availability for production use.<br/><br/>For detailed information, see [Get started with Azure SQL Database Dynamic Data Masking](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/). |
| . | ***January 2015:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Row-level security (RLS) preview | **Caution:** The RLS feature is presently at *preview* status only, even in geographic regions where V12 is in general availability (GA) status. Until RLS is in GA status, RLS is not yet appropriate for use in a business critical production database.<br/><br/>The new CREATE SECURITY POLICY statement in T-SQL enables you to implement RLS. RLS causes the database server to add conditions which filter out some data rows before a rowset is returned to the caller.<br/><br/>In the industry, RLS is sometimes also called fine-grained access control.<br/><br/>For a code example and more, see [Row-Level Security Preview](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx). |
| . | ***December 2014:*** |
| Window functions in T-SQL queries | The ANSI window functions are access with the [OVER clause](http://msdn.microsoft.com/library/ms189461.aspx). <br/><br/> Itzik Ben-Gan has an excellent [blog post](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) that further explains window functions and the OVER clause. |
| .NET CLR integration | The CLR (common language runtime) of the .NET Framework is integrated into V12. <br/><br/> Only SAFE assemblies that are fully compiled to binary code are supported. For details see [CLR Integration Programming Model Restrictions](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> You can find information about this feature in the following topics: <br/> * [Introduction to SQL Server CLR Integration](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Change tracking for data | Change tracking for data can now be configured at the database or table level. <br/><br/> For information about change tracking, see [About Change Tracking (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| XML indexes | V12 enables you use the T-SQL statements CREATE XML INDEX and CREATE SELECTIVE XML INDEX. <br/><br/> Information about XML indexes is available at: <br/> * [CREATE XML INDEX (Transact-SQL)](http://msdn.microsoft.com/library/bb934097.aspx) <br/> * [Create, Alter, and Drop Selective XML Indexes](http://msdn.microsoft.com/library/jj670109.aspx) |
| Table as a heap | V12 enables you to create a table that has no clustered index. <br/><br/> This feature is especially helpful for its support of the T-SQL SELECT...INTO statement which creates a table from a query result. |
| Application roles | For security and permissions control, V12 enables you to issue GRANT - DENY - REMOVE statements against [application roles](http://msdn.microsoft.com/library/ms190998.aspx). |


### 1.3 Category: Better customer insights


| Feature | Description |
| :--- | :--- |
| . | ***December 2014:*** |
| DMVs (dynamic management views) | Several DMVs are added in V12. For details see [Azure SQL Database Transact-SQL Reference](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Change tracking | V12 fully supports change tracking. <br/><br/> For details of this feature see [Enable and Disable Change Tracking (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |


### 1.4 Performance improvements at the Premium service tier


These performance enhancements apply to the P2 and P3 levels within the Premium service tier.


| Feature | Description |
| :--- | :--- |
| . | ***December 2014:*** |
| Parallel processing for queries | V12 provides a higher degree of parallelism for queries that can benefit from it. |
| Briefer I/O latency | V12 has significantly briefer latency for input/output operations. |
| Increased IOPS | V12 can process a larger quantity of input/output per second (IOPS). |
| Log rate | V12 can log more data changes per second. |


### 1.5 Summary of enhancements


V12 elevates our SQL Database close to full feature compatibility with our SQL Server product. V12 focuses on the most popular features, and on programmability support. This makes your development more efficient and more enjoyable.  It is now even easier to move your SQL database applications to the cloud.


And at the Premium tier V12 brings major performance improvements. Some applications will see extreme gains in query speed. Applications with heavy workloads will see reliably robust throughput.


## <a name="V12AzureSqlDbPreviewGaTable"></a>2. V12 general availability (GA) status per region


The V12 version of Azure SQL Database was released only for preview and testing in December 2014, in the regions indicated in the following table. The status change from preview to full general availability (GA) release is occurring on different dates depending on region. The following table displays the current release status of V12 for each region.


> [AZURE.NOTE]
> [Pricing](http://azure.microsoft.com/pricing/details/sql-database/) during the preview has been at a discount. Pricing returns to the GA level for all regions on March 31, 2015.


| Azure region | Current release<br/>status of V12 | Date of promotion<br/>to GA |
| :--- | :--- | :--- |
| South Central US | General Availability (GA) | February 9th, 2015 |
| Central US | General Availability (GA) | February 9th, 2015 |
| North Central US | General Availability (GA) | February 9th, 2015 |
| West US | General Availability (GA) | February 9th, 2015 |
| East US | General Availability (GA) | February 9th, 2015 |
| East US 2 | General Availability (GA) | February 9th, 2015 |
| East Asia | General Availability (GA) | February 24th, 2015 |
| Southeast Asia | General Availability (GA) | February 24th, 2015 |
| Japan West | General Availability (GA) | February 24th, 2015 |
| Japan East | General Availability (GA) | February 24th, 2015 |
| North Europe | General Availability (GA) | January 29th, 2015 |
| West Europe | General Availability (GA) | January 29th, 2015 |
| Brazil South | Not available | Third quarter 2015, estimated |
| Australia East | Preview | Second quarter 2015, estimated |
| Australia Southeast | Preview | Second quarter 2015, estimated |


For any region that has reached GA, all new subscriptions and their subsequent databases use the V12 service architecture and therefore have access to the latest features. The present article lists the new features brought by V12.


At GA, customers with pre-V12 servers and databases can elect to upgrade (or move) their databases to the V12 service architecture to use these new features for production. V12 databases must be at the basic, standard, or premium [pricing tier](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/).


## 3. How to proceed


You can learn how to upgrade your databases from Azure SQL Database V11 to V12 at [Plan and Prepare to Upgrade to the Latest Azure SQL Database Update Preview](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


Version numbers like V12 refer to the value returned by the following Transact-SQL statement:<br/>
**SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(or a bit higher, V12)*


*Discount:* During the V12 preview period you can use V12 at a discount price. The new S3 level delivers 100 DTUs (database throughput units) and all the features available in the Standard tier for $0.2016/hour. The price is discounted down to $0.1008/hour during the V12 preview. For details see [SQL Database Pricing](http://azure.microsoft.com/pricing/details/sql-database/).


## 4. Cautions for the V12 preview


Be aware of the following cautions regarding upgrade and post-upgrade to Azure SQL Database Update V12 (preview).


### 4.1 Preview portal for V12


Only the first of the following two Azure management portals supports V12 databases:


- [http://portal.azure.com/](http://portal.azure.com/)
 - This newer portal is at preview status and is not yet at General Availability (GA).<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - This older portal will not be updated to support V12.


We encourage customers to connect to their Azure SQL databases with Visual Studio 2013 (VS2013). VS2013 can be used for tasks such as the following:


- To run a T-SQL statement.
- To design a schema.
- To develop a database, either online or offline.


You can instead connect with [Visual Studio Community 2013](https://www.visualstudio.com/en-us/news/vs2013-community-vs.aspx/), which is a free and full-featured version of VS2013.


In the older Azure management portal, on the database page, you can click **Open in Visual Studio** to launch VS2013 on your computer for connection to your Azure SQL Database.


For another alternative, you can use SQL Server Management Studio (SSMS) 2014 with [CU6](http://support.microsoft.com/kb/3031047/) to connect to Azure SQL Database. More details are on this blog post:<br/>[Client tooling updates for Azure SQL Database](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### 4.2 Cautions *during* upgrade to V12


> [AZURE.NOTE]
> The pre-V12 database *remains available* for data access during the upgrade to V12.


- For databases larger than 50 GB, the upgrade to V12 can take up to 24 hours.
- During the upgrade of a database to a Azure SQL Database V12 server, you cannot perform the following actions on the server until the upgrade completes:
 - Create a new database.
 - Copy a database to the server.
 - Restore a deleted database.
 - Restore a database to a point in time.
 - Geo-replication.
- From the moment an upgrade to V12 completes, the system needs several minutes to update the domain name system (DNS) entry to your V12 database. Your client application can connect to your database after the DNS update occurs.
- The Web and Business service pricing tier is not supported on V12. Nor will it be supported in any future version.


### 4.3 Cautions *after* upgrade to V12


- Any database that is upgraded in-place to the V12 cannot be reverted back to the earlier version.
- The 50% cost discount for Azure SQL Database V12 is in effect in all geographic regions until it expires on Tuesday 2015-March-31. The discount applies to regions, for both preview and GA status.


### 4.4 Export and import *after* upgrade to V12


You can export or import a V12 database by using the [Azure web portal](http://portal.azure.com/). Or you can export or import by using any of the following tools:


- SQL Server Management Studio (SSMS) 2014
- Visual Studio 2013
- Data-Tier Application Framework (DacFx)


However, to use the tools, you must first install their latest updates to ensure they support the new V12 features:


- [Cumulative Update 6 for SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [February 2015 Update for SQL Server Database Tooling in Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [February 2015 Data-Tier Application Framework (DacFx) for Azure SQL Database V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] The preceding tool links were updated on or after March 2, 2015. We recommend that you use these newer updates of these tools.


[2. V12 general availability (GA) status per region]:#V12AzureSqlDbPreviewGaTable


<!-- EndOfFile -->

