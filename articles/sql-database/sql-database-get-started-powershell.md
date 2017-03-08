---
title: 'Azure PowerShell: Create a single SQL database | Microsoft Docs'
description: Learn how to create a SQL Database logical server, server-level firewall rule, and databases in the Azure portal.
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
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: carlrab
---

# Create a single Azure SQL database using PowerShell

PowerShell is used to create and manage Azure resources from the command line or in scripts. This guide details using PowerShell to deploy an Azure SQL database in an Azure resource group in a SQL Database logical server.

Before you start, make sure that the latest version of PowerShell is installed. Azure CLI has been installed. For detailed information, see [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs). 

## Log in to Azure

Log in to your Azure subscription with the [Add-AzureRmAccount](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) command and follow the on-screen directions.

```powershell
az login
```

## Create a resource group

Create a resource group with the [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) command. An Azure resource group is a logical container into which Azure resources are deployed and managed. The following example creates a resource group named `myResourceGroup` in the `northcentralus` location.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "northcentralus"
```
## Create a logical server in the resource group

Create a SQL Database logical server with the [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) command. The following example creates a randomly-named server in your resource group with an admin login named `ServerAdmin` and a password of `ChangeYourAdminPassword1`. Replace these pre-defined values as desired.

```powershell
$servername = "server-$($(Get-AzureRMContext).Subscription.SubscriptionId)"
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "northcentralus" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## Configure a firewall rule for the server

Create a SQL Database server-level firewall rule with the [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule) command. A server-level firewall rule allows an external application, such as SQL Server Management Studio or the SQLCMD utility to connect to a SQL database through the SQL Database service firewall. The following example creates a firewall rule for a predefined address range, which, in this example, and the entire possible range of IP address. Replace these predefined values with the values for your external IP address or IP address range. 

```powershell
$startip = "0.0.0.0"
$endip = "255.255.255.255"
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

## Create a blank database in the server

Create a blank SQL database with an S0 performance level in the server with the [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) command. The following example creates a database called `mySampleDatabase`. Replace this predefined value as desired.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## Clean up resources

To remove all the resources created by this QuickStart, run the following command:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## Next Steps

- To connect and query using SQL Server Management Studio, see [Connect and query with SSMS](sql-database-connect-query-ssms.md)
- To connect using Visual Studio, see [Connect and query with Visual Studio](sql-database-connect-query.md).
- To create a single database with Azure PowerShell, see [Create a single database using PowerShell](sql-database-get-started-powershell.md).
- To create a single database Azure CLI, see [Create a single database using CLI](sql-database-get-started-cli.md).
- For a getting started with SQL Server authentication tutorial, see [SQL authentication and authorization](sql-database-control-access-sql-authentication-get-started.md).
* If you're ready to start coding, choose your programming language at [Connection libraries for SQL Database and SQL Server](sql-database-libraries.md).
* If you want to move your on-premises SQL Server databases to Azure, see [Migrating a database to SQL Database](sql-database-cloud-migrate.md).
* For a technical overview of SQL Database, see [About the SQL Database service](sql-database-technical-overview.md).
