<properties 
	pageTitle="DocumentDB Java SDK | Microsoft Azure" 
	description="Learn all about the Java SDK including release dates, retirement dates, and changes made between each version of the DocumentDB Java SDK." 
	services="documentdb" 
	documentationCenter="java" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Java SDK

<table>
<tr><td>**Download**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr>
<tr><td>**Contribute**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Documentation**</td><td>[Java SDK Reference Documentation](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Get Started**</td><td>[Get started with the Java SDK](https://azure.microsoft.com/en-us/documentation/articles/documentdb-java-application/)</td></tr>
</table></br>

## Release Notes

### <a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Implement Upsert. New upsertXXX methods added to support Upsert feature.
- Implement ID Based Routing. No public API changes, all changes internal.

### <a name="1.3.0"/>[1.3.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.3.0)
- Release skipped to bring version number in alignment with other SDKs

### <a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Supports GeoSpatial Index
- Validates id property for all resources. Ids for resources cannot contain ?, /, #, \, characters or end with a space.
- Adds new header "index transformation progress" to ResourceResponse.

### <a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- Implements V2 indexing policy

### <a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- GA SDK

## Release & Retirement Dates
Microsoft will provide notification at least **12 months** in advance before retiring an SDK in order to smooth the transition to a newer/supported version.

We strongly recommend that you upgrade to the latest version as early as possible. 
It is recommended that you begin application upgrades now in order to avoid being impacted when the earlier service versions are removed.

| Version | Release Date | Retirement Date 
| ---	  | ---	         | ---
| [1.4.0](#1.4.0) | October 05 2015 |---
| [1.3.0](#1.3.0) | October 05 2015 |---
| [1.2.0](#1.2.0) | August 05, 2015 |---
| [1.1.0](#1.1.0) | July 09, 2015 |---
| [1.0.1](#1.0.1) | May 12, 2015 |---
| [1.0.0](#1.0.0) | April 07, 2015 |---
| 0.9.5-prelease | Mar 09, 2015 | ---
| 0.9.4-prelease | February 17, 2015 | ---
| 0.9.3-prelease | January 13, 2015 | ---
| 0.9.2-prelease | December 19, 2014 | ---
| 0.9.1-prelease | December 19, 2014 | ---
| 0.9.0-prelease | December 10, 2014 | ---

## FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## See Also

To learn more about DocumentDB, see [Microsoft Azure DocumentDB](https://azure.microsoft.com/en-us/services/documentdb/) service page. 
