<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

[WACOM.INCLUDE [vs-storage-aspnet-vnext-getting-started-intro](../includes/vs-storage-aspnet-vnext-getting-started-intro.md)]

# Getting Started with Azure Storage

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs" title="Blobs" class="current">Blobs</a><a href="/en-us/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues" title="Queues">Queues</a><a href="/en-us/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables" title="Tables">Tables</a></div>

The Azure Table storage service enables you to store large amounts of structured data. The service is a NoSQL datastore that accepts authenticated calls from inside and outside the Azure cloud. Azure tables are ideal for storing structured, non-relational data.  See [How to use Table Storage from .NET](http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET") for more information.

To programmatically access tables in ASP.NET vNext projects, you need to do the following tasks.

1.	Get the Microsoft.WindowsAzure.Storage.dll assembly. You can use NuGet to do this. Right-click your project in Solution Explorer and choose Manage NuGet Packages. Search online for "WindowsAzure.Storage" and click Install to install the Azure Storage package and dependencies. Add a reference to this assembly to your project.
2.	Install the Microsoft.Framework.ConfigurationModel.Json NuGet package.
3.	Add the following code namespace declarations to the top of any C# file in which you wish to programmatically access Azure Storage.

	using Microsoft.Framework.ConfigurationModel;
	...
	var config=new Configuration();
	config.AddXxxFile("config.json");

###Get the storage connection string
Before you can do anything with a table, you need to get the connection string for the storage account the tables will live in. You can use the **CloudStorageAccount** type to represent your storage account information. If you’re using an ASP.NET vNext project, you can you call the get method of the Configuration object to get your storage connection string and storage account information from the Azure service configuration, as shown in the following code.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]
