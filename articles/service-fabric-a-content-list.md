<properties
   pageTitle="Scratch TOC- Don't Publish"
   description=""
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/18/2015"
   ms.author="ryanwi"/>

# Working List of Service Fabric Content
We will not be publishing this topic.  The ACOM landing page, learning map, and left-hand navigation TOCs will be built from this content.
## About
[Technical Overview](../service-fabric-technical-overview)- Combines elements of what is, features and capabilities, key concepts: Cluster, nodes, applications, services, Programming models. **owner: mfussell**

Learning Map P2 **owner: adegeo**

[Application Scenarios](../service-fabric-application-scenarios) **owners: mfussell**

PaaS v2 compared to other things(P2)- short blurb, point to competitive analysis topics **owner: claudioc**

PaaS v2 vs Cloud Services- PaaS v2 is so much better, technical comparison. Is this really technical marketing info? **owner: mfussell**

## Getting Started
Sign Up for a Free Account  **owner: vturecek**

[Setup your development environment](../service-fabric-setup-your-development-environment) **owner: seanmck**

Run HelloWorld Quick Start- Deploy Locally, Test, Deploy to Cloud **owner: seanmck**

Install and extend the Samples **owner: vturecek**


## Work with Applications and Services- task oriented, just enough conceptual info to perform a task.  Links to Further Reading sections.

### Develop a Service owner: vturecek

Choose a Programming Model **owner: seanmck**

Create a Stateful Application **owner: Haishi**

[Getting Started with Microsoft Azure Service Fabric Stateless Services](../service-fabric-stateless-helloworld) **owner: Haishi**

How to Create an ASP.NET v5 Application **owner: Haishi**

