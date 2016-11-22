---

title: Load SQL Server to Azure SQL Data Warehouse – Data Factory | Microsoft Docs
description: This tutorial loads data from your on-premises SQL Server database into Azure SQL Data Warehouse by using Azure Data Factory.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: ''
tags: azure-sql-data-warehouse;azure-data-factory

ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: kevin;barbkess

---

# Use Azure Data Factory to load on-premises SQL Server tables into Azure SQL Data Warehouse

This tutorial loads data from your on-premises SQL Server database into Azure SQL Data Warehouse by using Azure Data Factory. When you’re finished, you have your SQL Server data in SQL Data Warehouse.

**Time estimate**: This tutorial takes about 10-15 minutes to complete once the prerequisites are met.

## Prerequisites

- The tutorial assumes you understand the basics of using Transact-SQL to create tables and schemas.  

- You need an Azure Storage Account. You can [open a free Azure account](/pricing/free-trial/?WT.mc_id=A261C142F) or [Activate Visual Studio subscriber benefits](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

- You need an online SQL Data Warehouse. If you do not already have a data warehouse, learn how to [Create an Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md). For best performance, locate the storage account and the data warehouse in the same Azure region.

- Prepare your data warehouse to receive the incoming data by creating one or more table schemas. You can use [SQL Data Warehouse Migration Utility](sql-data-warehouse-migrate-migration-utility.md) to create a script for the schemas. 

## Configure a new data factory
1. Log in to the [Azure portal][].
2. Locate your data warehouse and click to open it. 
3. In the **Properties** blade, click **Load Data > Azure Data Factory**.

    ![Launch Load Data wizard](media/sql-data-warehouse-load-from-sql-server-with-data-factory/launch-load-data-wizard.png)

4. You see a **New Data Factory** dialog box. Fill in the requested information, or choose an existing data factory. Click **Create**.

<!--
![Create a data factory](media/sql-data-warehouse-load-with-azure-data-factory/new-data-factory.png) 
-->

5. In the **Select Data Factory** dialog box, the **Load data** option is selected by default. Click **Next** to finish creating the data factory. 

<!--
![Create a data factory](media/sql-data-warehouse-load-with-azure-data-factory/select-data-factory.png)
-->


## Configure the data factory properties
Now that you have created a data factory, the next step is to configure the data loading schedule. 

1. Select **Properties** and fill in the requested information.
2. For **Task name**, enter **DWLoadData-fromSQLServer**.
2. Keep the **Run once now** option.
3. Click **Next**.

	![Configure load schedule](media/sql-data-warehouse-load-from-sql-server-with-data-factory/configure-load-schedule.png)

## Configure the data factory source and gateway
Now you tell Data Factory about the on-premises SQL Server database from which you want to load data.

1. Click **Source**.
2. Choose **SQL Server** from the supported source data store catalog, and click **Next**.

	![Choose SQL Server source](media/sql-data-warehouse-load-from-sql-server-with-data-factory/choose-sql-server-source.png)

2. A **Specify the on-premises SQL Server database** dialog appears. Fill in the required fields as follows:

<!-- ![Specify SQL Server connection](media/sql-data-warehouse-load-with-azure-data-factory/configure-connection.png) -->

- **Connection name**: Specify a new name for your connection.
- **Server name**: Name of the on-premises SQL Server.
- **Database name**: SQL Server database.
- **Credential encryption**: None. 
- **Authentication type**: Choose the type of authentication you are using.
- **User name** and **password**: Enter the user name and password for a user who has permission to copy the data.

1. The last field asks for the name of the Gateway. Click the **Create Gateway** link to create a Data Management Gateway. The gateway is a client agent that you must install in your on-premises environment to copy data between on-premises and cloud data stores. 

4. A **Create Gateway** dialog box appears. For Name, enter **GatewayForDWLoading**, and click **Create**.

<!--
![Create Data Management gateway](media/sql-data-warehouse-load-with-azure-data-factory/create-gateway.png) 
-->

5. A **Configure Gateway** dialog box appears.  
	![Launch Express setup](media/sql-data-warehouse-load-from-sql-server-with-data-factory/launch-express-setup.png)

6. Click **Launch express setup on this computer** to download, install, and register the gateway on your current machine. The progress is shown in a pop-up window.

	The express setup works natively with Microsoft Edge and Internet Explorer. If you are using Google Chrome, first install the ClickOnce extension from Chrome web store. Alternatively, you can manually download and install the gateway, then use the key to register.

6. Wait for the gateway setup to complete. Once the gateway is successfully registered and is online, the pop-up window closes and the new gateway appears in the gateway field. Click **Next**.

<!-- ![Configure gateway connection](media/sql-data-warehouse-load-with-azure-data-factory/configure-gateway-connection.png) 
-->

8. The next step is to choose the tables from which to copy the data. You can filter the tables by using keywords. And you can preview the data and table schema in the bottom panel. After you finish your selection, click **Next**.

	![Select tables](media/sql-data-warehouse-load-from-sql-server-with-data-factory/select-tables.png)

## Configure the destination, your SQL Data Warehouse

1. Click **Destination**. Your SQL Data Warehouse connection information is filled in automatically.
2. Enter the password for the user name. and click **Next**.

	![Configure destination](media/sql-data-warehouse-load-from-sql-server-with-data-factory/configure-destination.png)

1. An intelligent table mapping appears that maps source to destination tables based on similar names. Ad
2.  the mapping for any table, and the rest is automatically mapped by learning from the example. 
2. Review and click **Next**.

	![Map tables](media/sql-data-warehouse-load-from-sql-server-with-data-factory/table-mapping.png)

1. Review the schema mapping and look for error messages. Intelligent mapping is based on column name. If there is an unsupported data type conversion between the source and destination column, you see an error message alongside the corresponding table.

	![Map schema](media/sql-data-warehouse-load-from-sql-server-with-data-factory/schema-mapping.png)

1. Click **Next**.

## Configure the performance settings
In the Performance configurations, you configure an Azure storage account to use for staging the data before it loads into SQL Data Warehouse.

1. Click **Performance**. 
2. Select an existing Azure Storage account, and click **Next**.

	![Configure staging blob](media/sql-data-warehouse-load-from-sql-server-with-data-factory/configure-staging-blob.png)

## Review summary information and deploy the pipeline

1. Click **Summary** and review the information.
2. Click **Finish** button to deploy the pipeline.

	![Deploy data factory](media/sql-data-warehouse-load-from-sql-server-with-data-factory/deploy-data-factory.png)

## Monitor data loading progress

After the deployment is complete, a **Deployment** option appears on the left-hand menu. 

1. Click **Deployment**.
2. To monitor data loading progress, click the link that says **Click here to monitor copy pipeline**.

	![Monitor pipeline](media/sql-data-warehouse-load-from-sql-server-with-data-factory/monitor-pipeline.png)

1. In the **Resource Explorer**, expand the pipelines nodes and click the **DWLoadData-fromSQL
2. Server** data loading pipeline that you created in this tutorial.

	![View pipeline](media/sql-data-warehouse-load-from-sql-server-with-data-factory/view-pipeline.png)

1. Click into the pipeline to see the detailed status for each table that maps to an Activity.

	![View table activity](media/sql-data-warehouse-load-from-sql-server-with-data-factory/view-table-activity.png)

1. Further click into an activity and you see the data loading details in the right panel including data size, rows, throughput, etc.

	![View table activity details](media/sql-data-warehouse-load-from-sql-server-with-data-factory/view-table-activity-details.png)

1. To re
2. launch this monitoring view later, go to your SQL Data Warehouse, click **Load Data > Azure Data Factory**, select your factory, and choose **Monitor existing loading tasks**.

<!-- ![Monitor pipeline](media/sql-data-warehouse-load-with-azure-data-factory/monitor-pipeline.png)
-->

## Next steps

To migrate your database to SQL Data Warehouse, see [Migration overview](sql-data-warehouse-overview-migrate.md).

To learn more about Azure Data Factory copy capabilities, see [Introduction to Azure Data Factory](../data-factory/data-factory-introduction.md) and [Move data by using Copy Activity](../data-factory/data-factory-data-movement-activities.md).

To explore your data in SQL Data Warehouse, see [Connect to SQL Data Warehouse with Visual Studio and SSDT](sql-data-warehouse-query-visual-studio.md) and [Visual data with Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

<!-- Azure references -->
[Azure portal]: https://portal.azure.com 