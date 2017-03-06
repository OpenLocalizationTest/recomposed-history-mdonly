---
title: 'What is DocumentDB: API for MongoDB? | Microsoft Docs'
description: 'Learn about DocumentDB: API for MongoDB and how you can easily run existing MongoDB applications in the Azure cloud'
keywords: what is MongoDB
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''

ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh

---
# How does Azure DocumentDB work with MongoDB?

DocumentDB databases can now be used as the data store for apps written for MongoDB. This means that by using existing [drivers](https://docs.mongodb.org/ecosystem/drivers/) for MongoDB databases, your application written for MongoDB can now communicate with DocumentDB and use DocumentDB databases instead of MongoDB databases. In many cases, you can switch from using MongoDB to DocumentDB by simply changing a connection string. Using this functionality, customers can easily build and run MongoDB database applications in the Azure cloud - leveraging DocumentDB's fully managed and scalable NoSQL databases - while continuing to use familiar skills and tools for MongoDB.

## What is the benefit of using DocumentDB: API for MongoDB?
**No Server Management** - DocumentDB is a fully managed service, which means you do not have to manage any infrastructure or Virtual Machines yourself. DocumentDB is available in 20+ [Azure Regions](https://azure.microsoft.com/regions/services/).

**Limitless Scale** - You can scale throughput and storage independently and elastically. You can add capacity to serve millions of requests per second with ease.

**Enterprise grade** - DocumentDB supports multiple local replicas to deliver 99.99% availability and data protection in the face of local and regional failures. DocumentDB has enterprise grade [compliance certifications](https://www.microsoft.com/trustcenter) and security features. 

**MongoDB Compatibility** - DocumentDB: API for MongoDB is designed for compatability with MongoDB. You can use your existing code, applications, drivers, and tools to work with DocumentDB. 

Learn more in this Azure Friday video with Scott Hanselman and DocumentDB Principal Engineering Manager, Kirill Gavrylyuk.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## How to get started?
Create a DocumentDB: API for MongoDB account in the [Azure Portal](https://portal.azure.com) and swap the connection to your new account. 

*And, that's it!*

For more detailed instructions, follow [create account](documentdb-create-mongodb-account.md) and [connect to your account](documentdb-connect-mongodb-account.md).

## Next steps
* Follow the [Connect to a MongoDB account](documentdb-connect-mongodb-account.md) tutorial to learn how to get your account connection string information.
* Follow the [Use MongoChef with DocumentDB](documentdb-mongodb-mongochef.md) tutorial to learn how to create a connection between your DocumentDB database and MongoDB app in MongoChef.
* Explore DocumentDB: APIs for MongoDB [samples](documentdb-mongodb-samples.md).

