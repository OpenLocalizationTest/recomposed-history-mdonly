<properties 
	pageTitle=".NET samples for DocumentDB | Microsoft Azure" 
	description="Find .NET samples for common tasks in DocumentDB, including CRUD operations for database accounts, databases, and collections." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="mimig"/>


# .NET samples

> [AZURE.SELECTOR]
- [Node.js samples](documentdb-nodejs-samples.md)
- [Java samples](documentdb-java-samples.md)
- [Python sampples](documentdb-python-samples.md)
- [JavaScript client-side samples](documentdb-javascript-client-samples.md)
- [JavaScript server-side samples](documentdb-javascript-server-samples.md)

Samples for common operations on DocumentDB resources are included in the [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) GitHub repository.

**Prerequisites**

1. You need an Azure account to use these samples:
    - You can [open an Azure account for free](/pricing/free-trial/?WT.mc_id=A261C142F): You get credits you can use to try out paid Azure services, and even after they're used up you can keep the account and use free Azure services, such as Websites. Your credit card will never be charged, unless you explicitly change your settings and ask to be charged.
   - You can [activate MSDN subscriber benefits](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Your MSDN subscription gives you credits every month that you can use for paid Azure services.
2. You also need the [Microsoft.Azure.DocumentDB NuGet package](http://www.nuget.org/packages/Microsoft.Azure.DocumentDB/). 

## Database samples

Samples for the following database tasks are included in the [azure-documentdb-net/samples/code-samples/DatabaseManagement/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) file.

Task | Method
--- | ---
Create a database | [DocumentClient.CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx#M:Microsoft.Azure.Documents.Client.DocumentClient.CreateDatabaseAsync(Microsoft.Azure.Documents.Database,Microsoft.Azure.Documents.Client.RequestOptions))
Retrieve a database | [DocumentQueryable.CreateDatabaseQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdatabasequery.aspx)
List databases for an account | [DocumentClient.ReadDatabaseFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdatabasefeedasync.aspx)
Retrieve the DocumentDB endpointUrl, primary and secondary authorization keys | [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx)
Delete a database | [DeleteDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.deletedatabaseasync.aspx#M:Microsoft.Azure.Documents.Client.DocumentClient.DeleteDatabaseAsync)

## Collection samples 

Samples for the following collection tasks are included in the [azure-documentdb-net/samples/code-samples/ CollectionManagement/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) file.

- Create a collection
- Create a collection with a custom index policy
- Retrieve the performance tier of the collection
- Update the performance tier of a collection
- Delete a collection

## Document samples

The document sample file, [azure-documentdb-net/samples/code-samples/DocumentManagement/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs), shows how to do the following:

- Create a list of plain old clr objects (POCOs) and insert into collection by using [DocumentClient.CreateDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createattachmentasync.aspx)
- Read a document by using [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) 
- Update a document by using [DocumentClient.ReplaceDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replacedocumentasync.aspx).
- Create a document attachment by using [DocumentClient.CreateAttachmentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createattachmentasync.aspx). 
- Query the document for attachments by using [DocumentClient.CreateAttachmentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createattachmentquery.aspx). 
- Read media content by using [DocumentClient.ReadMediaAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readmediaasync.aspx). 

## Indexing samples

The indexing sample file, [azure-documentdb-net/samples/code-samples/IndexManagement/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs), shows how to do the following:

- Exclude a document from automatic indexing by using [CreateDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createattachmentasync.aspx) and [IndexingDirective.Exclude](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingdirective.aspx).
- Opt-in indexing of documents with manual indexing policy by using [CreateDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createattachmentasync.aspx).
- Use lazy indexing for bulk import or read heavy collections by instantiating a new [DocumentCollection](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.documentcollection.documentcollection.aspx#M:Microsoft.Azure.Documents.DocumentCollection) and using [IndexingMode.Lazy](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingmode.aspx).
- Use range indexing over strings in order to support Order by ...
- Exclude certain paths from indexing ...
- Force a range-based scan on a Hash index...
- Update the indexing policy ...
 
## Partitioning samples

The partitioning sample file, [azure-documentdb-net/samples/code-samples/Partitioning/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/Partitioning/Program.cs), shows how to do the following:

- A
- B
- C

## Geospatial samples

The geospatial sample file, [azure-documentdb-net/samples/code-samples/Queries.Spatial/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/Queries.Spatial/Program.cs), shows how to do the following:

-  Create a new collection, or modify an existing one to enable spatial indexing by using GetCollectionWithSpatialIndexingAsync
-  Insert documents with GeoJSON spatial data by using CreateDocumentAsync
-  Find points within a circle or radius of another point by using CreateDocumentQuery
-  Find points that exist within a polygon by using CreateDocumentQuery
-  Update a collection to enable spatial indexing in the indexing policy by using ReplaceDocumentCollectionAsync

## JavaScript samples

The javascript query sample file, [azure-documentdb-net/samples/code-samples/Queries.JavaScript/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/Queries.JavaScript/Program.cs), shows how to do the following:

- A
- B
- C

## Order by samples

The Order by sample file, [azure-documentdb-net/samples/code-samples/Queries.OrderBy/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/Queries.OrderBy/Program.cs), shows how to do the following:

- A
- B
- C

## Query samples

The query document file, [azure-documentdb-net/samples/code-samples/Queries/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/Queries/Program.cs), shows how to do the following:

- A
- B
- C

## Server-side programming samples

The server-side programming file, [azure-documentdb-net/samples/code-samples/ServerSideScripts/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs), shows how to do the following:

- A
- B
- C 

## User management samples

The user management file, [azure-documentdb-net/samples/code-samples/UserManagement/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs), shows how to do the following:

- A
- B
- C 



