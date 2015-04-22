<properties 
	title="Elastic database jobs overview" 
	pageTitle="Elastic database jobs overview" 
	description="Illustrates the elastic database job service" 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="sidneyh" />

# Elastic database jobs overview

The elastic job service (preview) enables you to run T-SQL scripts (jobs) against elastic any number of databases in an elastic database pool. For example, you can update the schema to every database in the deployment to include a new table. Normally, you must connect to each database in order to run TSQL statements or perform other tasks. The elastic database job service handles the task of logging in, and running the script for you, while logging the success of each step. 

![Elastic database job service][1]

## Benefits
* Define, maintain and persist T-SQL scripts to be executed across an elastic database pool
* Execute T-SQL scripts with automatic retry 
* Track job execution state

## Scenarios
* Update schemas reliably 
* Update reference data, for example product information common across all databases
* Run scripts to rebuild indexes. Configure to execute across a collection of databases on a recurring basis, such as during off-peak hours.
* Collect query results from a set of databases into a central table on an on-going basis. Execute performance queries continually and configure them to trigger additional tasks.



## How the job service works

1.	From the Elastic database pool view, click **Manage jobs**. 
2.	Type in the user name and password of a SQL Server login that will execute the script.
3.	Type the name of the job, and paste in or type the script.
4.	Click **Run** and the service executes the script against each database.
5.	A management view allows you to see all jobs running, or that have run. 
6.	Click any job to see its log of steps.
7.	If a job fails, click on its name  to see the error log.

## Service components

The following components work together to create a an Azure Cloud service that enables the execution of ad-hoc administrative jobs. The components are installed and configured automatically at setup, in your subscription. You can identify the services as they all have the same auto-generated name. The name is unique, and consists of the prefix "edj" followed by 21 randomly generated characters.

* **Azure Cloud Service**: elastic database jobs (preview) is delivered as a customer-hosted Azure Cloud service to perform execution of the requested tasks. From the portal, the service is deployed and hosted in your Microsoft Azure subscription. The default deployed service runs with the minimum of two worker roles for high availability. The default size of each worker role (ElasticDatabaseJobWorker) runs on an A0 instance. 
* **Azure SQL Database**: The service uses an Azure SQL Database known as the **control database** to hold metadata. The metadata about the elastic database pool allows the elastic job to log into each database and execute a script. The default service tier is a S0.
* **Azure Service Bus**: An Azure Service Bus is for coordination of the work within the Azure Cloud Service. 
* **Azure Storage**: An Azure Storage account is used to store diagnostic output logging in the event that an issue requires further debugging (a common practice for [Azure diagnostics](cloud-services-dotnet-diagnostics.md)).

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

