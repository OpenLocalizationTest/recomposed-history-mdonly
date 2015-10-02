<properties 
	pageTitle="Run U-SQL script from Azure Data Factory" 
	description="Learn how to process data by running U-SQL scripts on Azure Data Lake Analytics compute service." 
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
	ms.date="10/02/2015" 
	ms.author="spelluru"/>

# Invoke U-SQL script from Data Factory
A pipeline in an Azure data factory processes data in linked storage services by using linked compute services. It contains a sequence of activities where each activity performs a specific processing operation. This article describes the **U-SQL Activity** that runs a **U-SQL** script on an **Azure Data Lake Analytics** compute linked service. 

## Azure Data Lake Analytics Linked Service
You create an **Azure Data Lake Analytics** linked service to link an Azure Data Lake Analytics compute service to an Azure data factory.

### Example

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID> 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


### Properties

Property | Description | Required
-------- | ----------- | --------
Type | The type property should be set to: **AzureDataLakeAnalytics**. | Yes
accountName | Azure Data Lake Analytics Account Name. | Yes
dataLakeAnalyticsUri | Azure Data Lake Analytics URI. |  No 
authorization | Authorization code is automatically retrieved after clicking ‘**Authorize**’ button in the Data Factory Editor and completing the OAuth login. | Yes 
subscriptionId | Azure subscription id | No (If not specified, subscription of the data factory is used). 
resourceGroupName | Azure resource group name |  No (If not specified, resource group of the data factory is used).
sessionId | OAuth session id from the oauth authorization session. Each session id is unique and may only be used once. This is auto-generated in the Data Factory Editor. | Yes

   
 
## JSON for U-SQL Activity 

The following JSON snippet defines a pipeline with a U-SQL Activity.  
 
 

	{
    	"name": "ComputeEventsByRegionPipeline",
    	"properties": {
        	"description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        	"activities": 
			[
            	{
            	    "type": "DataLakeAnalyticsU-SQL",
                	"typeProperties": {
                    	"scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
	                    "scriptLinkedService": "StorageLinkedService",
    	                "degreeOfParallelism": 3,
    	                "priority": 100,
    	                "parameters": {
    	                    "in": "/datalake/input/SearchLog.tsv",
    	                    "out": "/datalake/output/Result.tsv"
    	                }
    	            },
    	            "inputs": [
	    				{
	                        "name": "DataLakeTable"
	                    }
	                ],
	                "outputs": 
					[
	                    {
                    	    "name": "EventsByRegionTable"
                    	}
                	],
                	"policy": {
                    	"timeout": "06:00:00",
	                    "concurrency": 1,
    	                "executionPriorityOrder": "NewestFirst",
    	                "retry": 1
    	            },
    	            "scheduler": {
    	                "frequency": "Day",
    	                "interval": 1
    	            },
    	            "name": "EventsByRegion",
    	            "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
    	        }
    	    ],
    	    "start": "2015-08-08T00:00:00Z",
    	    "end": "2015-08-08T01:00:00Z",
    	    "isPaused": false
    	}
	}


The following table describes names and descriptions of properties that are specific to this activity. 

Property | Description
-------- | -----------
type | The type property must be set to **DataLakeAnalyticsU-SQL**.
scriptPath | Path to folder that contains the U-SQL script. 
scriptLinkedService | Linked service that links the storage that contains the script to the data factory
degreeOfParallelism | The maximum number of nodes that will be used simultaneously to run the job.
priority | Determines which jobs out of all that are queued should be selected to run first. The lower the number, the higher the priority.
parameters | Parameters for the U-SQL script 


### Sample input and output datasets

#### Input dataset
In this example, the input data resides in an Azure Data Lake Store (SearchLog.tsv file in the datalake/input folder). 

	{
    	"name": "DataLakeTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
    	    "linkedServiceName": "AzureDataLakeLinkedService",
    	    "typeProperties": {
    	        "folderPath": "datalake/input/",
    	        "fileName": "SearchLog.tsv",
    	        "format": {
    	            "type": "TextFormat",
    	            "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
    	    },
    	    "availability": {
    	        "frequency": "Day",
    	        "interval": 1
    	    }
    	}
	}	

#### Output dataset
In this example, the output data produced by the U-SQL script is stored in an Azure Data Lake Store (datalake/output folder). 

	{
	    "name": "EventsByRegionTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeLinkedService",
	        "typeProperties": {
	            "folderPath": "datalake/output/"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

