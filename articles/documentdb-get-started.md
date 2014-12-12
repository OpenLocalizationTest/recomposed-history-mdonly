<properties title="Get started with the DocumentDB .NET SDK" pageTitle="Get started with a the DocumentDB .NET SDK | Azure" description="Learn how to create and configure an Azure DocumentDB account, create databases, create collections, and store JSON documents within your NoSQL document database account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="mimig" manager="jhubbard" editor="monicar" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="mimig" />

#Get started with the DocumentDB .NET SDK  

This guide shows you how to get started using [**Microsoft Azure DocumentDB (Preview)**](https://portal.azure.com/#gallery/Microsoft.DocumentDB) and the DocumentDB .NET SDK. DocumentDB is a NoSQL document database service, which has a number of APIs and SDKs available. The code samples in this article are written in C# and use the [DocumentDB .NET SDK](http://go.microsoft.com/fwlink/p/?linkid=402989), which is packaged and distributed as a NuGet package. 

The scenarios covered in this article include creating and configuring a DocumentDB account, creating databases, creating collections, and storing JSON documents within the account. Each of these samples are part of a complete solution available on [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started).

##Table of contents

-	[Prerequisites](#Prerequisites)
-	[Connect to a DocumentDB account](#Connect)
-	[Create a database](#CreateDB)
-	[Create a collection](#CreateColl)
-	[Create documents](#CreateDoc)
-	[Query DocumentDB resources](#Query)
-	[Next steps](#NextSteps)
##<a id="prerequisites"></a>Prerequisites
To use this getting started guide, you will need the following:

-   A [DocumentDB account][documentdb-create-account].  
-   The [DocumentDB .NET SDK](http://go.microsoft.com/fwlink/p/?linkid=402989), available through the [Nuget Package Manager for Visual Studio 2013](https://visualstudiogallery.msdn.microsoft.com/4ec1526c-4a8c-4a84-b702-b21a8f5293ca).

##<a id="Connect"></a>Connect to a DocumentDB account

We'll start by creating a new instance of the DocumentClient class in order to establish a connection to our DocumentDB account.   We'll need the following references in our C# application:  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
A DocumentClient can be instantiated using the DocumentDB account endpoint and either the primary or secondary access key associated with the account.  

The DocumentDB account endpoint and keys can be obtained from the [Azure preview management portal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) blade for your DocumentDB account. 

![][1]
 
>Note that the DocumentDB access keys available from the Keys blade grant administrative access to your DocumentDB account and the resources in it.  DocumentDB also supports the use of resource keys that allow clients to read, write, and delete resources in the DocumentDB account according to the permissions you've granted, without the need for an account key.    

Create the client using code like the following example.  

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

**Warning:** Never store credentials in source code. To keep this sample the credentials are shown in the source code. See [Windows Azure Web Sites: How Application Strings and Connection Strings Work](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) for information on how to store credentials.  

Now that you know how to connect to a DocumentDB account and create an instance of DocumentClient, let's take a look at working with DocumentDB resources.  

##<a id="CreateDB"></a>Create a database
Using the DocumentDB .NET SDK, a DocumentDB database can be created by using the **CreateDatabaseAsync** method of a DocumentClient.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Create a collection  

Using the DocumentDB .NET SDK, a DocumentDB collection can be created by using the **CreateDocumentCollectionAsync** method of a DocumentClient.  The database created in the previous step has a number of properties, one of which is the **CollectionsLink** property.  With that information, we can now create a collection.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>Create documents	
Using the DocumentDB .NET SDK, a DocumentDB document can be created by using the **CreateDocumentAsync** method of a DocumentClient.  The collection created in the previous step has a number of properties, one of which is the **DocumentsLink** property.  With that information, we can now insert one or more documents.  For the purposes of this example, we'll assume that we have a Family class that describes the attributes of a family such as name, gender, and age.  

    // Create the Andersen family document.
	Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents =  new Parent[] {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay"}
        },
        Children = new Child[] {
            new Child { 
                FirstName = "Henriette Thaulow", 
                Gender = "female", 
                Grade = 5, 
                Pets = new Pet[] {
                    new Pet { GivenName = "Fluffy" } 
                }
            } 
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, andersenFamily);
    
    // Create the WakeField family document.
    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        Parents = new Parent[] {
            new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
            new Parent { FamilyName= "Miller", FirstName= "Ben" }
        },
        Children = new Child[] {
            new Child {
                FamilyName= "Merriam", 
                FirstName= "Jesse", 
                Gender= "female", 
                Grade= 8,
                Pets= new Pet[] {
                    new Pet { GivenName= "Goofy" },
                    new Pet { GivenName= "Shadow" }
                }
            },
            new Child {
                FamilyName= "Miller", 
                FirstName= "Lisa", 
                Gender= "female", 
                Grade= 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, wakefieldFamily);
 

##<a id="Query"></a>Query DocumentDB resources
DocumentDB supports rich queries against the JSON documents stored in each collection.  The sample code below shows various queries - using both DocumentDB SQL syntax as well as LINQ - that we can run against the documents we inserted in the previous step.  

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQl with one join.
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join.
    items = client.CreateDocumentQuery<Family>(documentCollection.DocumentsLink)
        .SelectMany(family => family.Children
            .Select(children => new
            {
                family = family.Id,
                child = children.FirstName
            }));

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }
	
The full get-started sample is available for download from [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started).

##<a id="NextSteps"></a>Next steps
-	Learn how to [monitor a DocumentDB account](http://go.microsoft.com/fwlink/p/?LinkId=402378).
-	For details on the programming model, see the Development section on the [DocumentDB documentation page](http://go.microsoft.com/fwlink/p/?LinkID=402319 )


[Connect to a DocumentDB Account]: #Connect
[Create a database]: #CreateDB
[Create a collection]: #CreateColl
[Create documents]: #CreateDoc
[Query DocumentDB Resources]: #Query
[Next steps]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: ../documentdb-create-account/
[documentdb-manage]: ../documentdb-manage/

[1]: ./media/documentdb-get-started/gs1.png
