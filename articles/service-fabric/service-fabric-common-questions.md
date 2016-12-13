<properties
   pageTitle="Common questions about Service Fabric | Microsoft Azure"
   description="Frequently asked Service Fabric questions and their answers"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/09/2016"
   ms.author="seanmck"/>

# Commonly asked Service Fabric questions

There are many commonly asked questions about what Service Fabric can do and how it should be used. This document covers many of those common questions and their answers.

## Cluster setup and management

### Can I create a cluster that spans multiple Azure regions?

Not today, but this is a common request that we continue to investigate.

The core Service Fabric clustering technology knows nothing about Azure regions and can be used to combine machines running anywhere in the world, so long as they have network connectivity to each other. However, the Service Fabric cluster resource in Azure is regional, as are the virtual machine scale sets that the cluster is built on. In addition, there is an inherent challenge in delivering strongly consistent data replication across a set of machines spread across a wide geographic region. We want to ensure that performance is predictable and acceptable before supporting cross-regional clusters.

### Do Service Fabric nodes automatically receive OS updates?

Not today, but this is also a common request that we intend to deliver.

The challenge with OS updates is that they typically require a reboot of the machine, which results in temporary availability loss. By itself, that is not a problem, since Service Fabric will automatically redirect traffic for those services to other nodes. However, if OS updates are not coordinated across the cluster, there is the potential that many nodes will go down at once, causing complete availability loss for the service, or at least for a specific partition (for a stateful service).

In the future, we will support an OS update policy that is coordinated across update domains, ensuring that availability is maintained despite reboots and other unexpected failures.

In the interim, the only safe option is to perform OS updates manually, one node at a time.

### What is the minimum size of a Service Fabric cluster? Why can't it be smaller?

The minimum supported size for a Service Fabric cluster running production workloads is 5 nodes. For dev/test scenarios, we support 3 node clusters, although these must be provisioned from the command line or PowerShell using an ARM template, as they cannot be created using the Azure portal.

To understand why these minimums exist, it is important to understand that the Service Fabric cluster itself runs a number of stateful services, including the naming service and the failover manager. These services, which keep track of what services have been deployed to the cluster and where they're currently hosted, depend on the strong consistency inherent in the Service Fabric data model. That strong consistency, in turn, depends on the ability to acquire a "quorum" for any given update to the state of those services, where a quorum represents a strict majority of the replicas for a given service.

With that background, let's examine some possible cluster configurations:

**1 node**: this option does not provide high availability since the loss of the single node for any reason means the loss of the entire cluster.

**2 nodes**: a quorum for a service deployed across 2 nodes (N = 2) is 2 (2/2 + 1 = 2). Thus, as soon as a single replica is lost, it is impossible to create a quorum. Since performing a service upgrade requires temporarily taking down a replica, this is not a useful configuration.

**3 nodes**: with 3 nodes, the requirement to create a quorum is still 2 nodes (3/2 + 1 = 2). This means that you can lose an individual node and still maintain quorum.

The 3 node cluster configuration is supported for dev/test because you can safely perform upgrades and survive individual node failures, as long as they don't happen simultaneously. For production workloads, you must be resilient to such a simultaneous failure.

### Can I turn off my cluster at night/weekends to save costs?

In general, no. Service Fabric stores state on ephemeral disks, meaning that if the virtual machine is moved to a different host, the data will not move with it. In normal operation, that is not a problem as the new node will be brought up to date by other nodes. However, if you stop all nodes and restart them later, there is a significant possibility that most of the nodes start on new hosts and thus the system is unable to recover.

If you would like to create clusters for testing your application before it is deployed, we recommend that you dynamically create those clusters as part of your [continuous integration/continuous deployment pipeline](service-fabric-continuous-integration.md).

## Application Design

### What's the best way to query data across partitions of a Reliable Collection?

Reliable collections depend on partitioning to enable scale out for greater performance and throughput. That means that the state for a given service may be spread across 10s or 100s of machines. In order to perform operations over that full data set, you have a few options:

- Create a service that queries all partitions of another service to pull in the required data.
- Create a service that can receive data from all partitions of another service.
- Periodically push data from each service to an external store. This approach is only appropriate if the queries you're performing are not part of your core business logic.

### What's the best way to query data across my actors?

Actors are designed to be independent units of state and compute, so it is not recommended to perform broad queries of actor state at runtime. If you have a need to query across the full set of actor state, you should consider either:

- Replacing your actor services with stateful reliable services, such that the number of network requests to gather all data from the number of actors to the number of partitions in your service.
- Designing your actors to periodically push their state to an external store for easier querying. As above, this approach is only viable if the queries you're performing are not required for your runtime behavior.

### How much data can I store in a Reliable Collection?

Reliable collections are partitioned, meaning that the amount you can store is only limited by the number of machines you have in the cluster, and the amount of memory available on those machines.

### How much data can I store in an actor?

As with reliable collections, there is no fixed limit on the amount of data that you can store in an actor. However, actors are most effective when they are used to encapsulate a small amount of state and associated business logic. As a general rule, an individual actor should have state that is measured in kilobytes.

### How does Service Fabric relate to containers?

Containers offer a simple way to package services and their dependencies such that they run consistently in all environments and can operate in an isolated fashion on a single machine. Service Fabric offers a way to deploy and manage services, including services that have been packaged in a container.

## Next steps

- [Learn about core Service Fabric concepts and best practices](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)
