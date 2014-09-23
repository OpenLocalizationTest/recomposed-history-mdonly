<properties title="Scripting Elastic Scale with Scripts" pageTitle="Scripting Elastic Scale with Scripts" description="Scripting Elastic Scale with Scripts, powershell, elastic scale" metaKeywords="Azure SQL Database, elastic scale, powershell scripts" services="sql-database" documentationCenter="sql-database" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Managing Elastic Scale with Scripts


## Azure Automation Service 

Recently introduced as a public preview service, **Azure Automation** brings a powerful, much needed PowerShell Workflow execution service to the Azure platform.  Those once difficult maintenance tasks are now possible to automate, and conveniently encapsulated within the common Azure portal experience.  Simply author a PowerShell Workflow (called a **runbook** in Azure Automation), upload it to the cloud, and schedule when you want the runbook to execute. This document provides thorough details on the end-to-end setup of Azure Automation for a handful of shard elasticity examples. 

In the context of this example, Azure Automation will be utilized as the schedule and workload execution framework – think of Azure Automation as Your SQL Agent in the Cloud. 

In addition to the detailed steps provided in this document there have been a few getting started guides for Azure Automation to help supplement the content.  If you have any Azure Automation specific questions, please post them here.  


## Prerequisites

Sign-up [link] and familiarize [link] yourself with the Microsoft Azure Automation Preview service 


## Shard Elasticity PowerShell Files  

The following set of PowerhShell files provide the primitives in order to accomplish both of the previously described horizontal and vertically scaling scenarios using Azure Automation. 

To facilitate both horizontal and vertical elasticity, the provided examples illustrate how to use the Shard Elasticity with PowerShell sample module, Microsoft Azure Automation service and corresponding Azure Automation runbooks to create automated and scheduled jobs that can provision a new shard and/or change the performance level (i.e., edition) of specific shards in a shard set based on a set of rules. 

**SetupShardedEnvironment.ps1** - This PowerShell runbook performs a one-time setup of a sharded environment complete with a shard map manager and range shard map. 

**ProvisionShardByDate.ps1** - This script provisions a new database in advance of the upcoming day's workload. The database is created and named based on date stamp (YYYYMMDD) and is registered with the shard map manager as a range [YYYYMMDD, YYYYMMDD + 1D). 

**ProvisionShardBySize.ps1** – Similar to ProvisionShardByDate.ps1, but instead provisions a new database when the current database is running out of capacity. 

**ReduceServiceTier.ps1** - This script iterates through the shards in a provided shard map and determines if each individual shard is a candidate for performance tier reduction (i.e., change a database from Standard to Basic).  There are two criteria that determine whether or not the shard is a candidate 1) current service tier of the shard and 2) the age of the database.  In this implementation, the current DB service tier is 'Standard' and the desired database SKU when the DB is >= 1 day old is 'basic'. 

**ShardManagement.psm1** - Provides a set of methods to interact with Elastic Scale shard map manager. 

**SqlDatabaseHelpers.psm1** - Provides a set of methods to interact with Azure SQL Database libraries. 

**ShardElasticity.psm1** - Provides a set of methods to perform horizontally scaling as well as vertically scaling operations. 

ShardElasticity.psd1 - Provides a set of methods to interact with Elastic Scale and Azure SQL DB libraries. 

## Costs

Note that the execution of the PowerShell example scripts will result in the creation of databases that will incur real costs to the subscription owner. The underlying Azure SQL DBs will be charged at a rate no different than any other Azure SQL DB database.  The costs starting November 1 are: 

* SetupShardedEnvironment runbook creates the shard map manager on a Basic database ($0.0069/hour) and provisions the first shard on a Basic database ($0.0069/hour) as well. 

* Both ProvisionBySize and ProvisionByDate runbooks will provision a Standard S0 database ($0.0208/hour).  To counter act these costs, if run in conjunction with the ReduceServiceTier runbook, the service tier of the newly provisioned database will be reduced from a Standard S0 ($0.0208/hour) to a Basic ($0.0069/hour) after a day’s time. 

Lastly, within the scope of the provided examples, the use of Azure Automation currently will not incur any charges to the subscription owner.  Please see the Azure Automation pricing page for more details. 



## Quick Example Instructions 

To execute the shard elasticity scenario mentioned above, below are the detailed steps to exercise the provided example code: 

