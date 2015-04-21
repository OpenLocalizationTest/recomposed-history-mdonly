<properties 
	pageTitle="Creating and managing elastic database jobs" 
	description="Walk through creation and management of an elastic database job." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh"/>

# Creating and managing elastic database jobs

The elastic database pool service provides a predictable model for deploying large numbers of databases. You can set minimum the Data Throughput Units (DTUs) for each database at a set cost. Managing these databases can be most easily accomplished using the **elastic database job service**. The service allows you to run scripts against each database elastic database pool. For example, you can set the policy on each database to allow only a person with the right credentials to view sensitive data. Here's how to use the service.


## Prerequistes

* An Azure subscription. For a free trial, see [Free one-month trial](http://azure.microsoft.com/pricing/free-trial/).
* An elastic database pool. See [About Elastic database pools](sql-database-elastic-pool.md)
* Installation of elastic database job service components. See [Installing the elastic database job service](vsql-database-elastic-jobs-service-installation.md).

## Creating jobs

1. In the elastic database job pool blade, click **Create job**.
2. In the **Create Job** blade, type a name for the job.
3. Paste or type in the T-SQL script.
4. Click **Save** and then click **Run**.

	![Name the job, type or paste in code, and click Run][1]

## Run idempotent jobs

When you run a script against a set of databases, you must be sure that the script is idempotent. That is, the script must be able to run multiple times, even if it has failed before in an incomplete state. For example, when a script fails, the job will be automatically retried until it succeeds (within limits, as the retry logic will eventually cease the retrying). The way to do this is to use the an "IF EXISTS" clause and delete any found instance before creating a new object. An example is shown here:

	IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
	GO
	CREATE INDEX IX_ProductVendor_VendorID 
    ON Purchasing.ProductVendor (VendorID);


## Checking job status

After a job has begun, you can check on its progress. 

1. From the elastic database pool page, click **Manage jobs**.

	![Check other jobs from elastic database pool][3]

2. Click on the name (a) of the running job. The job's page appears (b). The list (c) below the description shows the progress of the script against databases in the pool.

![Checking a running job][2]

To check on an job that has finished running, from the elastic database job pool, click **Manage jobs**. Then click on the name of any job to view its details. 

![Check other jobs from elastic database pool][3]

## Checking failed jobs

If a job fails, a log of its execution can found. Click the name of the failed job to see its details.

![Check a failed job][4]

## Next steps

To understand the job creation, see "Creating an elastic database job"

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png


