<properties 
	pageTitle="Teradata Connector - Move Data From Teradata" 
	description="Learn about Teradata Connector for the Data Factory service that lets you move data from Teradata Database" 
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
	ms.date="07/24/2015" 
	ms.author="spelluru"/>

# Teradata Connector - Move data from Teradata

This article outlines how you can use data factory copy activity to move data to from Teradata to another data store. This article builds on the [data movement activities](data-factory-data-movement-activities.md) article which presents a general overview of data movement with copy activity and supported data store combinations.

Currently data factory supports connecting to on-premises Teradata sources via the Data Management Gateway. Please refer to [moving data between on-premises locations and cloud](data-factory-move-data-between-onprem-and-cloud.md) article to learn about Data Management Gateway and step by step instructions on setting up the gateway. 

Note: Currently you need to leverage the gateway to connect to Teradata even if it is hosted in Azure IaaS VMs. If you are trying to connect to an instance of Teradata hosted in cloud you can also install the gateway instance in the IaaS VM.

Data factory currently only supports moving data from Teradata to other data stores as for now.

## Installation 

For Data Management Gateway to connect to the Teradata Database, you need to install the [Teradata Connector/Net 6.6.5 for Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) on the same system as the Data Management Gateway.

### Sample: Copy data from Teradata to Azure Blob

The sample below shows:

1.	The linked service of type Teradata.
2.	The linked service of type Azure Storage.
3.	The input and output datasets.
4.	The pipeline with Copy Activity.

The sample copies data from a query result in Teradata database to a blob every hour. For more information on various properties used in the sample below please refer to documentation on different properties in the sections following the samples.

As a first step please setup the data management gateway as per the instructions in the [moving data between on-premises locations and cloud](data-factory-move-data-between-onprem-and-cloud.md) article.

**Teradata linked service:**

	{
	    "name": "OnPremTeradataLinkedService",
	    "properties": {
	        "type": "OnPremisesTeradata",
	        "typeProperties": {
	            "server": "<server>",
	            "database": "<database>",
	            "schema": "<schema>",
	            "authenticationType": "<authentication type>",
	            "username": "<username>",
	            "password": "<password>",
	            "gatewayName": "<gatewayName>"
	        }
	    }
	}

**Azure Blob storage linked service:**

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorageLinkedService",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
			}
	    }
	}


**Teradata input dataset:**

The sample assumes you have created a table “MyTable” in Teradata and it contains a column called “timestamp” for time series data.

Setting “external”: true and specifying externalData policy tells data factory that this is a table that is external to the data factory and not produced by an activity in the data factory.

	{
	    "name": "TeradataDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremTeradataLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

**Azure Blob output dataset:**

Data is copied to a new blob every hour with the path for the blob reflecting the specific datetime with hour granularity.

	{
	  "name": "AzureBlobTeradataDataSet",
	  "properties": {
	    "published": false,
	    "type": "AzureBlob",
	    "linkedServiceName": "AzureStorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "format": {
	        "type": "TextFormat",
	        "rowDelimiter": "\n",
	        "columnDelimiter": "\t"
	      },
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%HH"
	          }
	        }
	      ]
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Copy activity:**

Copy activity specifies the input, output dataset and is scheduled for runs every hour. The SQL query specified with query property selects the data in the past hour to copy.

	{
	    "name": "CopyTeradataToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "TeradataDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobTeradataDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "name": "TeradataToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z",
	        "isPaused": false
	    }
	}

## Teradata Linked Service Properties

The following table provides description for JSON elements specific to Teradata linked service. 

Property | Description | Required
-------- | ----------- | --------
type | The type property must be set to: **OnPremisesTeradata** | Yes
server | Name of the Teradata server. | Yes
database | Name of the Teradata database. | Yes 
schema  | Name of the schema in the database. | No
authenticationType | Type of authentication used to connect to the Teradata database. Possible values are: Anonymous, Basic, and Windows. | Yes
username | Specify user name if you are using Basic or Windows authentication. | No 
password | Specify password for the user account you specified for the username. | No 
gatewayName | Name of the gateway that the Data Factory service should use to connect to the on-premises Teradata database. | Yes

## Teradata Dataset Type Properties

For a full list of sections & properties available for defining datasets please refer to the [Creating datasets](data-factory-create-datasets) article. Sections like structure, availability, and policy of a dataset JSON are similar for all dataset types (Azure SQL, Azure blob, Azure table, etc...).

The typeProperties section is different for each type of dataset and provides information about the location of the data in the data store. The typeProperties section for dataset of type RelationalTable (which includes Teradata dataset) has the following properties.

Property | Description | Required
-------- | ----------- | --------
tableName | Name of the table in the Teradata Database instance that linked service refers to. | Yes 

## Teradata Copy Activity Type Properties

For a full list of sections & properties available for defining activities please refer to the [Creating Pipelines](data-factory-create-pipelines.md) article. Properties like name, description, input and output tables, various policies etc. are available for all types of activities. 

Properties available in the typeProperties section of the activity on the other hand vary with each activity type and in case of Copy activity they vary depending on the types of sources and sinks.

In case of Copy Activity when source is of type RelationalSource (which includes Teradata) the following properties are available in typeProperties section:

Property | Description | Allowed values | Required
-------- | ----------- | -------------- | --------
query | Use the custom query to read data. | SQL query string. For example: select * from MyTable. | No

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Type Mapping for Teradata

As mentioned in the [data movement activities](data-factory-data-movement-activities.md) article Copy activity performs automatic type conversions from automatic type conversions from source types to sink types with the following 2 step approach:

1. Convert from native source types to .NET type
2. Convert from .NET type to native sink type

When moving data to Teradata the following mappings will be used from Teradata type to .NET type.

Teradata Database | type	.NET Framework type
----------------- | ---------------------------
Char | String
Clob | String
Graphic | String
VarChar | String
VarGraphic | String
Blob | Byte[]
Byte | Byte[]
VarByte | Byte[]
BigInt | Int64
ByteInt | Int16
Decimal | Decimal
Double | Double
Integer | Int32
Number | Double
SmallInt | Int16
Date | DateTime
Time | TimeSpan
Time With Time Zone | String
Timestamp | DateTime
Timestamp With Time Zone | DateTimeOffset
Interval Day | TimeSpan
Interval Day To Hour | TimeSpan
Interval Day To Minute | TimeSpan
Interval Day To Second | TimeSpan
Interval Hour | TimeSpan
Interval Hour To Minute | TimeSpan
Interval Hour To Second | TimeSpan
Interval Minute | TimeSpan
Interval Minute To Second | TimeSpan
Interval Second | TimeSpan
Interval Year | String
Interval Year To Month | String
Interval Month | String
Period(Date) | String
Period(Time) | String
Period(Time With Time Zone) | String
Period(Timestamp) | String
Period(Timestamp With Time Zone) | String
Xml | String


[AZURE.INCLUDE [data-factory-data-stores-with-rectangular-tables](../../includes/data-factory-data-stores-with-rectangular-tables.md)]

[AZURE.INCLUDE [data-factory-type-repetability-for-relational-sources](../../includes/data-factory-type-repetability-for-relational-sources.md)]