1. Download the “ShardElasticity.zip” file and extract contents.
2. Via the Elastic Scale NuGet feed, obtain the Elastic Scale client DLL (i.e., Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll), place the DLL in the ShardElasticityModule folder and then zip the folder. 
3. In your Azure Automation account, upload the ShardElasticityModule.zip file as an Asset. 
4. Continuing in Azure Automation, create a Asset Credential entitled “ElasticScaleCredential” that contains the username and password for your Azure SQL Database server.  Additionally, create a Asset Variable entitled “SqlServerName” for your fully-qualified Azure SQL Database server name. 
5. Upload the “SetupShardedEnvironment.ps1”, “ProvisionBySize.ps1”, “ProvisionByDate.ps1”, “ProvisionByDate.ps1” files as runbooks. 
6. As a one-time operation, test the “SetupShardedEnvironment.ps1” runbook to setup the sharded environment. 
7. Publish one or more of the remaining runbooks and link the runbook(s) to a schedule. 
8. Observe the output of the runbook via the “JOBS” tab. 

If the Quick Example Instructions were not successful, please see the Detailed Example Instructions below.  

## Detailed Example Instructions 

To execute the shard elasticity scenario mentioned above, below are the detailed steps to exercise the provided example code: 

1. Author and package a PowerShell module 
2. Create a Microsoft Azure Automation Account 
3. Upload PowerShell module to Azure Automation as an Asset 
4. Create Azure Automation credential and variable Assets 
5. Upload PowerShell runbooks to Azure Automation 
6. Setup a sharded environment 
7. Test the Automation runbooks 
8. Publish the runbooks 
9. Schedule the runbooks 


## Author and Package a PowerShell Module 

The first step is to create a PowerShell module that references the Elastic Scale assemblies and package this module so that it is ready to be uploaded to the Azure Automation Service as an asset. 

1. Download the “ShardElasticity.zip” file.
2. Extract all content.

	![Extract all][1]
3. Obtain the Elastic Scale client DLL (i.e., Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll) and copy the following files into your local “ShardElasticityModule” folder that was downloaded in step a.  This can be done in two ways: 1) download the DLL via the Elastic Scale NuGet package [link] or 2) from your Elastic Scale Starter Kit project [link] (must be built), go to \bin\Debug\ to obtain the DLL.

	![Obtain Dll][2]

4. Zip the ShardElasticityModule folder. 

	Note: Azure Automation requires several name conventions: given the module name ShardElasticityModule.psm1, the zip file name must match exactly (ShardElasticityModule.zip). The zip file contains the folder ShardElasticityModule (name matching name of module), which in turn contains psm1 file. If this structure is not followed, Azure Automation will not be able to unpack the module.

5.	Once you have verified that the contents and structure of the zipped folder match requirements, proceed to the next step. It should resemble this:

	![dll][3]


## Create a Microsoft Azure Automation Account 

The second step is to create an Azure Automation Account. 

