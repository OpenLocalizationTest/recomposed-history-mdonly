<properties
   pageTitle="Restore a database in Azure SQL Data Warehouse (Portal) | Microsoft Azure"
   description="Azure portal tasks for restoring a live or deleted database in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/10/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Restore a database in Azure SQL Data Warehouse (Portal)

> [AZURE.SELECTOR]
- [Overview][]
- [Portal][]
- [PowerShell][]
- [REST][]

PowerShell tasks for restoring a live or deleted database in Azure SQL Data Warehouse. 

## Before you begin

Verify your SQL Database DTU capacity. Since SQL Data Warehouse restores to a new database on your logical SQL server, it is important to make sure the SQL server you are restoring to has enough DTU capacity for the new database. See this blog post for more information on [how to view and increase DTU quota][].


## Restore a live database

To restore a database:

1. Log in to the [Azure portal][].
2. On the left side of the screen select **BROWSE** and then select **SQL Databases**.
3. Navigate to your database and select it.
4. At the top of the database blade, click **Restore**.
5. Specify a new **Database name**, select a **Restore Point** and then click **Create**.
6. The database restore process will begin and can be monitored using **NOTIFICATIONS**.


## Restore a deleted database

To restore a deleted database:

1. Log in to the [Azure portal][].
2. On the left side of the screen select **BROWSE** and then select **SQL Servers**.
3. Navigate to your server and select it.
4. Scroll down to Operations on your server's blade, click the **Deleted Databases** tile.
5. Select the deleted database you want to restore.
5. Specify a new **Database name** and click **Create**.
6. The database restore process will begin and can be monitored using **NOTIFICATIONS**.


## Next steps
For more information, see [Azure SQL Database business continuity overview][] and [Management overview][].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ./sql-database-business-continuity.md
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->

<!--Blog references-->
[how to view and increase DTU quota]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/

