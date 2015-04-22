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
- [Overview](service-fabric-overview.md) **owner: mfussell**

- [Technical Overview](service-fabric-technical-overview.md)- Combines elements of what is, features and capabilities, key concepts: Cluster, nodes, applications, services, Programming models. **owner: mfussell**

- [Application Scenarios](service-fabric-application-scenarios.md) **owners: mfussell**

- Learning Map P2 **owner: adegeo**

## Getting Started

[Setup your development environment](service-fabric-setup-your-development-environment.md) **owner: seanmck**

Run HelloWorld Quick Start- Deploy Locally, Test, Deploy to Cloud **owner: seanmck**

Install and extend the Samples **owner: vturecek**


## Work with Applications and Services- task oriented

just enough conceptual info to perform a task.  Links to Further Reading sections.

### Develop a Service; owner: vturecek

Choose a Programming Model **owner: seanmck**

#### Manage Services

Create basic management tutorial **owner: Brad**
[Microsoft Azure Service Fabric Application Basic Management](../service-fabric-get-started-basic-management.md)


#### Create Framework Services

Create a Stateful Application **owner: vturecek**
[Getting Started with Microsoft Azure Service Fabric Stateless Services](../service-fabric-get-started-hello-world-stateless) **owner: vturecek**

Create Windows Fabric Stateful  applications using .NET Distributed Collections (C#) **owner: vturecek**
[]()

#### Create Actor Services

Create Stateless using .NET Actor Framework  (volatile, durable stateful) **owner: Brad**
[Hello World with Actors](../service-fabric-get-started-hello-world-with-actors.md)

Create Stateful using .NET Actor Framework  (volatile, durable stateful) **owner: Brad**
**pending prioritization**
[]()

#### Communicate with services

Create stateless service with Web API
[Echo Service using Web API](../service-fabric-get-started-echo-service-using-web-api.md)

Create stateful service with Web API
[Todo Service using Web API](../service-fabric-get-started-todo-service-with-web-api.md)

Create an ASP.NET v5 Application **owner: Brad**
**skipped for //build due to lack of project-k integration**

#### Fabric Service Programming Models

[Getting Started with FabSrv programming model](../service-fabric-fabsrv-getting-started) **owners: masnider, alanwar, tyadam, sumukhs**

Add Diagnostics code to a service **owner: Kunalds**
=======
- [Choose a Programming Model](service-fabric-choose-framework.md) **owner: seanmck**

- HOLs/tutorials **owners: Haishi, bmerrill**

- [Reliable Service Programming Model Introduction](service-fabric-reliable-services-introduction.md) **owner: masnider**

- [Reliable Service Programming Model Quick Start](service-fabric-reliable-services-quick-start.md) **owner: masnider**

- [Reliable Services Programming Model Advanced Usage](service-fabric-reliable-services-advanced-usage.md) **owner: jesseb**

- Communicate with a Service

- Add Diagnostics code to a service **owner: Kunalds**

- [Secure replication traffic of stateful services](service-fabric-replication-security.md)


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
- [Testability Overview](service-fabric-testability-overview.md) - P1 **owner: vturecek**

- [Run an action](service-fabric-testability-actions.md)  **owner: vturecek**

- [Run a scenario](service-fabric-testability-scenarios.md) - P1 **owner: vturecek**

- Validating and troubleshooting - P1 **owner: anmola**

- Testing during development - P2 **owner: rsinha**

- Testing in test/staging - P2 **owner: rsinha**

- Testing in production - P2 **owner: rsinha**

- Writing a custom test scenario - P3 **owner: vturecek**


### Monitoring and Diagnostics owners: kunalds, toddpf, oanapl
- [How to Monitor and Diagnose Services locally](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) - **owner:kunalds**

### Package, Deploy and Remove an Application

- [Service Model](service-fabric-service-model.md)

- [Application Model](service-fabric-application-model.md)

- [Package an Application](service-fabric-application-model.md) **owner: alexwun**

- [Deploy and Remove an Application](service-fabric-deploy-remove-applications.md) **owner: alexwun**

### Upgrading your Application owner: Mani

- [Service Fabric Application Upgrade](service-fabric-application-upgrade.md) **owner: subramar**

- [Upgrade Parameters](service-fabric-application-upgrade-parameters.md) **owner: subramar**

- [Application Upgrade Flowchart](service-fabric-application-upgrade-flowchart.md) **owner: subramar**

- [Advanced Topics](service-fabric-application-upgrade-advanced.md) **owner: subramar**

- [Troubleshooting Application Upgrade Failures](service-fabric-application-upgrade-troubleshooting.md) **owner: subramar**

- [Application Upgrade Tutorial/Walkthrough](service-fabric-application-upgrade-tutorial.md) **owner: subramar**

### Scale Services and Partitions owner: vturecek

### Add/Remove Services from Applications P2 owner: vturecek

### Remove an Application owner: vturecek
Remove an Application in Visual Studio **owner: vturecek**

Remove an Application **owner: vturecek**

Remove a Service and Application (PowerShell) **owner: vturecek**

## Reference
### Managed Reference
### PowerShell Reference
### REST Reference

## Further Reading- Deep conceptual content, link back to Work with... topics
### Monitoring and Diagnostics
Introduction to monitoring and diagnostics in Service Fabric- overview, crash dump, perf counters, AppInsights, OpInsights, WAD. **owner: kunalds**

### Application Lifecycle
[Application Lifecycle](service-fabric-application-lifecycle.md) **owner: matt**

### FabricExplorer
[Visualizing your cluster using Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

### Health
[Azure Service Fabric Health](service-fabric-health-introduction.md) **owner: oanapl**

[View Azure Service Fabric Entities Aggregated Health](service-fabric-view-entities-aggregated-health.md)
**owner: oanapl**

[Understand and troubleshoot with System health reports](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
**owner: oanapl**

[Report Health on Azure Service Fabric entities](service-fabric-report-health.md)
**owner: oanapl**

### Programming Models Owners: masnider, claudioc, vipulm, richhas
#### Actors
- [Introduction to the Service Fabric Actor Model](service-fabric-fabact-introduction.md) **owner: claudioc**

- [Actor lifecycle and Garbage Collection](service-fabric-fabact-lifecycle.md) **owner: claudioc**

- [Actor Model Design Patterns](service-fabric-fabact-patterns-introduction.md)  **owner: claudioc**

- [Pattern: Smart Cache](winfab-fabact-pattern-smartcache.md) **owner: claudioc**

- [Pattern: Distributed Networks and Graphs](service-fabric-fabact-pattern-distributed-networks-and-graphs.md) **owner: claudioc**

- [Pattern: Resource Governance](service-fabric-fabact-pattern-resource-governance.md) **owner: claudioc**

- [Pattern: Stateful Service Composition](service-fabric-fabact-pattern-stateful-service-composition.md) **owner: claudioc**

- [Pattern: Internet of Things](service-fabric-fabact-pattern-internet-of-things.md) **owner: claudioc**

- [Pattern: Distributed Computation](service-fabric-fabact-pattern-distributed-computation.md) **owner: claudioc**

- [Some Anti-patterns](service-fabric-fabact-anti-patterns.md) **owner: claudioc**

- [Actor Events](service-fabric-fabact-events.md) **owner: myamanbh**

- [Actor Reentrancy](service-fabric-fabact-reentrancy.md) **owner: myamanbh**

- [Actor Timers](service-fabric-fabact-timers-reminders.md) **owner: myamanbh**

#### Reliable Services
- [Programming Model Overview](service-fabric-fabsrv-service-overview.md)  **owners: masnider, richhas**

- [Architecture](service-fabric-fabsrv-platform-architecture.md) **owner: alanwar**

- [Reliable Collections](service-fabric-fabsrv-reliable-collections.md) **owners: mcoskun, tyadam**

- [API Discussion](service-fabric-fabsrv-service-api-description.md) **owners: tyadam, mcoskun**

- [Operations](service-fabric-fabsrv-managing-your-services.md) **owner: jesseb, alanwar**

## P2 conceptual topics
### PaaS v2 and Cloud Applications
### Applications, Services, Partitions, Replicas, and Replica Sets
### Cluster
### Service State
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

### Glossary
