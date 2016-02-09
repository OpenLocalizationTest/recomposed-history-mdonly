<properties 
	pageTitle="Get started with Azure Search in the Azure Portal | Microsoft Azure | Cloud search service" 
	description="Get started with Azure Search, a hosted cloud search service on Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/08/2016" 
	ms.author="heidist"/>

# Get started with Azure Search in the Azure Portal

Microsoft Azure Search is a hosted cloud search service that you can use to add search functionality to custom applications. It provides the search engine and storage for your data, which you access and manage using the Azure Portal, a .NET SDK, or a REST API. 

This article is a code-free introduction to Azure Search, using capabilities built right into the portal. If you don't have data, you can quickly create a [sample Azure DocumentDB database](#apdx-sampledata) using the data files and instructions in this article.

> [AZURE.NOTE] This tutorial requires an [Azure subscription](../includes/free-trial-note.md) and an [Azure Search service](search-create-service-portal.md). If you aren't ready to sign up for a trial subscription, you can skip this tutorial and opt for [Try Azure App Service](search-tryappservice.md) instead. This alternative option gives you Azure Search with an ASP.NET Web app for free - one hour per session - no subscription required.
 
## Find your service

1. Sign in to the [Azure Portal](https://portal.azure.com).

2. Open the service dashboard of your Azure Search service. Here are a few ways to find the dashboard.
	- In the Jumpbar, click **Search services**. The Jumpbar lists every service provisioned in your subscription. If a search service has been defined, you'll see **Search services** in the list.
	- In the Jumpbar, click **Browse** and then type "search" in the search box to produce a list of all search services created in your subscriptions.

## Check for space

Many customers start with the free service. This version is limited to three indexes, three data sources, and three indexers. Make sure you have room for extra items before starting this walkthrough. This walkthrough will create one of each object.

## Create an index and load data

Search queries iterate over an *index* containing searchable data, metadata, and constructs used for optimizing certain search behaviors. As a first step, you will need to define and populate an index.

There are several ways to create an index. Approaches vary in how much automation or integration is offered. If you have usable data in a data store that Azure Search can crawl - such as Azure SQL Database, SQL Server on an Azure VM, or DocumentDB - you can create and populate an index very easily using an indexer.

To keep this task simple, we'll assume a data source that Azure Search can crawl using one of its *indexers* and the **Import data** wizard. If you don't already have a SQL table or view or a DocumentDB database, you can quickly create [a sample DocumentDB database](#apdx-sampledata) as a prerequisite to this tutorial.

> [AZURE.NOTE] Newly announced is indexer support for crawling Azure Blob Storage, but that feature is in preview and not yet a portal option. To try that indexer, you'll need to write code. See [Indexing Azure Blob storage in Azure Search](search-howto-indexing-azure-blob-storage.md) for more information.

<a id="defineDS"></a>
#### Step 1: Define the data source

1. On your Azure Search service dashboard, click **Import data** to start a wizard that both creates and populates an index.

2. Click **Data Source** > **DocumentDB** > **Name**, type a name for the data source. A data source is a connection object in Azure Search that can be used with other indexers. Once you create it, it becomes available as an "existing data source" in your service.

3. Choose your existing DocumentDB account, and the database and collection. If you're using the sample data we provide, your data source definition will look like this:

  ![][2]

Notice that we skipped the query. This is because we're not implementing change tracking in our dataset this time around. If your dataset includes a field that keeps track of when a record is updated, you can configure an Azure Search indexer to use change tracking for selective updates to your index.

#### Step 2: Define the index

Still in the wizard, click **Index** and take a look at the design surface for creating an Azure Search index. In our sample dataset, all of the fields were detected automatically and added to the definition with the correct data type. 

  ![][3]

Checkboxes across the top of the field list are *index attributes* that control how the field is used. 

- **Retrievable** means that it shows up in search results list. You can mark individual fields as off limits for search results, for example when fields used only in filter expressions. 
- **Filterable**, **Sortable**, and **Facetable** determine whether a field can be used in a filter, a sort, or a facet navigation structure. 
- **Searchable** means that a field is included in full text search. Numeric fields and Boolean fields are often marked as not searchable. 

Mark the fields in your index to use the following options (Retrievable, Searchable, and so on). 

Most fields are Retrievable. Most string fields are Searchable (you don't need to make the Key searchable). A few fields like genre, orderableOnline, rating, and tags are also Filterable, Sortable, and Facetable. 
	
Field | Type | Options |
------|------|---------|
key | Edm.String | |
albumTitle | Edm.String | Retrievable, Searchable |
albumUrl | Edm.String | Retrievable, Searchable |
genre | Edm.String | Retrievable, Searchable, Filterable, Sortable, Facetable |
genreDescription | Edm.String | Retrievable, Searchable |
artistName | Edm.String | Retrievable, Searchable |
orderableOnline | Edm.Boolean | Retrievable, Filterable, Sortable, Facetable |
tags | Collection(Edm.String) | Retrievable, Filterable, Facetable |
price | Edm.Double | Retrievable, Filterable, Facetable |
margin | Edm.Int32 | |
rating | Edm.Int32 | Retrievable, Filterable, Sortable, Facetable |
inventory | Edm.Int32 | Retrievable |
lastUpdated | Edm.DateTimeOffset | |

As a point of comparison, the following screenshot is an illustration of an index built to the specification in the previous table.

 ![][4]

#### Step 3: Define the indexer

Still in the **Import data** wizard, click **Indexer** > **Name**, type a name for the indexer. This object defines an executable process. Once you create it, you could put it on recurring schedule, but for now use the default option to run the indexer immediately. 

Your import data entries should be all filled in and ready to go.

  ![][5]

To run the wizard, click **OK** to start the import.

To check progress, double-click the **Indexers** tile on the dashboard to open the indexers list. You should see the indexer you just created in the list, and you should see status indicating "in progress" or success, along with the number of documents indexed into Azure Search.

  ![][6]

## Query the index

You now have a search index that's ready to query. 

**Search explorer** is a query tool built into the portal. It provides a search box so that you can verify a search input returns the data you expect. 

1. Click **Search explorer** on the command bar.
2. Notice which index is active. If it's not the one you just created, click **Change index** on the command bar to select the one you want.
2. Leave the search box empty and then click the **Search** button to execute a wildcard search that returns all documents.
3. Enter a few full-text search queries, like Frank Zappa OR Santana.
4. Try other query syntax using the [examples provided at the end of this article](https://msdn.microsoft.com/library/azure/dn798927.aspx) for ideas, modifying your query to use search strings that are likely to be found in your index.

## Next steps

After you run the wizard once, you can go back and view or modify individual components: index, indexer, or data source. Some edits, such as the changing the field data type, are not allowed on the index, but most properties and settings are modifiable. To view individual components, click the **Index**, **Indexer**, or **Data Sources** tiles on your dashboard to display a list of existing objects.

To learn more about other features mentioned in this article, visit these links:

- [Indexers](search-indexer-overview.md)
- [Create Index (includes a detailed explanation of the index attributes)](https://msdn.microsoft.com/library/azure/dn798941.aspx)
- [Search Explorer](search-explorer.md)
- [Search Documents (includes examples of query syntax)](https://msdn.microsoft.com/library/azure/dn798927.aspx)

<a id="apdx-sampledata"></a>
## Appendix: Get sample data from this DocumentDB database

This section creates a small database in DocumentDB that can be used to complete the tasks in this tutorial.

The following instructions give you general guidance, but are not exhaustive. If you need more help with DocumentDB portal navigation or tasks, you can refer to DocumentDB documentation, but most of the commands you'll need are either in the service command bar at the top of the dashboard or in the database blade. 

  ![][1]

We provide three JSON data files for this dataset. Data is divided into three files to meet the upload requirements in Document Explorer. 

[Click here](https://github.com/Azure-Samples/) to get the music store JSON data files. 

1. Add DocumentDB to your subscription and then open the service dashboard.
2. Click **Add Database** to create a new database with an id of `musicstoredb`. It will show up in a database list further down the page after it's created.
2. Click on the database name to open the database blade.
3. Click **Add Collection** to create a collection with an id of `musicstorecoll`.
3. Click **Document Explorer**.
4. Click **Add Documents**.
5. In **Add Document**, upload JSON files in batches smaller than 100.
	- 386.json
	- 387.json
	- . . .
6. Click **Query Explorer** to verify the data is uploaded.
7. An easy way to do this is to modify the default query so that it selects the top 300 (there are less than 300 items in total), or you can write `select * from musicstorecoll`, and then click **Run Query**.

You should get back JSON output, starting with document number 386, and ending with document 669. Once the data is loaded, you can [use it to begin this walkthrough](#defineDS) using the Azure Search **Import wizard**.


<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
