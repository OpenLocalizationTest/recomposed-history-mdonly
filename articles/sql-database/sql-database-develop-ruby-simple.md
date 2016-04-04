<properties
	pageTitle="Connect to SQL Database by using Ruby with TinyTDS on Windows"
	description="Give a Ruby code sample you can run on Windows to connect to Azure SQL Database."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="meetb"/>


# Connect to SQL Database by using Ruby on Windows

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 

This topic presents a Ruby code sample that you can use to connect to Azure SQL Database. 

## Step 1: Configure Development Environment

[Prerequisites for using the TinyTDS Ruby Driver for SQL Server](https://msdn.microsoft.com/library/mt711041.aspx)

## Step 2: Create a SQL database

See the [getting started page](sql-database-get-started.md) to learn how to create a sample database.  It is important you follow the guide to create an **AdventureWorks database template**. The samples shown below only work with the **AdventureWorks schema**.

## Step 3: Get Connection Details

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Step 4: Run sample code

[Proof of Concept connecting to SQL using Ruby](http://msdnstage.redmond.corp.microsoft.com/library/mt715797.aspx)