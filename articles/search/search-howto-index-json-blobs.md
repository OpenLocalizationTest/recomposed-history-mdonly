<properties
pageTitle="Indexing JSON blobs with Azure Search blob indexer"
description="Learn how to index JSON blobs with Azure Search"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="04/14/2016"
ms.author="eugenesh" />

# Indexing JSON blobs with Azure Search blob indexer 

By default, [Azure Search blob indexer](search-howto-indexing-azure-blob-storage.md) parses JSON blobs as a single chunk of text. Often, you want preserve the structure of your JSON documents. For example, given this JSON document 

	{ 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

you might want to parse that document into "text", "datePublished", and "tags" fields in your search index.

This article shows how to configure Azure Search blob indexer for JSON parsing. Happy indexing! 

> [AZURE.IMPORTANT] Currently this functionality is in preview. It is available only in the REST API using version **2015-02-28-Preview**. Please remember, preview APIs are intended for testing and evaluation, and should not be used in production environments. 

## Setting up JSON indexing

To index JSON blobs, use the blob indexer in "JSON parsing" mode: 

1. Enable `useJsonParser` configuration setting in the indexer parameters: 

	`"parameters" : { "configuration" : { "useJsonParser" : true } }`

2. If needed, use field mappings to construct a search document by picking the desired properties of the source JSON document.  This is described in detail below. 

> [AZURE.IMPORTANT] JSON parsing mode applies to all blobs in your data source. If the data source includes a mix of JSON blobs and blobs with other content formats, please let us know on [our UserVoice site](https://feedback.azure.com/forums/263029-azure-search).

## Using field mappings to build search document 

Currently, Azure Search cannot index arbitrary JSON documents directly, because it supports only primitive data types, string arrays, and GeoJSON points. However, you can use **field mappings** to pick parts of your JSON document and "lift" them into top-level fields of the search document. To learn about field mappings basics, see [Azure Search Indexer Customization](search-indexers-customization.md).

Coming back to our example JSON document: 

    { 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

Let's say you have a search index with the following fields: `text` of type Edm.String, `date` of type Edm.DateTimeOffset, and `tags` of type Collection(Edm.String). To map your JSON into the desired shape, use the following field mappings: 

	"fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	]

The source field names in the mappings are specified using the [JSON Pointer](http://tools.ietf.org/html/rfc6901) notation. You start with a forward slash to refer to the root of your JSON document, then drill into the desired property (at arbitrary level of nesting) by using forward slash-separated path. 

You can also refer to individual array elements by using a zero-based index. For example, to pick the first element of the "tags" array from the above example, use a field mapping like this:

	{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.TIP] If your JSON documents only contain simple top-level properties, you may not need field mappings at all. For example, if your JSON looks like this, the top-level properties "text", "datePublished" and "tags" will directly map to the corresponding fields in the search index: 
 
	{ 
	   "text" : "A hopefully useful article explaining how to parse JSON blobs",
	   "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
 	}

> [AZURE.NOTE] If a source field name path refers to a non-existing property, this property is skipped without an error. This is done so that we can support documents with a different schema (which is a common use case). This does mean, however, that you need to take care to avoid typos in your field mapping specification. 

## Request examples

Putting this all together, here are the complete payloads examples. 

Datasource: 

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "optional, my-folder" }
	}   

Indexer:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-json-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	  ]
	}

## Help us make Azure Search better

If you have feature requests or ideas for improvements, please reach out to us on our [UserVoice site](https://feedback.azure.com/forums/263029-azure-search/).