1. Go to[ Microsoft Azure portal](https://manage.windowsazure.com/microsoft.onmicrosoft.com).
2. Click on **Automation**.

	![Automation][4]
3. In the bottom-left hand corner of the screen click **New**. 
4. In the prompt shown below, please enter a valid account name and click the check in the bottom right-hand corner of the box.

	![Prompt][5] 
5. Proceed to the next step.

## Upload PowerShell module to Azure Automation as an Asset 

The third step is to upload the PowerShell module from Step 1 to the Azure Automation Account created in Step 2. For example, the module contains a set of Shard Elasticity functions and Elastic Scale .dlls that can be referenced from the runbooks. 

1. Click **ASSETS** in the ribbon on the top of the screen.
2. Click **IMPORT MODULE** at the bottom of the page. 
3. Click **BROWSE FOR FILE…**, and locate the “ShardElasticityModule.zip” file from above. 
4. Once the correct file has been chosen, click the check in the bottom right-hand corner of the box to import it. Azure Automation service imports the module. 
5. Proceed to the next step. Success resembles this graphic. If module was not imported successfully, please ensure that zip file matches conventions described above.

	![Success][6] 

	  
## Create Azure Automation Credential and Variable Assets 

Instead of hard coding credentials and commonly used variables into the runbooks, Azure Automation provides the facility to create credential and variable assets respectively that can be referenced across many runbooks.  Changing a password happens in one location. 

1. Under the ShardElasticityExamples account, click **ASSETS** in the ribbon .
2. Click **ADD SETTING** at the bottom of the screen.  
3. Click **ADD CREDENTIAL**. 
4. Select **Windows PowerShell Credential** as the **CREDENTIAL TYPE** and **ElasticScaleCredential** as the Name. A description is optional.  
5. Click the arrow in the bottom right-hand corner of the box. 

	Note: To use the runbooks without modification, use the variable names verbatim, as provided in the instructions. The variable names are referenced by the runbooks. 
5. Insert the user name and password (twice) for the Azure SQL DB server on which you wish to run the Shard Elasticity examples. Click the check when finished. 
6. To create the variable asset, repeat step 3, but instead select **ADD VARIABLE**. 

	![Add variable][7]
7. For this tutorial, create a variable for the fully-qualified Azure SQL DB server name under which the shard map manager and sharded databases will reside. Select **String** as the **VARIABLE TYPE** and enter **SqlServerName**. Click the arrow to proceed. 
8. Enter the fully-qualified Azure SQL DB server name as the VALUE and click on the check. 
9. You have now created both a credential and variable asset that will be used in the Shard Elasticity runbooks.  Proceed to the next step. Success looks like the following: 

## Upload PowerShell runbooks to Azure Automation 

Upload the four example PowerShell runbooks provided. 

1. To upload a new Azure Automation Runbook, click **RUNBOOKS** in the ribbon. 
2. At the bottom of the screen, click **IMPORT**. 
3. Navigate to the folder holding the file, and select **SetupShardedEnvironment.ps1**, and click the check mark. 
4. Repeat steps 2 and 3for the three remaining PowerShell runbooks (**ProvisionByDate.ps1**, **ProvisionBySize.ps1**, and **ReduceServiceTier.ps1**). 
5. Proceed to the next step. 

## Setup a Sharded Environment 

The next step is to execute the **SetupShardedEnvironment** runbook which provisions a sharded environment, complete with a shard map manager DB, a range shard map, and a shard for the current day.   

1. Select the **SetupShardedEnvironment** runbook by clicking on its name. 
2. Click **AUTHOR** on the ribbon. 
3. From this screen, you will see the code (and be able to edit, if you so choose) that composes the runbook. To set up the sharded environment, click the **TEST** button at the bottom of the screenC. Confirm that you want to save and test the runbook. 
4. You can watch the status of the job in the output pane. When finished, the Azure SQL DB server that you specified will be populated with a shard map manager database as well as a shard database.The **SetupShardedEnvironment** runbook is only intended to provision the sharded environment and is not intended to run on a reoccurring schedule. Proceed to the next step.  

## Test the Automation Runbook 

Test the successful execution of each of the runbooks before publishing and scheduling the runbook. 

1. Click “RUNBOOK” on the ribbon at the top of the page. 
2. Click the “ProvisionByDate” runbook.
3. Click “AUTHOR” on the ribbon at the top of the page. 
4. Click “SAVE” then “TEST”.
5. Repeat for the **ReduceServiceTier**. 

	Note, since **ProvisionBySize** and **ProvisionByDate** both provision new shards (using different algorithms), it is not necessary to run **ProvisionByDate** at this time. 

## Publish the Runbook 
The next step is to publish the runbook so that it can be scheduled to execute on a periodic basis. 
1. Click “PUBLISH” on the bottom of the page. 
2. Click “Published”. 
3. Proceed to the next step.
 
## Schedule the Runbook 

The final step is to create and link a schedule to the runbook published above. 
1. Click “SCHEDULE” at the top of the page. 
2. Click “LINK TO A NEW SCHEDULE”.
3. Name the schedule appropriately and click the right arrow button.
4. Configure the schedule.
5. When finished, click the check at the bottom of the box.
6. Once the job has been executed based on the previously established schedule, click the “JOBS” option on the ribbon at the top of the page and then select the scheduled job. 

## Conclusion 

You have now successfully authored and packaged a PowerShell module, uploaded the PowerShell module to Azure automation as an Asset, and created, tested, published and scheduled a runbook.  To monitor the health and status of the runbook, use the dashboard and jobs tabs. 

The provided examples only scratch the surface for what is possible when combining elastic scale, Azure SQL DB, and Azure Automation. Experiment and build upon these examples to enable your elastic scale application to scale horizontal, vertically, or both. 

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-scripting/zip.png
[2]: ./media/sql-database-elastic-scale-scripting/dll.png
[3]: ./media/sql-database-elastic-scale-scripting/lookslikethis.png
[4]: ./media/sql-database-elastic-scale-scripting/automation.png
[5]: ./media/sql-database-elastic-scale-scripting/prompt.png
[6]: ./media/sql-database-elastic-scale-scripting/success.png
[7]: ./media/sql-database-elastic-scale-scripting/add-variable.png