Create Windows Fabric Stateful  applications using .NET Distributed Collections (C#)

Create Stateless and Stateful applications using .NET Actor Framework  (volatile, durable stateful)

[Getting Started with FabSrv programming model](../service-fabric-fabsrv-getting-started) **owners: masnider, alanwar, tyadam, sumukhs**

Communicate with a Service

Add Diagnostics code to a service **owner: Kunalds**

### Configure a Service Manifest P2  owner: vturecek
more advanced configuration stuff

### Configure an Application Manifest P2 owner: vturecek
more advanced configuration stuff

### Package and Deploy an Application P2
Package and deploy using VS. Explain VS template. **owner: vturecek**

Package and deploy using Cmdlets **owner: vturecek**

Create an Application Instance (PowerShell) **owner: vturecek**

Create a Service Instance (PowerShell) **owner: vturecek**

More advanced conceptual topics, this handled by VS **owner: vturecek**

### Test a Service owner: vturecek, rsinha
What is Testability - P1 **owner: vturecek**

Why is Testability important - P1 - **owner: rsinha**

How does it work - P1 **owner: anmola**

Run an action with PowerShell - P1 **owner: vturecek**

Run an action with C# - P1 **owner: vturecek**

Run a scenario with PowerShell - P1 **owner: vturecek**

Run a scenario with C# - P1 **owner: vturecek**

Run the Chaos Test - P1 **owner: anmola**

Validating and troubleshooting - P1 **owner: anmola**

Testing during development - P2 **owner: rsinha**

Testing in test/staging - P2 **owner: rsinha**

Testing in production - P2 **owner: rsinha**

Writing a custom test scenario - P3 **owner: vturecek**


### Troubleshoot and monitor services locally owners: kunalds, toddpf
Connect to and view the local development cluster **owner: Kunalds**

View system events fromm the local development cluster using Visual Studio **owner: Kunalds**

Write trace events from a service **owner: Kunalds**

View trace events for a service using Visual Studio **owner: Kunalds**

Debug a service in Visual Studio using F5 **owner: Kunalds**

### Upgrade an Application owner: Mani
Perform an un-monitored automatic application upgrade **owner: alexwun**

Perform a monitored automatic application upgrade **owner: alexwun**

Perform an un-monitored manual application upgrade **owner: alexwun**

Upgrade an application (PowerShell) **owner: alexwun**

Handle application upgrade failures **owner: alexwun**

### Scale Services and Partitions owner: vturecek

### Troubleshoot and monitor deployed services P2 (Phosphorus?)
Setup Azure Diagnostics and OpInsights to collect diagnostics **owner: Kunalds**

Setup AppInsights to collect metrics and alerts **owner: Kunalds**

Identify slow performance requests in Service Fabric **owner: Kunalds**

Identify slow performance requests in an Actor **owner: Kunalds**

Troubleshoot an application upgrade **owner: Kunalds**

View application upgrade history **owner: Kunalds**

View performance data **owner: Kunalds**

Download crash dumps **owner: Kunalds**

View health information in Visual Studio **owner: oanapl**


### Query and Evaluate Application, Service, Partition, Replica Health  Owners: kunalds, oanapl

Query Health State and Events (PowerShell) **owner: oanapl**

### Add/Remove Services from Applications P2 owner: vturecek

### Remove an Application owner: vturecek
Remove an Application in Visual Studio **owner: vturecek**

Remove an Application **owner: vturecek**

Remove a Service and Application (PowerShell) **owner: vturecek**

## Reference
### Managed Reference
### PowerShell Reference
### REST Reference
### Application Manifest Schema and Settings P2
### Service Manifest Schema and Settings P2
### Cluster Manifest Schema and Settings (Phosphorus)

## Further Reading- Deep conceptual content, link back to Work with... topics
### Monitoring and Diagnostics
Introduction to monitoring and diagnostics in Service Fabric- overview, crash dump, perf counters, AppInsights, OpInsights, WAD. **owner: kunalds**

### Application Lifecycle
[Application Lifecycle](../service-fabric-application-lifecycle) **owner: matt**

### Health
[Azure Service Fabric Health](service-fabric-health-introduction.md) **owner: oanapl**

### Programming Models Owners: masnider, claudioc, vipulm, richhas
#### Actors
- [Introduction to the Service Fabric Actor Model](../service-fabric-fabact-introduction) **owner: claudioc**

- [Actor Model Design Patterns](../service-fabric-fabact-patterns-introduction)  **owner: claudioc**

- [Pattern: Smart Cache](winfab-fabact-pattern-smartcache.md) **owner: claudioc**

- [Pattern: Distributed Networks and Graphs](service-fabric-fabact-pattern-distributed-networks-and-graphs.md) **owner: claudioc**

- [Pattern: Resource Governance](service-fabric-fabact-pattern-resource-governance.md) **owner: claudioc**

- [Pattern: Stateful Service Composition](service-fabric-fabact-pattern-stateful-service-composition.md) **owner: claudioc**

- [Pattern: Internet of Things](service-fabric-fabact-pattern-internet-of-things.md) **owner: claudioc**

- [Pattern: Distributed Computation](service-fabric-fabact-pattern-distributed-computation.md) **owner: claudioc**

- [Some Anti-patterns](service-fabric-fabact-anti-patterns.md) **owner: claudioc**

#### Services
- [Programming Model Overview](../service-fabric-fabsrv-service-overview)  **owners: masnider, richhas**

- [Architecture](../service-fabric-fabsrv-platform-architecture) **owner: alanwar**

- [Reliable Collections](../service-fabric-fabsrv-service-reliable-collection) **owners: mcoskun, tyadam**

- [API Discussion](../service-fabric-fabsrv-service-api-description) **owners: tyadam, mcoskun**

- [Operations](../service-fabric-fabsrv-managing-your-services) **owner: jesseb, alanwar**

### PaaS v2 and Cloud Applications
### Applications, Services, Partitions, Replicas, and Replica Sets
### Cluster
### Service State
### Service Model
### Application and Service Types
### Names and Properties/Naming Service
### Image Store


### System Services
Failover Manager Service

Cluster Manager Service

ImageStore Service

Naming Service
### Cluster Lifecycle
### Management APIs
### Application Security
### Cluster and Client Security
 Node-to-Node Security

 Client-to-Node Security

### Resource Balancing a Cluster
Balancing Strategies

Describe the Cluster

Describe Services

Proactive Metric Packing

### PaaSv2 compared to other systems...

### Replicas and Replica Sets owners: *aprameyr*
Replica Role and State Lifetime

 Replication and State Consistency

 The Copy Process

 Epochs, LSNs, and Progress Vectors

### Service Host Activation and Deactivation

### FabricExplorer

### Glossary
