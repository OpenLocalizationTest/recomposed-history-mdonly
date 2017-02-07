---
title: Performance levels in DocumentDB | Microsoft Docs
description: Learn about how performance levels in DocumentDB enable you to reserve throughput on a per collection basis.
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''

ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2016
ms.author: mimig

---
# Performance levels in DocumentDB

> [!IMPORTANT] 
> The S1, S2, and S3 performance levels discussed in this article are being retired and are no longer available for new DocumentDB collections.
>

This article provides an overview of S1, S2, and S3 performance levels, and discusses how the collections that use these performance levels will be migrated to the new Standard pricing tier on August 1st, 2017. After reading this article, you'll be able to answer the following questions:

- [Why are the S1, S2, and S3 performance levels being retired?](#why-retired)
- [How does the Standard pricing tier compare to the S1, S2, S3 performance levels?](#compare)
- [What do I need to do to ensure uninterrupted access to my data?](#uninterrupted-access)
- [How will my collection change after the migration?](#collection-change)
- [How will my billing change after I’m migrated to the Standard pricing tier?](#billing-change)
- [What if I need more than 10 GB of storage?](#more-storage-needed)
- [Can I change between the S1, S2, and S3 performance levels before August 1, 2017?](#change-before)
- [How will I know when my collection has migrated?](#when-migrated)
- [How do I migrate from the S1, S2, S3 performance levels to the Standard pricing tier on my own?](#migrate-diy)

<a name="why-retired"></a>

## Why are the S1, S2, and S3 performance levels being retired?

The S1, S2, and S3 performance levels do not offer the flexibility that the new DocumentDB Standard pricing tier offers. With the S1, S2, S3 performance levels, both the throughput and storage capacity were pre-set. DocumentDB now offers the ability to customize your throughput and storage, offering you much more flexibility in your ability to scale as your needs change.

<a name="compare"></a>

## How does the Standard pricing tier compare to the S1, S2, S3 performance levels?

The following table compares the throughput and storage options available in Standard pricing and the S1, S2, S3 performance levels. Here is an example for US East 2 region:

|   |Standard with partitioned collection|Standard with single partition collection|S1|S2|S3|
|---|---|---|---|---|---|
|Maximum throughput|Unlimited|10K RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Minimum throughput|2.5K RU/s|400 RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Maximum storage|Unlimited|10 GB|10 GB|10 GB|10 GB|
|Price|Throughput: $6 / 100 RU/s<br><br>Storage: $0.25/GB|Throughput: $6 / 100 RU/s<br><br>Storage: $0.25/GB|$25 USD|$50 USD|$100 USD|

<a name="uninterrupted-access"></a>

## What do I need to do to ensure uninterrupted access to my data?

Nothing, DocumentDB handles the migration for you. If you have an S1, S2, or S3 collection, your current collection will be migrated to the Standard performance tier on July 31, 2017. 

<a name="collection-change"></a>

## How will my collection change after the migration?

If you have an S1 collection, you will be migrated to a single partition collection with 400 RU/s throughput. 400 RU/s is the lowest throughput available with single partition collections. However, the cost for 400 RU/s in the new standard pricing tier is approximately the same as you were paying with your S1 collection and 250 RU/s – so you are not paying for the extra 150 RU/s available to you.

If you have an S2 collection, you will be migrated to a single partition collection with 1 K RU/s. You will see no change to your throughput level.

If you have an S3 collection, you will be migrated to a single partition collection with 2.5 K RU/s. You will see no change to your throughput level.

In each of these cases, after your collection is migrated, you will be able to customize your throughput level, or scale it up and down as needed to provide low-latency access to your users. To change the throughput level after your collection has migrated, simply open your DocumentDB account in the Azure portal, click Scale, choose your collection, and then adjust the throughput level, as shown in the following screenshot:

![How to scale throughput in the Azure portal](./media/documentdb-performance-levels/azure-documentdb-portal-scale-throughput.png)

<a name="billing-change"></a>

## How will my billing change after I’m migrated to the Standard pricing tier?

Assuming you have 10 S1 collections, 1 GB of storage for each, in the US East region, and you migrate these 10 S1 collections to 10 single partition collections at 400 RU/sec (the minimum level). Your bill will look as follows if you keep the 10 single partition collections for a full month:

![How S1 pricing for 10 collections compares to 10 collections using Standard pricing](./media/documentdb-performance-levels/documentdb-s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## What if I need more than 10 GB of storage?

Whether you have a collection with an S1, S2, or S3 performance level, or have a single partition collection with the Standard pricing tier, all of which have 10 GB of storage available, you can use the DocumentDB Data Migration tool to migrate your data to a partitioned collection with virtually unlimited storage. For information about the benefits of a partitioned collection, see [Partitioning and scaling in Azure DocumentDB](documentdb-partition-data.md). For information about how to migrate your S1, S2, S3, or single partition collection to a partitioned collection, see [Migrating from single-partition to partitioned collections](documentdb-partition-data.md#migrating-from-single-partition). 

<a name="change-before"></a>

## Can I change between the S1, S2, and S3 performance levels before August 1, 2017?

Only existing accounts with S1, S2, and S3 performance will be able to change and alter performance level tiers through the portal or programmatically. By August 1, 2017, the S1, S2, and S3 performance levels will no longer be available.

<a name="when-migrated"></a>

## How will I know when my collection has migrated?

The migration will occur on July 31, 2017. If you have a collection that uses the S1, S2 or S3 performance levels, the DocumentDB team will contact you by email before the migration takes place. Once the migration is complete, on August 1, 2017, the Azure portal will show that your collection uses Standard pricing

![How to confirm your collection has migrated to the Standard pricing tier](./media/documentdb-performance-levels/documentdb-portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## How do I migrate from S1, S2, S3 performance levels to the Standard pricing tier?

You can migrate from the S1, S2, and S3 performance levels to the Standard pricing tier using the Azure portal or programmatically. You can do this on your own before August 1 to benefit from the flexible throughput options available with the Standard pricing tier, or we will migrate your collections for you on July 31, 2017.

**To migrate to Standard pricing using the Azure portal**

1. In the [**Azure portal**](https://portal.azure.com), click **NoSQL (DocumentDB)**, then select the DocumentDB account to modify. 
 
    If **NoSQL (DocumentDB)** is not on the Jumpbar, click >, scroll to **Databases**, select **NoSQL (DocumentDB)**, and then select the DocumentDB account.  

2. On the resource menu, under **Collections**, click **Scale**, select the collection to modify from the drop-down list, and then click **Pricing Tier**. Accounts using pre-defined throughput have a pricing tier of S1, S2, or S3.  In the **Choose your pricing tier** blade, click **Standard** to change to user-defined throughput, and then click **Select** to save your change.

    ![Screen shot of the Settings blade showing where to change the throughput value](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

3. Back in the **Scale** blade, the **Pricing Tier** is changed to **Standard** and the **Throughput (RU/s)** box is displayed with a default value of 400. Set the throughput between 400 and 10,000 [Request units](documentdb-request-units.md)/second (RU/s). The **Estimated Monthly Bill** at the bottom of the page updates automatically to provide an estimate of the monthly cost. 

    >[!IMPORTANT] 
    > Once you save your changes and move to the Standard pricing tier, you cannot roll back to the S1, S2, or S3 performance levels.

4. Click **Save** to save your changes.

    If you determine that you need more throughput (greater than 10,000 RU/s) or more storage (greater than 10GB) you can create a partitioned collection. To migrate a single partition collection to a partitioned collection, see [Migrating from single-partition to partitioned collections](documentdb-partition-data.md#migrating-from-single-partition).

    > [!NOTE]
    > Changing from S1, S2, or S3 to Standard may take up to 2 minutes.
    > 
    > 

**To migrate to Standard pricing using the .NET SDK**
Another option for changing your collections' performance levels is through our SDKs. This section only covers changing a collection's performance level using our [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx), but the process is similar for our other [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx). If you are new to our .NET SDK, visit our [getting started tutorial](documentdb-get-started.md).

Here is a code snippet for changing the offer throughput to 5,000 request units per second:
    
    ```c#
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
    ```


> [!NOTE]
> Collections provisioned with under 10,000 request units per second can be migrated between offers with user-defined throughput and pre-defined throughput (S1, S2, S3) at any time. Collections that are provisioned with above 10,000 request units per second cannot be converted to pre-defined throughput levels.
> 
> 

Visit [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) to view additional examples and learn more about our offer methods:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## Next steps
To learn more about pricing and managing data with Azure DocumentDB, explore these resources:

1.	[Partitioning data in DocumentDB](documentdb-partition-data.md): understand the difference between single partition and partitioned collection, tips on partitioning strategy to scale seamlessly
2.	[DocumentDB pricing](https://azure.microsoft.com/pricing/details/documentdb/): Learn about the cost of provisioning throughput and consuming storage
3.	[Request units](documentdb-request-units.md): Understand the consumption of throughput for different operation type (for example: Read, Write, Query)
4.	[Modeling data in DocumentDB](documentdb-modeling-data.md)