<properties 
	pageTitle="Invoke SqlIp script from Azure Data Factory" 
	description="Learn how to process data by running SQL IP scripts on Azure Big Analytics compute." 
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
	ms.date="07/31/2015" 
	ms.author="spelluru"/>

# Invoke Sql Ip script from Data Factory
This article describes how to run a **SqlIp** script on an Azure Kona compute from an Azure Data Factory pipeline by using the **SqlIp Activity**. 

## Introduction 
A pipeline in an Azure data factory processes data in linked storage services by using linked compute services. It contains a sequence of activities where each activity performs a specific processing operation. This article describes using the Sql Ip Activity.
 
## JSON for SqlIp Activity 

The following JSON snippet defines a pipeline with a SqlIp Activity.  
 
 

		{
		    "name": "ComputeEventsByRegionPipeline",
		    "properties": {
		        "description": "This is a pipeline to compute events by region.",
		        "activities": [
		            {
		                "type": "SqlIp",
		                "typeProperties": {
		                    "scriptPath": "scripts\\kona\\LogProcessing.txt",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "degreeOfParallelism": 3,
		                    "priority": 100,
		                    "parameters": {
		                        "in": "$$Text.Format('/datalake/input/yearno={0:yyyy}/monthno={0:%MM}/dayno={0:%dd}/SearchLog.tsv', SliceStart)",
		                        "out": "$$Text.Format('/datalake/output/yearno={0:yyyy}/monthno={0:%MM}/dayno={0:%dd}/result.tsv', SliceStart)"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "DataLakeTable"
		                    },
		                    {
		                        "name": "DummyBlobDemoTable"
		                    }
		                ],
		                "outputs": [
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
		                "linkedServiceName": "KonaLinkedService"
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
type | The type property must be set to **SqlIp**.
scriptPath | Path to folder that contains the SqlIp script. 
scriptLinkedService | Linked service that links the storage that contains the script to the data factory
degreeOfParallelism | Also known as BAUs (Big Analytics Units), or the maximum number of nodes that will be used simultaneously to run the job.
priority | Determines which jobs out of all that are queued should be selected to run first. The lower the number, the higher the priority.
