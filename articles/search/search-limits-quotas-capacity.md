<properties
	pageTitle="Service limits in Azure Search | Microsoft Azure | Hosted cloud search service"
	description="Service limits used in capacity planning and maximum limits on requests and reponses for Azure Search."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Service limits in Azure Search

Maximum limits on storage, workloads, and quantities of indexes, documents, and other objects depend on whether you add Azure Search at the **Free**, **Standard 1 (S1)**, or **Standard 2 (S2)** pricing tiers.

**Free** is a multi-tenant shared service that comes with your Azure subscription. It's a no-additional-cost option for existing subscribers that allows you to experiment with the service before signing up for dedicated resources. **Standard** runs on dedicated machines used only by your service. Sole use of a dedicated service gives you the ability to scale up or down, with more storage and processing capacity at every level, including the minimum configuration.

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

> [AZURE.NOTE] Queries-per-second (QPS) are variable, especially in the shared service, because throughput is based on available bandwidth and competition for system resources. The Azure compute and storage resources backing the shared service are shared by multiple subscribers, so QPS for your solution will always vary depending on how many other workloads are running at the same time. At the standard level, you can estimate QPS more closely because you have control over more of the parameters. See the best practices section in [Manage your search solution](search-manage.md) for guidance on how to calculate QPS for your workloads.

## API-key limits

Api-keys are used for service authentication. There are two types. Admin keys are specified in the request header and grant full read-write access to the service. Query keys are read-only, specified on the URL, and typically distributed to client applications.

- Maximum of 2 admin keys per service
- Maximum of 50 query keys per service

## Request limits

- Maximum of 16 MB per request <sup>1</sup>
- Maximum 8 KB URL length
- Maximum 1000 documents per batch of index uploads, merges, or deletes
- Maximum 32 fields in $orderby clause
- Maximum search term size is 32766 bytes (32 KB minus 2 bytes) of UTF-8 encoded text

## Response limits

- Maximum 1000 documents returned per page of search results
- Maximum 100 suggestions returned per Suggest API request

<sup>1</sup> In Azure Search, the body of a request is subject to an upper limit of 16 MB, imposing a practical limit on the contents of individual fields or Collections that are not otherwise constrained by theoretical limits (see [Supported data types](https://msdn.microsoft.com/library/azure/dn798938.aspx) for more information about field composition and restrictions). 