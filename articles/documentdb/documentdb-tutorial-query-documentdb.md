---
title: How to query in Azure Cosmos DB? | Microsoft Docs
description: Learn to query with the different data models in Azure Cosmos DB
services: documentdb
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: ''
tags: ''

ms.assetid: 
ms.service: documentdb
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/19/2017
ms.author: mimig

---

# How to query with Azure Cosmos DB?

Each of the Azure Cosmos DB data-models have their own query protocol, so creating queries for each model is slightly different. This article provides links to the query protocol for each model, as well as sample queries for the DocumentDB API.

**How to query each data model using Azure Cosmos DB?**

|   |DocumentDB API|Tables API|Graph API|MongoDB API|
|---|-----------------|--------------|-------------|---------------|
|Query protocol|[SQL](documentdb-sql-query.md)|[OData](https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities)<br>[LINQ](ttps://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service)|[Gremlin](http://tinkerpop.apache.org/gremlin.html)|[MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/)|
|Example queries|[Document query](#documentdbquery)|Table query|Graph query|[MongoDB query](documentdb-tutorial-query-mongodb.md)|

The queries in this article use the following sample document.

## Sample Family document

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a id="documentdbquery"></a> How to query with DocumentDB API?

Given the sample family document above, following SQL query returns the documents where the id field matches `WakefieldFamily`. Since it's a `SELECT *` statement, the output of the query is the complete JSON document:

**Query**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Results**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

The next query returns all the given names of children in the family whose id matches `WakefieldFamily` ordered by their grade.

**Query**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.children.grade ASC

**Results**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


## Next steps

For more information about querying document data with SQL queries, see [SQL](documentdb-sql-query.md), and print out the [SQL Query Cheat Sheet](documentdb-sql-query-cheat-sheet.md).