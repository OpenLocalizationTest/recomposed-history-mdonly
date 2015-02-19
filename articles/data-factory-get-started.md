<properties 
	pageTitle="Get started using Azure Data Factory" 
	description="This tutorial shows you how to create a sample data pipeline that copies data from a blob to an Azure SQL Database instance." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/13/2015" 
	ms.author="spelluru"/>

# Get started with Azure Data Factory
This article helps you get started with using Azure Data Factory. The tutorial in this article shows you how to create an Azure data factory and create a pipeline in the data factory to copy sample data from an Azure blob storage to an Azure SQL database.

The following list provides typical steps that developers need to perform: 

1.	Create an **Azure data factory**.
2.	Create **linked services** to link data stores (Azure Storage, Azure SQL Database, etc...) and compute services (Azure HDInsight cluster) to the data factory.  
3.	Create **tables** that describe input data and output data for pipelines. Tables also specify actual location of data within the data stores linked to a data factory. For example, a table may specify the name of the SQL table in an Azure SQL database (or) a blob container in an Azure blob. 
4.	Create **pipelines**. A pipeline consists of one or more activities that consumes input data and produces output data. A Copy activity copies data from a source data store to a destination data store and an HDInsight activity processes input data by using Hive/Pig scripts.  
5.	Specify the **active period** for pipelines (for data processing). The active period defines the time duration in which data slices will be produced.You can specify start date-time and end date-time for a pipeline (or) you can have it run all the time.

In this tutorial, you will use the **Azure Preview Portal** to create an Azure data factory, linked services, tables and a pipeline. You will also use the portal to monitor the data factory you create. 


##Prerequisites
Before you begin this tutorial, you must have the following:

- An Azure subscription. For more information about obtaining a subscription, see [Purchase Options] [azure-purchase-options], [Member Offers][azure-member-offers], or [Free Trial][azure-free-trial].
- Read through [Introduction to Azure Data Factory][data-factory-introduction] topic.
- Azure Storage Account. You will use the blob storage as a source data store in this tutorial. See [About Storage Accounts][data-factory-create-storage] for steps to create an Azure storage. You will need the **storage account name** and **account key** to do this tutorial. 
- Azure SQL Database. You will create a sample database and use it as a destination data store in this tutorial. See [How to create and configure an Azure SQL Database][data-factory-create-sql-database] for steps to create an Azure SQL database. You will need the **server name**, **database name**, **user name**, and **password** to do this tutorial. 

##In This Tutorial

