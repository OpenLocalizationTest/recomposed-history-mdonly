<properties linkid="mobile-services-sql-scale-guidance" urlDisplayName="Scale mobile services backed by Azure SQL Database" pageTitle="Scale mobile services backed by Azure SQL Database - Azure Mobile Services" metaKeywords="" description="Learn how to diagnose and fix scalability issues in your mobile services backed by SQL Database" metaCanonical="" services="" documentationCenter="Mobile" title="Scale mobile services backed by Azure SQL Database" authors="yavorg" solutions="" manager="" editor="mollybos" />
# Scale mobile services backed by Azure SQL Database

Azure Mobile Services makes it very easy to get started and build an app that connects to a cloud-hosted backend that stores data in a SQL database. As your app grows, scaling your service instances is as simple as adjusting scale settings in the portal to add more computational and networking capacity. However, scaling the SQL database backing your service requires some proactive planning and monitoring as the service receives more load. This document will walk you through a set of best practices to ensure continued great performance of your SQL-backed mobile services.

This topic walks you through these basic sections:

1. [Diagnosing Problems](#Diagnosing)
2. [Indexing](#Indexing)
3. [Schema Design](#Schema)
4. [Query Design](#Query)
5. [Service Architecture](#Architecture)
6. [Advanced Troubleshooting](#Advanced)

<a name="Diagnosing"></a>
## Diagnosing Problems

If you suspect your mobile service is experiencing problems under load, the first place to check is the **Dashboard** tab for your service in the [Azure Management Portal][]. Some of the things to verify here:
- Your usage meters including **API Calls** and **Active Devices** meters are not over quota
- **Endpoint Monitoring** status indicates service is up (only available if service is using the Standard tier and Endpoint Monitoring is enabled) 

If any of the above is not true, consider adjusting your scale settings on the *Scale* tab. If that does not address the issue, we can proceed and investigate whether Azure SQL Database may be the source of the issue. The next few sections cover a few different approaches to diagnose what may be going wrong.

### Choosing the Right SQL Database Tier 

It is important to understand the different database tiers you have at your disposal to ensure you've picked the right tier given your app's needs. Azure SQL Database offers two different database editions with different tiers:
- Web and Business Edition
- Basic, Standard, and Premium Edition (currently in preview)

While the Web and Business Edition is fully supported, it is being sunset by April 24, 2015 as discussed in [Web and Business Edition Sunset FAQ](http://msdn.microsoft.com/en-US/library/azure/dn741330.aspx). We encourage new customers to start using the Basic, Standard, and Premium preview in preparation for this change, if their application requirements allow it. 

#### Web and Business Edition

Currently this is the default edition used by Mobile Services. Here are some recommendations in selecting the tier for your database:
- **Free 20 MB database** - use for development purposes only 
- **Web and Business** - use for development and production services. The two tiers provide the same level of performance, however the Web tier only supports databases up to 5GB in size. For larger databases, use the Business tier. 

#### Basic, Standard, and Premium Edition

This new edition provides a variety of new tiers and monitoring capabilities that make it even easier to understand and troubleshoot database performance. To use this edition with your mobile service:

1. Navigate to the [Preview Features](https://account.windowsazure.com/previewfeatures) page and sign up for **New Service Tiers for SQL Databases**
2. Once the preview feature is active, launch the [Azure Management Portal][].
3. Select **+NEW** in the toolbar and then pick **Data Services**, **SQL Database**, **Quick Create**.
4. Enter a database name and then select **New SQL database server** in the **Server** field. This will create a server that is using the new Basic, Standard, and Premium Edition. 
5. Fill out the rest of the fields and select **Create SQL Database**. This will create a 100MB database using the Basic tier.
6. Configure your mobile service to use the database you just created
    - If you are creating a new mobile service, select **+NEW**, **Compute**, **Mobile Service**, **Create**. On the next screen, fill out the values, select **Use an existing SQL database** in the **Database** field, and then select **Next**. On the next screen be sure to pick the database you created in step 5, then select **OK**.
    - If you already have a mobile service, navigate to the **Configure** tab for that service and select **Change Database** in the toolbar. On the next screen, select **Use an existing SQL database** in the **SQL Database** field and then select **Next**. On the next screen be sure to pick the database you created in step 5, then select **OK**.

Here are some recommendations on selecting the right tier for your database:
- **Basic** - use at development time or for small production services where you only expect to make a single database query at a time
- **Standard** - use for production services where you expect multiple concurrent database queries
- **Premium** - use for large scale production services with many concurrent queries, high peak load, and expected low latency for every request.

For more information on when to use each tier, see [Reasons to Use the new Service Tiers](http://msdn.microsoft.com/en-US/library/azure/dn369873.aspx#Reasons)

### Analyzing Database Metrics

Now that we are familiar with the different database tiers, we can explore database performance metrics to help us reason about scaling within and among the tiers.

1. Launch the [Azure Management Portal][].
2. On the Mobile Services tab, select the service you want to work with.
3. Select the **Configure** tab.
4. Select the **SQL Database** name in the **Database Settings** section. This will navigate to the Azure SQL Database tab in the portal.
5. Navigate to the **Monitor** tab
6. Ensure the relevant metrics are displayed by using the **Add Metrics** button. Include the following
    - *CPU Percentage* (available only in Basic/Standard/Premium tiers)
    - *Physical Data Reads Percentage* (available only in Basic/Standard/Premium tiers) 
    - *Log Writes Percentage* (available only in Basic/Standard/Premium tiers)
    - *Storage* 
7. Inspect the metrics over the time window when your service was experiencing issues. 

    ![Azure Management Portal - SQL Database Metrics][PortalSqlMetrics]

If any metric exceeds 80% utilization for an extended period of time, this could indicate a performance problem. For more detailed information on understanding database utilization, see [Understanding Resource Use](http://msdn.microsoft.com/en-US/library/azure/dn369873.aspx#Resource).

If the metrics indicate your database is incurring high utilization, consider the following mitigation steps:
- **Scale up the database to a higher service tier.**
  To immediately resolve issues, consider using the **Scale** tab for your database to scale up your database. This will result in an increase in your bill.
    ![Azure Management Portal - SQL Database Scale][PortalSqlScale]

- **Tune your database.**
  It is frequently possible to reduce database utilization and avoid having to scale to a higher tier by optimizing your database. 
- **Consider your service architecture.**
  Frequently your service load is not distributed evenly over time but contains "spikes" of high demand. Instead of scaling the database up to handle the spikes, and having it go underutilized during periods of low demand, it is frequently possible to adjust the service architecture to avoid such spikes, or to handle them without incurring database hits.

The remaining sections of this document contain tailored guidance to help with implementing these mitigations.

### Configuring Alerts

It is frequently useful to configure alerts for key database metrics as a proactive step to ensure you have plenty of time to react in case of resource exhaustion. 

1. Navigate to the **Monitoring** tab for the database you want to set up alerts for
2. Ensure the relevant metrics are displayed as described in the previous section
3. Select the metric you want to set an alert for and select **Add Rule**
    ![Azure Management Portal - SQL Alert][PortalSqlAddAlert]
4. Provide a name and description for the alert
    ![Azure Management Portal - SQL Alert Name and Description][PortalSqlAddAlert2]
5. Specify the value to use as the alert threshold. Consider using **80%** to allow for some reaction time. Also be sure to specify an email address that you actively monitor. 
    ![Azure Management Portal - SQL Alert Threshold and Email][PortalSqlAddAlert3]

<a name="Indexing"></a>
## Indexing

When you start to see problems with your query performance, the first thing you should investigate is the design of your indexes. Indexes are important because they directly affect how the SQL engine executes a query. 

For instance, if you often need to look up an element by ID, you should consider adding an index for that column. Otherwise, the SQL engine will be forced to perform a table scan and read each physical record (or at least the query column) and the records could be substantially spread out on disk.

So, if you are frequently doing SELECT or JOIN statements on particular columns, you should make sure you index them. See the section [Creating Indexes](#CreatingIndexes) for more information.

If indexes are so great and table scans are so bad, does that mean you should index every column in your table, just to be safe?  The short answer is, "probably not." Indexes take up space and have overhead themselves: every time there is an insert in a table, the index structures for each of the indexed columns need to be updated. See below for guidelines on how to choose your column indexes.

### Index Design Guidelines

As mentioned above, it's not always better to add more indexes to a table, because indexes themselves can be costly, both in terms of performance and storage overhead.

#### Query considerations

- Consider adding indexes to columns that are frequently used in predicates (e.g., WHERE clauses) and join conditions, while balancing the database considerations below.
- Write queries that insert or modify as many rows as possible in a single statement, instead of using multiple queries to update the same rows. When there is only one statement, the database engine can better optimize how it maintains indexes.
	
#### Database considerations

Large numbers of indexes on a table affect the performance of INSERT, UPDATE, DELETE, and MERGE statements because all indexes must be adjusted appropriately as data in the table changes.

- For **heavily updated** tables, avoid indexing a large number of columns. For composite indexes, use as few columns as possible.
- For tables that are **not frequently updated** but that have large volumes of data, use many indexes. This can improve the performance of queries that do not modify data (such as SELECT statements) because the query optimizer will have more options for finding the best access method.

Indexing small tables may not be optimal because it can take the query optimizer longer to traverse the index searching for data than to perform a simple table scan. Therefore, indexes on small tables might never be used, but must still be maintained as data in the table changes.


<a name="CreatingIndexes"></a>
### Creating Indexes

#### JavaScript backend

To set the index for a column in the JavaScript backend, do the following:

1. Open your mobile service in the [Azure Management Portal][].
2. Click the **Data** tab.
3. Select the table you want to modify.
4. Click the **Columns** tab.
5. Select the column. In the command bar, click **Set Index**:

	![Mobile Services Portal - Set Index][SetIndexJavaScriptPortal]

You can also remove indexes within this view.

#### .NET backend

To define an index in Entity Framework, use the attribute `[Index]` on the fields that you want to index. For example:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }

		[Index]
        public bool Complete { get; set; }
    }
		 
For more information, see [Index Annotations in Entity Framework][].

<a name="Schema"></a>
## Schema Design

<a name="Query"></a>
## Query Design

<a name="Architecture"></a>
## Service Architecture

Imagine a scenario where you are about to send a push notification to all your customers to check out some new content in your app. As they tap on the notification, the app launches, which possibly triggers a call to your mobile service and a query execution against your SQL database. As potentially millions of customers take this action over the span of just a few minutes, this will generate a surge of SQL load, which may be orders of magnitude higher than your app's steady state load. This could be addressed by scaling your app to a higher SQL tier during the spike and then scaling it back down, however that solution requires manual intervention and comes with increased cost. Frequently slight tweaks in your mobile service architecture can significantly balance out the load clients drive to your SQL database and eliminate problematic spikes in demand. These modifications can often be implemented easily with minimal impact to your customer's experience. Here are some examples:
- **Spread out the load over time.** If you control the timing of certain events (for example, a broadcast push notification), which are expected to generate a spike in demand, and the timing of those events is not critical, consider spreading them out over time. In the example above, perhaps it is acceptable for your app customers to get notified of the new app content in batches over the span of a day instead of nearly simultaneously. Consider batching up your customers into groups which will allow staggered delivery to each batch. If using Notification Hubs, applying an additional tag to track the batch, and then delivering a push notification to that tag provides an easy way to implement this strategy.
- **Use Blob and Table Storage whenever appropriate.** Frequently the content that the customers will view during the spike is fairly static and doesn't need to be stored in a SQL database since you are unlikely to need relational querying capabilities over that content. In that case, consider storing the content in Blob or Table Storage. You can access public blobs in Blob Storage directly from the device. To access blobs in a secure way or use Table Storage, you will need to go through a Mobile Services Custom API in order to protect your storage access key. For more information, see [Upload images to Azure Storage by using Mobile Services](http://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/).
- **Use an in-memory cache**. Another alternative is to store data, which would commonly be accessed during a traffic spike, in an in-memory cache such as [Azure Cache](http://azure.microsoft.com/en-us/services/cache/). This means incoming requests would be able to fetch the information they need from memory, instead of repeatedly querying the database.

<a name="Advanced"></a>
## Advanced Troubleshooting
This section covers some more advanced diagnostic tasks, which may be useful if the steps so far have not addressed the issue fully.

### Prerequisites
To perform some of the diagnostic tasks in this section, you need access to a management tool for SQL databases such as **SQL Server Management Studio** or the management functionality built into the **Azure Management Portal**.

SQL Server Management Studio is a free Windows application, which offers the most advanced capabilities. If you do not have access to a Windows machine, consider provisioning a Virtual Machine in Azure as shown in [Create a Virtual Machine Running Windows Server](http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-tutorial/). If you intend to use the VM primarily for the purpose of running SQL Server Management Studio, a **Basic A0** (formerly "Extra Small") instance should be sufficient. 

The Azure Management Portal offers a built-in management experience, which is more limited, but is available without a local install.

The the following steps walk you through obtaining the connection information for the SQL database backing your mobile service and then using either of the two tools to connect to it. You may pick whichever tool you prefer.

#### Obtain SQL connection information 
1. Launch the [Azure Management Portal][].
2. On the Mobile Services tab, select the service you want to work with.
3. Select the **Configure** tab.
4. Select the **SQL Database** name in the **Database Settings** section. This will navigate to the Azure SQL Database tab in the portal.
5. Select **Set up Windows Azure firewall rules for this IP address**.
6. Make a note of the server address in the **Connect to your database** section, for example: *mcml4otbb9.database.windows.net*.

#### SQL Server Management Studio
1. Navigate to [SQL Server Editions - Express](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/sql-server-express.aspx)
2. Find the **SQL Server Management Studio** section and select the **Download** button underneath.
3. Complete the setup steps until you can successfully run the application:

    ![SQL Server Management Studio][SSMS]

4. In the **Connect to Server** dialog enter the following values
    - Server name: *server address you obtained earlier*
    - Authentication: *SQL Server Authentication*
    - Login: *login you picked when creating server*
    - Password: *password you picked when creating server*
5. You should now be connected.

#### Azure Management Portal
1. On Azure SQL Database tab for your database, select the **Manage** button 
2. Configure the connection with the following values
    - Server: *should be pre-set to the right value*
    - Database: *leave blank*
    - Username: *login you picked when creating server*
    - Password: *password you picked when creating server*
3. You should now be connected.

    ![Azure Management Portal - SQL Database][PortalSqlManagement]

### Advanced Diagnostics

#### SQL connectivity events

The **sys.event\_log** view contains the details of connectivity-related events. The columns contained in this view, and the types of events it collects, are described in [sys.event_log](http://msdn.microsoft.com/en-us/library/azure/jj819229.aspx).

    select * from sys.event_log 
    where database_name = 'my_user_db'
    and event_type like 'throttling%'
    order by start_time desc


    event_type                   event_count description
    ---------------------------- ----------- ----------------
    throttling_long_transaction  2           The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.


### Advanced Indexing

A table or view can contain the following types of indexes:

- **Clustered**. A clustered index specifies how records are physically stored on disk. There must be only one clustered index per table, because the data rows themselves can be sorted in only one order.

- **Nonclustered**. Nonclustered indexes are stored separately from data rows and are used to do a lookup based on the index value. All nonclustered indexes on a table use the key values from the clustered index as lookup key.

To provide a real-world analogy: consider a book or a technical manual. The contents of each page are a record, the page number is the clustered index, and the topic index in the back of the book is a nonclustered index. Each entry in the topic index points to the clustered index, the page number.

> [WACOM.NOTE] 
> By default, the JavaScript backend of Azure Mobile Services sets **\_createdAt** as the clustered index. If you remove this column, or if you want a different clustered index, be sure to follow the [clustered index design guidelines](#ClusteredIndexes) below. In the .NET backend, the class `EntityData` defines `CreatedAt` as a clustered index using the annotation `[Index(IsClustered = true)]`.

<a name="ClusteredIndexes"></a>
#### Clustered index design guidelines

Every table should have a clustered index on the column (or columns, in the case of a composite key) with the following properties:

- Narrow - uses a small datatype, or is a [composite key][Primary and Foreign Key Constraints] of a small number of narrow columns
- Unique, or mostly unique
- Static - value is not frequently changed
- Ever-increasing 
- (Optional) Fixed-width
- (Optional) nonnull

The reason for the **narrow** property is that all other indexes on a table use the key values from the clustered index as lookup keys. In the example of a topic index at the back of a book, the clustered index is a page number and is a small number. If the chapter title were instead included in the clustered index, then the topic index would itself be much longer, because the key value would then be (chapter name, page number).

The key should be **static** and **ever-increasing** to avoid having to maintain the physical location of the records (which means either moving records physically, or potentially fragmenting storage by splitting the pages where the records are stored). 

The clustered index will be most valuable for queries that do the following:

- Return a range of values by using operators such as BETWEEN, >, >=, <, and <=. 
	- After the row with the first value is found by using the clustered index, rows with subsequent indexed values are guaranteed to be physically adjacent. 
- Use JOIN clauses; typically these are foreign key columns.
- Use ORDER BY, or GROUP BY clauses.
	- An index on the columns specified in the ORDER BY or GROUP BY clause may remove the need for the Database Engine to sort the data, because the rows are already sorted. This improves query performance.

#### Creating clustered indexes in Entity Framework

To set the clustered index in the .NET backend using Entity Framework, set the `IsClustered` property of the annotation. For example, this is the definition of `CreatedAt` in `Microsoft.WindowsAzure.Mobile.Service.EntityData`:

	[Index(IsClustered = true)]
	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
	[TableColumnAttribute(TableColumnType.CreatedAt)]
	public DateTimeOffset? CreatedAt { get; set; }

#### Creating indexes in the database schema

For the JavaScript backend, you can only modify the clustered index of a table by changing the database schema directly, either through SQL Server Management Studio or the Azure SQL Database Portal.

The following guides describe how to set a clustered or nonclustered index by modifying the database schema directly:  

- [Creating and Modifying PRIMARY KEY Constraints][]
- [Create Nonclustered Indexes][]
- [Create Clustered Indexes][]
- [Create Unique Indexes][]


#### Find top N missing indexes 
You can write SQL queries on dynamic management views that will tell you more detailed information about the resource usage of individual queries or give you heuristics on what indexes to add. The following query determines which 10 missing indexes would produce the highest anticipated cumulative improvement, in descending order, for user queries.

    SELECT TOP 10 *
    FROM sys.dm_db_missing_index_group_stats
    ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)
    DESC;

The following example query runs a join across these tables to get a list of the columns that should be part of each missing index and calculates an 'index advantage' to determine if the given index should be considered:

    SELECT * from 
    (
        SELECT 
        (user_seeks+user_scans) * avg_total_user_cost * (avg_user_impact * 0.01) AS index_advantage, migs.*
        FROM sys.dm_db_missing_index_group_stats migs
    ) AS migs_adv,
      sys.dm_db_missing_index_groups mig,
      sys.dm_db_missing_index_details mid
    WHERE
      migs_adv.group_handle = mig.index_group_handle and
      mig.index_handle = mid.index_handle
      AND migs_adv.index_advantage > 10
    ORDER BY migs_adv.index_advantage DESC;

For more information, see [Monitoring SQL Database Using Dynamic Management Views][] and [Missing Index Dynamic Management Views](sys-missing-index-stats).

### Advanced Query Design 

#### Finding top N queries

The following example returns information about the top five queries ranked by average CPU time. This example aggregates the queries according to their query hash, so that logically equivalent queries are grouped by their cumulative resource consumption.

	-- Find top 5 queries
	SELECT TOP 5 query_stats.query_hash AS "Query Hash", 
	    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
	    MIN(query_stats.statement_text) AS "Statement Text"
	FROM 
	    (SELECT QS.*, 
	    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
	    ((CASE statement_end_offset 
	        WHEN -1 THEN DATALENGTH(st.text)
	        ELSE QS.statement_end_offset END 
	            - QS.statement_start_offset)/2) + 1) AS statement_text
	     FROM sys.dm_exec_query_stats AS QS
	     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
	GROUP BY query_stats.query_hash
	ORDER BY 2 DESC;

For more information, see [Monitoring SQL Database Using Dynamic Management Views][].

## See Also

- [Azure SQL Database Documentation][]
- [Azure SQL Database performance and scaling][]
- [Troubleshooting Azure SQL Database][]

### Indexing

- [Index Basics][]
- [General Index Design Guidelines][]
- [Unique Index Design Guidelines][]
- [Clustered Index Design Guidelines][]
- [Primary and Foreign Key Constraints][]
- [How much does that key cost?][]

### Entity Framework
- [Performance Considerations for Entity Framework 5][]
- [Code First Data Annotations][]

<!-- IMAGES -->
 
[SSMS]: ./media/mobile-services-sql-scale-guidance/1.png
[PortalSqlManagement]: ./media/mobile-services-sql-scale-guidance/2.png
[PortalSqlMetrics]: ./media/mobile-services-sql-scale-guidance/3.png
[PortalSqlScale]: ./media/mobile-services-sql-scale-guidance/4.png
[PortalSqlAddAlert]: ./media/mobile-services-sql-scale-guidance/5.png
[PortalSqlAddAlert2]: ./media/mobile-services-sql-scale-guidance/6.png
[PortalSqlAddAlert3]: ./media/mobile-services-sql-scale-guidance/7.png
[SetIndexJavaScriptPortal]: ./media/mobile-services-sql-scale-guidance/set-index-portal-ui.png
 
<!-- LINKS -->

[Azure Management Portal]: http://manage.windowsazure.com

[Azure SQL Database Documentation]: http://azure.microsoft.com/en-us/documentation/services/sql-database/
[Managing SQL Database using SQL Server Management Studio]: http://go.microsoft.com/fwlink/p/?linkid=309723&clcid=0x409
[Monitoring SQL Database Using Dynamic Management Views]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
[Azure SQL Database performance and scaling]: http://go.microsoft.com/fwlink/p/?linkid=397217&clcid=0x409
[Troubleshooting Azure SQL Database]: http://msdn.microsoft.com/en-us/library/azure/ee730906.aspx

<!-- MSDN -->
[Creating and Modifying PRIMARY KEY Constraints]: http://technet.microsoft.com/en-us/library/ms181043(v=sql.105).aspx
[Create Clustered Indexes]: http://technet.microsoft.com/en-us/library/ms186342(v=sql.120).aspx
[Create Unique Indexes]: http://technet.microsoft.com/en-us/library/ms187019.aspx
[Create Nonclustered Indexes]: http://technet.microsoft.com/en-us/library/ms189280.aspx

[Primary and Foreign Key Constraints]: http://msdn.microsoft.com/en-us/library/ms179610(v=sql.120).aspx
[Index Basics]: http://technet.microsoft.com/en-us/library/ms190457(v=sql.105).aspx
[General Index Design Guidelines]: http://technet.microsoft.com/en-us/library/ms191195(v=sql.105).aspx 
[Unique Index Design Guidelines]: http://technet.microsoft.com/en-us/library/ms187019(v=sql.105).aspx
[Clustered Index Design Guidelines]: http://technet.microsoft.com/en-us/library/ms190639(v=sql.105).aspx

[sys-missing-index-stats]: http://technet.microsoft.com/en-us/library/ms345421.aspx

<!-- EF -->
[Performance Considerations for Entity Framework 5]: http://msdn.microsoft.com/en-us/data/hh949853
[Code First Data Annotations]: http://msdn.microsoft.com/en-us/data/jj591583.aspx
[Index Annotations in Entity Framework]:http://msdn.microsoft.com/en-us/data/jj591583.aspx#Index

<!-- BLOG LINKS -->
[How much does that key cost?]: http://www.sqlskills.com/blogs/kimberly/how-much-does-that-key-cost-plus-sp_helpindex9/
