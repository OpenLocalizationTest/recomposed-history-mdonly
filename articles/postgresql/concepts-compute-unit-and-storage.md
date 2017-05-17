---
title: 'Explaining Azure DB for PostgreSQL Compute Units and Storage Units  | Microsoft Docs'
description: 'Azure DB for PostgreSQL: Explains Compute Unit and Storage Unit and what happens when you hit the maximum Compute Unit or Storage Unit.'
services: postgresql
author:
ms.author:
manager: jhubbard
editor: jasonh
ms.assetid:
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/17/2017
---
# Explaining Compute Units and Storage
This article explains the concepts of Compute Units and Storage and what happens when you hit the maximum Compute Units or Storage.

## What are Compute Units?
Compute Units are a measure of CPU processing throughput that is guaranteed to be available to a single Azure Database for PostgreSQL server. A Compute Unit is a blended measure of CPU and memory resources. In general, 50 Compute Units equate to half-core, 100 Compute Units equate to one core, and 2000 Compute Units equate to twenty cores of guaranteed processing throughput available to your server. 

The amount of memory per Compute Unit is optimized for the Basic and Standard service tiers. Doubling the Compute Units by increasing the performance level equates to doubling the set of resource available to that single Azure Database for PostgreSQL. 

For example, a Standard 2000 Compute Units provides 20x more CPU throughput and memory than a Standard configured with 100 Compute Units. However, while Standard 100 Compute Units provide the same CPU throughput compared to Basic 100 Compute Units, the amount of memory that is pre-configured in Standard tier is double the amount of memory configured for Basic tier and therefore provides better workload performance and lower transaction latency.

>[!IMPORTANT]
>For predictable workload performance throughput and high user concurrency, it is recommended you choose the Standard service tier.

You can change [service tiers](concepts-service-tiers.md) at any time with virtually no application downtime. For many businesses and apps, being able to create one to many databases within each single Azure Database for PostgreSQL server, and dialing the performance up or down on demand provides the needed flexibility to manage costs.

>[!IMPORTANT]
>Currently we support scaling up/down performance levels within a service tier. For example, you can scale up from Standard 100 Compute Units to Standard 400 Compute Units. Similarly, you can scale down from Standard 400 Compute Units to Standard 100 Compute Units. Scaling across the service tiers, for example from Basic tier to Standard tier, is not yet available but will be in the future.

## Configuring Storage
The Storage configuration defines the amount of capacity available to a Azure Database for PostgreSQL server. Each service tier has a fixed amount of included storage as described in the table below. 

Additional storage capacity can be added when a new server is created, in increments of 125 GB, up to the maximum allowed storage as described in the table below. The additional storage capacity can be configured independently of the Compute Units configuration. 

| **Service tier features** | **Basic** | **Standard** |
|---------------------------|-----------|--------------|
| Included Storage | 50 GB | 125 GB |
| Maximum total storage | 1 TB | 1 TB |
| Storage IOPS guarantee | N/A | Yes |
| Maximum storage IOPS | N/A | 3,000 |

Basic tier does not provide any IOPS guarantee. Standard tier provides a provisioned IOPS guarantee, and is fixed to 3 times the provisioned storage.  For example in standard tier, when you select 125 GB of storage capacity, there is 375 provisioned IOPS available to your server. 

You can monitor the consumption of storage and provisioned IOPS via the Azure portal or Azure CLI.

>[!IMPORTANT]
> Once provisioned, Storage cannot be dynamically scaled down.

## How can I determine the number of Compute Units needed for my workload?
If you are looking to migrate an existing PostgreSQL server running on-premise or on a virtual machine, you can determine the number of Compute Units by estimating how many cores of processing throughput your workload needs. 

If the on-premise or virtual machine is currently utilizing 4 cores (without counting CPU hyperthread), start by configuring 400 Compute Units in your Azure Database for PostgreSQL. Compute Units can be dynamically scaled up or down depending on your workload needs with virtually no application downtime. You can monitor the consumption of Compute Units via the Azure portal or Azure CLI in effort to optimize the resources.

>[!IMPORTANT]
> It is recommended that you monitor and scale compute units if you are not able to fully utilize the available provisioned IOPS.

## What happens when I hit my maximum Compute Units or Storage?
Performance levels are calibrated and governed to provide resources to run your database workload up to the max limits for selected service tier/performance level. 

If your workload is hitting the maximum limits in either the Compute Units or provisioned IOPS limits, you can continue to utilize the resources at the maximum allowed level, but your queries are likely to see increased latencies. These limits do not result in any errors, but rather a slowdown in the workload, unless the slowdown becomes so severe that queries time out. 

If you workload hits the limits of maximum connections allowed, you see explicit errors. See [Azure Database for PostgreSQL resource limits](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits) for more information on limit on resources.

## Next steps
- See [Azure Database for PostgreSQL service tiers](./concepts-service-tiers.md) for information on the Compute Units and Storage Units available for single servers.

