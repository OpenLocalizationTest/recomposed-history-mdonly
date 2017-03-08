---
title: 'Azure portal: Create and query a single SQL database | Microsoft Docs'
description: Learn how to create a SQL Database logical server, server-level firewall rule, and databases in the Azure portal. You also learn to query an Azure SQL database using the Azure portal and using SQL Server Management Studio.
keywords: sql database tutorial, create a sql database
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: carlrab

---
# Create a single Azure SQL database in the Azure portal and then query the SQL database

Azure SQL databases can be created through the Azure portal. This quick start details the portal deployment experience. You also use the [newest version of SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) to connect to and query the new SQL database.

## Create a database

Follow these steps to create a database with sample data in a server and a resource group. 

1. Log in to the [Azure portal](https://portal.azure.com/).
2. Click the **New** button.

      ![new](./media/sql-database-get-started/new.png)

3. Select **Databases** from the Marketplace screen, select **SQL databases** from the featured apps screen.

4. Fill out the form with the required information (the **Server** name must be globally unique). 

      ![create sql database2](./media/sql-database-get-started/create-database2.png)

5. Click **Create** when complete. Provisioning takes a few minutes.
6. Once the SQL database deployment has finished, select the **SQL databases** icon in the left-hand section of the Azure dashboard and click your new database on the **SQL databases** page. THis opens the Azure dashboard page for your database, showing you the fully qualified server name (such us **newserver0308.database.windows.net**) and provides options for further configuration.

      ![new sql database](./media/sql-database-get-started/sql-database.png) 

## Create a server-level firewall rule

Follow these steps to create a server-level firewall rule for your IP address to enable external connectivity to the database and to its server. 

1. Click **Set server firewall** on the toolbar for your database. This opens the **Firewall settings** page for the SQL Database server. 

2. Click **Add client IP** on the toolbar and then click **Save**. This creates a server-level firewall rule for your current IP address.

3. Click **OK** and then click the **X** to close the Firewall settings page. This returns you to the SQL database page for the next step.

You can now connect to the database and its server using SQL Server Management Studio or another tool of your choice.

## Query the database

Follow these steps to query the database on the Azure dashboard. 

1. Click **Tools** on the toolbar for your database. This opens the **Query editor** page.

2. Click **Query editor (preview)**, click the preview checkbox, and then click **OK**. 

3. Click **Login** and then, when prompted, select **SQL server authentication** and provide the server admin login and password, and click **OK**.

4. After you are authenticated, type a query of your choice in the query window, such as the following query:

   ```
   SELECT * FROM sys.objects;
   ```

5. Click **Run** and then review the query results in the **Results** pane.

    ![query editor results](./media/sql-database-get-started/query-editor-results.png)

6. Click the **X** to close the Query editor page. This returns you to the SQL database page.

## Query the database using SQL Server Management Studio

Follow these steps to connect to your database using the [newest version of SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS).

1. Open SSMS.
2. In the **Connect to Server** dialog box, enter the appropriate information to connect to your SQL Database server using SQL Server Authentication. Enter your **Server name** in the form of **<server name).database.window.net**. 

    ![ssms connect](./media/sql-database-get-started/ssms-connect.png)

3. After you connect Object Explorer to your SQL Database server, expand **Databases** and then expand your new database. This opens a connection your database.

   ![ssms object explorer](./media/sql-database-get-started/ssms-object-explorer.png)

4. Click **New Query** on the toolbar. This opens a new query window connected to your SQL database instance.

5. In the query window, type a query of your choice in the query window, such as the following query:

   ```
   SELECT * FROM sys.objects;
   ```

6. On the query window toolbar, click **Execute** and then review the query results in the **Results** pane.

   ![ssms object explorer](./media/sql-database-get-started/query-results-ssms.png)

## Troubleshoot connectivity

You receive error messages when the connection to Azure SQL Database fails. The connection problems can be caused by SQL Azure database reconfiguration, firewall settings, connection time-out, or incorrect login information. For a connectivity troubleshooter tool, see [Troubleshooting connectivity issues with Microsoft Azure SQL Database](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database).

## Clean up resources

The steps in this procedure show how to delete all resources created by this quick start in the Azure portal.

1. On the Azure dashboard, click **Resource groups** and then click **myResourceGroup**. 
2. On the page for your resource group, click **Delete**, type **myResourceGroup** in the text box and then click **Delete**.

## Next steps

- To connect using Visual Studio, see [Connect and query with Visual Studio](sql-database-connect-query.md)
- For a getting started with SQL Server authentication tutorial, see [SQL authentication and authorization](sql-database-control-access-sql-authentication-get-started.md)
* If you're ready to start coding, choose your programming language at [Connection libraries for SQL Database and SQL Server](sql-database-libraries.md).
* If you want to move your on-premises SQL Server databases to Azure, see [Migrating a database to SQL Database](sql-database-cloud-migrate.md).
* For a technical overview of SQL Database, see [About the SQL Database service](sql-database-technical-overview.md).