Step | Description
-----| -----------
[Step 1: Create an Azure Data Factory](#CreateDataFactory) | In this step, you will create an Azure data factory named **ADFTutorialDataFactory**. 
[Step 2: Create linked services](#CreateLinkedServices) | In this step, you will create two linked services: **StorageLinkedService** and **AzureSqlLinkedService**. The StorageLinkedService links an Azure storage and AzureSqlLinkedService links an Azure SQL database to the ADFTutorialDataFactory.
[Step 3: Create input and output datasets](#CreateInputAndOutputDataSets) | In this step, you will define two data sets (**EmpTableFromBlob** and **EmpSQLTable**) that are used as input and output for the **Copy Activity** in the ADFTutorialPipeline that you will create in the next step.
[Step 4: Create and run a pipeline](#CreateAndRunAPipeline) | In this step, you will create a pipeline named **ADFTutorialPipeline**. The pipeline will have a **Copy Activity** that copies data from an Azure blob to an output Azure database table.
[Step 5: Monitor data sets and pipeline](#MonitorDataSetsAndPipeline) | In this step, you will monitor the datasets and the pipeline using Azure Preview Portal in this step.
 


## <a name="CreateDataFactory"></a>Step 1: Create an Azure Data Factory
In this step, you use the Azure Preview Portal to create an Azure data factory named **ADFTutorialDataFactory**.

1.	After logging into the [Azure Preview Portal][azure-preview-portal], click **NEW** from the bottom-left corner, and click **Data Factory** on the **New** blade. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	If you do not see **Data Factory** on the **New** blade, scroll down.  


6. In the **New data factory** blade:
	1. Enter **ADFTutorialDataFactory** for the **name**. 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. Click **RESOURCE GROUP NAME** and do the following:
		1. Click **Create a new resource group**.
		2. In the **Create resource group** blade, enter **ADFTutorialResourceGroup** for the **name** of the resource group, and click **OK**. 

			![Create Resource Group][image-data-factory-create-resource-group]

		Some of the steps in this tutorial assume that you use the resource group named **ADFTutorialResourceGroup**. If you use a different resource group, you will need to use the resource group you select here in place of ADFTutorialResourceGroup.  
7. In the **New data factory** blade, notice that **Add to Startboard** is selected.
8. Click **Create** in the **New data factory** blade.

	> [AZURE.NOTE] The name of the Azure data factory must be globally unique. If you receive the error: **Data factory name “ADFTutorialDataFactory” is not available**, change the name (for example, yournameADFTutorialDataFactory). Use this name in place of ADFTutorialFactory while performing steps in this tutorial.  
	 
	![Data Factory name not available][image-data-factory-name-not-available]

9. Click **NOTIFICATIONS** hub on the left and look for notifications from the creation process.
10. After creation is complete, you will see the **DATA FACTORY** blade as shown below

    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Step 2: Create linked services
Linked services link data stores or compute services to an Azure data factory. A data store can be an Azure Storage, Azure SQL Database or an on-premises SQL Server database.

In this step, you will create two linked services: **StorageLinkedService** and **AzureSqlLinkedService**. StorageLinkedService linked service links an Azure Storage Account and AzureSqlLinkedService links an Azure SQL database to the **ADFTutorialDataFactory**. You will create a pipeline later in this tutorial that copies data from a blob container in StorageLinkedService to a SQL table in AzureSqlLinkedService.

### Create a linked service for an Azure storage account
1.	In the **DATA FACTORY** blade, clcik **Author and deploy** tile to launch the **Editor** for the data factory.

	![Author and Deploy Tile][image-author-deploy-tile] 

5. In the **Editor**, click **New data store** button on the toolbar and click **Azure storage** from the drop down menu. You should see the JSON template for creating an Azure storage in the right pane. 

	![Editor New data store button][image-editor-newdatastore-button]
    
6. Replace **<accountname\>** and **<accountkey\>** with the account name and account key values for your Azure storage account. 

	![Editor Blob Storage JSON][image-editor-blob-storage-json]    

	> [AZURE.NOTE] See [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) for details about JSON properties.

6. Click **Deploy** on the toolbar to create and deploy the StorageLinkedService. Confirm that you see the message **LINKED SERVICE CREATED SUCCESSFULLY** on the title bar.

	![Editor Blob Storage Deploy][image-editor-blob-storage-deploy]

### Create a linked service for an Azure SQL Database
1. In the **Editor** for the Data Factory, click **New data store** button on the toolbar and click **Azure SQL database** from the drop down menu. You should see the JSON template for creating the AzureSqlLinkedService in the right pane.

	![Editr Azure SQL Settings][image-editor-azure-sql-settings]

2. Replace **<servername\>**, **<databasename\>**, **<username\>@<servername\>**, and **<password\>** with names of your Azure SQL server, database, user account, and  password. 
3. Click **Deploy** on the toolbar to create and deploy the AzureSqlLinkedService. 

> [AZURE.NOTE] If you enter all the values correctly and the Data Factory service is not able to connect to the Azure SQL Database, you may not have **Allow access to Azure services** setting turned ON for your Azure SQL server. To verify and turn it on, do the following:
>
> <ol>
> <li>Click <b>BROWSE</b> hub on the left and click <b>SQL servers</b>.</li>
> <li>Select your server, and click <b>SETTINGS</b> on the <b>SQL SERVER</b> blade.</li>
> <li>In the <b>SETTINGS</b> blade, click <b>Firewall</b>.</li>
> <li>In the <b>Firewalll settings</b> blade, click <b>ON</b> for <b>Allow access to Azure services</b>.</li>
> <li>Click <b>ACTIVE</b> hub on the left to switch to the <b>Data Factory</b> blade you were on.</li>
> </ol>
    

## <a name="CreateInputAndOutputDataSets"></a>Step 3: Create input and output tables

In the previous step, you created linked services **StorageLinkedService** and **AzureSqlLinkedService** to link an Azure Storage account and Azure SQL database to the data factory: **ADFTutorialDataFactory**. In this step, you will create tables that represent the input and output data for the Copy Activity in the pipeline you will be creating in the next step. 

A table is a rectangular dataset and it is the only type of dataset that is supported at this time. The input table in this tutorial refers to a blob container in the Azure Storage that StorageLinkedService points to and the output table refers to a SQL table in the Azure SQL database that AzureSqlLinkedService points to.  


* Create a blob container named **adftutorial** in the Azure blob storage that StorageLinkedService points to. 
* Create and upload a text file, **emp.txt**, as a blob to the **adftutorial** container. 
* Create a table named **emp** in the Azure SQL Database in the Azure SQL database that AzureSqlLinkedService points to.
* Create a folder named **ADFGetStarted** on your hard drive.  

### Prepare Azure Blob Storage and Azure SQL Database for the tutorial
1. Launch Notepad, paste the following text, and save it as **emp.txt** to **C:\ADFGetStarted** folder on your hard drive. 

        John, Doe
		Jane, Doe
				
2. Use tools such as [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) to create the **adftutorial** container and to upload the **emp.txt** file to the container.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Use the following SQL script to create the **emp** table in your Azure SQL Database. You can use Azure SQL Management Console to connect to an Azure SQL Database and to run SQL script. You can also SQL Server Management Studio to do this task. 


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				
	To launch Azure SQL Management Console, click **MANAGE** as shown in the following image:
 
	![Launch Azure SQL Management Console][image-data-factory-sql-management-console]

	![Azure SQL Management Console][image-data-factory-sql-management-console-2]
### Create input table 
A table is a rectangular dataset and has a schema. In this step, you will create a table named **EmpBlobTable** that points to a blob container in the Azure Storage represented by the **StorageLinkedService** linked service.


1. In the **Editor** for the Data Factory, click **New dataset** button on the toolbar and click **Blob table** from the drop down menu. 
2. Replace JSON in the right pane with the following JSON snippet: 

        {
     	    "name": "EmpTableFromBlob",
		    "properties":
    		{
        		"structure":  
       			 [ 
            		{ "name": "FirstName", "type": "String"},
            		{ "name": "LastName", "type": "String"}
        		],
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitonexternal": {}
       		 	}
    		}
		}

		
     Note the following: 
	
	- location **type** is set to **AzureBlobLocation**.
	- **linkedServiceName** is set to **StorageLinkedService**. You had created this linked service in Step 2.
	- **folderPath** is set to the **adftutorial** container. You can also specify the name of a blob within the folder. Since you are not specifying the name of the blob, data from all blobs in the container is considered as an input data.  
	- format **type** is set to **TextFormat**
	- There are two fields in the text file – **FirstName** and **LastName** – separated by a comma character (columDelimiter)	
	- The **availability** is set to **hourly** (**frequency** is set to **hour** and **interval** is set to **1**), so the Data Factory service will look for input data every hour in the root folder in the blob container (**adftutorial**) you specified.

	if you don't specify a **fileName** for an **input table**, all files/blobs from the input folder (**folderPath**) are considered as inputs. If you specify a fileName in the JSON, only the specified file/blob is considered asn input. See the sample files in the [tutorial][adf-tutorial] for examples.
 
	If you do not specify a **fileName** for an **output table**, the generated files in the **folderPath** are named in the following format: Data.<Guid>.txt (for example: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	To set **folderPath** and **fileName** dynamically based on the **SliceStart** time, use the partitionedBy property. In the following example, folderPath uses Year, Month, and Day from from the SliceStart (start time of the slice being processed) and fileName uses Hour from the SliceStart. For example, if a slice is being produced for 2014-10-20T08:00:00, the folderName is set to wikidatagateway/wikisampledataout/2014/10/20 and the fileName is set to 08.csv. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	> [AZURE.NOTE] See [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) for details about JSON properties.

2. Click **Deploy** on the toolbar to create and deploy the **EmpTableFromBlob** table. Confirm that you see the **TABLE CREATED SUCCESSFULLY** message on the title bar of the Editor.

### Create output table
In this part of the step, you will create an output table named **EmpSQLTable** that points to a SQL table in the Azure SQL database that is represented by the **AzureSqlLinkedService** linked service. 

1. In the **Editor** for the Data Factory, click **New dataset** button on the toolbar and click **Azure SQL table** from the drop down menu. 
2. Replace JSON in the right pane with the following JSON snippet:

        {
    		"name": "EmpSQLTable",
    		"properties":
    		{
        		"structure":
        		[
                	{ "name": "FirstName", "type": "String"},
                	{ "name": "LastName", "type": "String"}
        		],
        		"location":
        		{
            		"type": "AzureSQLTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "AzureSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     Note the following: 
	
	* location **type** is set to **AzureSQLTableLocation**.
	* **linkedServiceName** is set to **AzureSqlLinkedService** (you had created this linked service in Step 2).
	* **tablename** is set to **emp**.
	* There are three columns – **ID**, **FirstName**, and **LastName** – in the emp table in the database, but ID is an identity column, so you need to specify only **FirstName** and **LastName** here.
	* The **availability** is set to **hourly** (**frequency** set to **hour** and **interval** set to **1**).  The Data Factory service will generate an output data slice every hour in the **emp** table in the Azure SQL database.


3. Click **Deploy** on the toolbar to create and deploy the **EmpSQLTable** table.


## <a name="CreateAndRunAPipeline"></a>Step 4: Create and run a pipeline
In this step, you create a pipeline with a **Copy Activity** that uses **EmpTableFromBlob** as input and **EmpSQLTable** as output.

1. In the **Editor** for the Data Factory, click **New pipeline** button on the toolbar. Click **... (Ellipsis)** on the toolbar if you do not see the button. Alternatively, you can right-click **Pipelines** in the tree view and click **New pipeline**.

	![Editor New Pipeline Button][image-editor-newpipeline-button]
 
2. Replace JSON in the right pane with the following JSON snippet: 

         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Sql Azure",
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpTableFromBlob"} ],
						"outputs": [ {"name": "EmpSQLTable"} ],		
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink"
							}	
						},
						"Policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 0,
							"timeout": "01:00:00"
						}		
					}
        		],

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	Note the following:

	- In the activities section, there is only one activity whose **type** is set to **CopyActivity**.
	- Input for the activity is set to **EmpTableFromBlob** and output for the activity is set to **EmpSQLTable**.
	- In the **transformation** section, **BlobSource** is specified as the source type and **SqlSink** is specified as the sink type.

	> [AZURE.NOTE] Replace the value of the **start** property with the current day and **end** value with the next day. Both start and end datetimes must be in [ISO format](http://en.wikipedia.org/wiki/ISO_8601). For example: 2014-10-14T16:32:41Z. The **end** time is optional, but we will use it in this tutorial. 
	> If you do not specify value for the **end** property, it is calculated as "**start + 48 hours**". To run the pipeline indefinitely, specify **9/9/9999** as the value for the **end** property.
	> In the example above, there will be 24 data slices as each data slice is produced hourly.
	
	> [AZURE.NOTE] See [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) for details about JSON properties.

4. Click **Deploy** on the toolbar to create and deploy the **ADFTutorialPipeline**. Confirm that you see the **PIPELINE CREATED SUCCESSFULLY** message.
5. Now, close the **Editor** blade by clicking **X**. Click **X** again to close the ADFTutorialDataFactory blade with the toolbar and tree view. If you see **your unsaved edits will be discarded** message, click **OK**.
6. You should be back to the **DATA FACTORY** blade for the **ADFTutorialDataFactory**.

**Congratulations!** You have successfully created an Azure data factory, linked services, tables, and a pipeline and scheduled the pipeline.   
 
### View the data factory in a Diagram View 
4. In the **DATA FACTORY** blade, click **Diagram**.

	![Data Factory Blade - Diagram Tile][image-datafactoryblade-diagramtile]

5. You should see the diagram similar to the following: 

	![Diagram view][image-data-factory-get-started-diagram-blade]

	You can zoom in, zoom out, zoom to 100%, zoom to fit, automatically position pipelines and tables, and show lineage information (highlights upstream and downstream items of selected items).  You can double-blick on an object (input/output table or pipeline) to see properties for it. 

## <a name="MonitorDataSetsAndPipeline"></a>Step 5: Monitor the datasets and pipeline
In this step, you will use the Azure Portal to monitor what’s going on in an Azure data factory. You can also use PowerShell cmdlets to monitor datasets and pipelines. For details about using cmdlets for monitoring, see [Monitor and Manage Data Factory using PowerShell Cmdlets][monitor-manage-using-powershell].

1. Navigate to [Azure Portal (Preview)][azure-preview-portal] if you don't have it open. 
2. If the blade for **ADFTutorialDataFactory** is not open, open it by clicking **ADFTutorialDataFactory** on the **Startboard**. 
3. You should see the count and names of tables and pipeline you created on this blade.

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. Now, click **Datasets** tile.
5. In the **Datasets** blade, click **EmpTableFromBlob**. This is the input table for the **ADFTutorialPipeline**.

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. Notice that the data slices up to the current time have already been produced and they are **Ready** because the **emp.txt** file exists all the time in the blob container: **adftutorial\input**. Confirm that no slices show up in the **Problem slices** section at the bottom.
6. Now, in the **Datasets** blade, click the **EmpSQLTable**. This is the output table for the **ADFTutorialPipeline**.

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. You should see the **EmpSQLTable** blade as shown below:

	![table blade][image-data-factory-get-started-table-blade]
 
7. Notice that the data slices up to the current time have already been produced and they are **Ready**. No slices show up in the **Problem slices** section at the bottom.
8. Click **… (Ellipsis)** to see all the slices.

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. Click on any data slice from the list and you should see the **DATA SLICE** blade.

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. In the **DATA SLICE** blade, you should see all activity runs in the list at the bottom. Click on an **activity run** to see the **ACTIVITY RUN DETAILS** blade. 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. Click **X** to close all the blades until you get back to the home blade for the **ADFTutorialDataFactory**.
14. (optional) Click **Pipelines** on the home page for **ADFTutorialDataFactory**, click **ADFTutorialPipeline** in the **Pipelines** blade, and drill through input tables (**Consumed**) or output tables (**Produced**).
15. Launch **SQL Server Management Studio**, connect to the Azure SQL Database, and verify that the rows are inserted into the **emp** table in the database.

	![sql query results][image-data-factory-get-started-sql-query-results]


## Summary 
In this tutorial, you created an Azure data factory to copy data from an Azure blob to an Azure SQL database. You used the Azure Preview Portal to create the data factory, linked services, tables, and a pipeline. Here are the high level steps you performed in this tutorial:  

1.	Create an Azure **data factory**.
2.	Create **linked services** that link data stores and computes (referred as **Linked Services**) to the data factory.
3.	Create **tables** which describe input data and output data for pipelines.
4.	Create **pipelines**. A pipeline consists of one or more activities and processes the inputs and produces outputs. 
5.	Specify the **active period** for pipelines (for data processing). The active period defines the time duration in which data slices will be produced.

For a list of supported activities, see [Pipelines and Activities][msdn-activities] topic and for a list of supported linked services, see [Linked Services][msdn-linkedservices] topic on MSDN Library.  

## Next steps

Article | Description
------ | ---------------
[Copy data with Azure Data Factory - Copy Activity][copy-activity] | This article provides detailed description of the **Copy Activity** you used in this tutorial. 
[Enable your pipelines to work with on-premises data][use-onpremises-datasources] | This article has a walkthrough that shows how to copy data from an **on-premises SQL Server database** to an Azure blob.
[Use Pig and Hive with Data Factory][use-pig-and-hive-with-data-factory] | This article has a walkthrough that shows how to use **HDInsight Activity** to run a **hive/pig** script to process input data to produce output data.
[Tutorial: Move and process log files using Data Factory][adf-tutorial] | This article provides an **end-to-end walkthrough** that shows how to implement a **real world scenario** using Azure Data Factory to transform data from log files into insights.
[Use custom activities in a Data Factory][use-custom-activities] | This article provides a walkthrough with step-by-step instructions for creating a **custom activity** and using it in a pipeline. 
[Monitor and Manage Azure Data Factory using PowerShell][monitor-manage-using-powershell] | This article describes how to **monitor and manage** an Azure Data Factory using **Azure PowerShell cmdlets**. You can try out the examples in the article on the ADFTutorialDataFactory.
[Troubleshoot Data Factory issues][troubleshoot] | This article describes how to **troubleshoot** Azure Data Factory issue. You can try the walkthrough in this article on the ADFTutorialDataFactory by introducing an error (deleting table in the Azure SQL Database). 
[Azure Data Factory Cmdlet Reference][cmdlet-reference] | This reference content has details about all the **Data Factory cmdlets**.
[Azure Data Factory Developer Reference][developer-reference] | The Developer Reference has the comprehensive reference content for cmdlets, JSON script, functions, etc… 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction
[data-factory-create-storage]: ../storage-whatis-account
[data-factory-create-sql-database]: ../sql-database-create-configure/


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png
