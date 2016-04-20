<properties
   pageTitle="Create SQL Data Warehouse by using Powershell | Microsoft Azure"
   description="Create SQL Data Warehouse by using Powershell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Create SQL Data Warehouse using Powershell

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

### Prerequisites
Before starting, be sure you have the following prerequisites.

- A V12 Azure SQL Server to host the database
- Know the resource group name for the SQL Server.

## Creating a SQL Data Warehouse database

> [AZURE.NOTE]  In order to use Azure PowerShell with SQL Data Warehouse, you will need to install Azure PowerShell version 1.0.3 or greater.  You can check your version by running **Get-Module -ListAvailable -Name Azure**.  The latest version can be installed from  [Microsoft Web Platform Installer](http://aka.ms/webpi-azps).  For more information on installing the latest version, see [How to install and configure Azure PowerShell](powershell-install-configure.md).

1. Start Windows PowerShell by clicking Start and typing **Windows PowerShell**.
2. Run this cmdlet to login to Azure Resource Manager.
	```Powershell
	Login-AzureRmAccount
	```
3. Select the subscription you want to use for your current session.
	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```
4.  Create database. This example deploys a new database named "mynewsqldw1", with service objective  level "DW400", to the server named "sqldwserver1" which is in the resource group named "mywesteuroperesgp1".
	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw1" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

	The parameters required for this cmdlet are:

	- **RequestedServiceObjectiveName**: The amount of DWU you are requesting, in the form "DWXXX" currently supported values are: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
 	- **DatabaseName**: The name of the SQL Data Warehouse that you are creating.
 	- **ServerName**: The name of the server that you are using for creation (must be V12).
 	- **ResourceGroupName**: Resource group you are using.  To find available resource groups in your subscription use Get-AzureResource.
 	- **Edition**: You must set edition to "DataWarehouse" to create a SQL Data Warehouse.

For the command reference, see [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx)

For the parameter options, see [Create Database (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx).

## Next steps
After your SQL Data Warehouse has finished provisioning you can [load sample data][] or check out how to [develop][], [load][], or [migrate][].

If you're interested in more on how to manage SQL Data Warehouse programmatically, check out our [Powershell][] or [REST API][] documentation.

<!--Image references-->

<!--Article references-->
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[load sample data]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Powershell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[REST API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]:https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: ../sql-database/sql-database-configure-firewall-settings.md
