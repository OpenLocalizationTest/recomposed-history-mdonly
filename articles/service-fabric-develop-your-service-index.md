<properties
   pageTitle="Develop a Service Fabric service"
   description="Conceptual information and tutorials that help you understand how to develop a Service Fabric service using the Reliable Actor or Reliable Services programming models."
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
   ms.date="04/21/2015"
   ms.author="ryanwi"/>

# Develop a Service Fabric service
This page has links to overview and conceptual articles and tutorial to help you learn to develop a Service Fabric service. Service Fabric offers two high-level programming models for building services: the reliable actor APIs and the reliable services APIs. While both are built on the same Service Fabric core, they make different trade-offs between simplicity and flexibility in terms of concurrency, partitioning, and communication. It is useful to understand both models so that you can choose the appropriate framework for a particular service within your application.

- [Choose a Programming Model](service-fabric-choose-framework.md)
- [Introduction to the Service Fabric Actor Model](service-fabric-fabact-introduction.md)
- [Reliable Service Programming Model Introduction](service-fabric-reliable-services-introduction.md)

## Reliable Actor programming model
 Reliable Actors provide an asynchronous, single-threaded actor model. The actors represent the unit of state and computation that are distributed throughout the cluster to achieve high scalability. Reliable Actor model leverages the distributed store provided by underlying Service Fabric platform to provide highly available and consistent state management for the application developers.  To learn more, read:

- [Actor lifecycle and Garbage Collection](service-fabric-fabact-lifecycle.md)
- [Actor Model Design Patterns](service-fabric-fabact-patterns-introduction.md)  
- [Pattern: Smart Cache](winfab-fabact-pattern-smartcache.md)
- [Pattern: Distributed Networks and Graphs](service-fabric-fabact-pattern-distributed-networks-and-graphs.md)
- [Pattern: Resource Governance](service-fabric-fabact-pattern-resource-governance.md)
- [Pattern: Stateful Service Composition](service-fabric-fabact-pattern-stateful-service-composition.md)
- [Pattern: Internet of Things](service-fabric-fabact-pattern-internet-of-things.md)
- [Pattern: Distributed Computation](service-fabric-fabact-pattern-distributed-computation.md)
- [Some Anti-patterns](service-fabric-fabact-anti-patterns.md)
- [Actor Events](service-fabric-fabact-events.md)
- [Actor Reentrancy](service-fabric-fabact-reentrancy.md)
- [Actor Timers](service-fabric-fabact-timers-reminders.md)

## Reliable Service programming model
Reliable Services gives you a simple, powerful, top-level programming model to help you express what is important to your application. To learn more, read:

- [Reliable Services Quick Start](service-fabric-reliable-services-quick-start.md)
- [Getting Started with Microsoft Azure Service Fabric Web API services with OWIN self-host (VS 2015 RC)](service-fabric-fabsrv-communication-webapi)
- [Reliable Services Programming Model Advanced Usage](service-fabric-reliable-services-advanced-usage.md)
- [Programming Model Overview](service-fabric-fabsrv-service-overview.md)  
- [Architecture](service-fabric-fabsrv-platform-architecture.md)
- [Reliable Collections](service-fabric-fabsrv-reliable-collections.md)
- [API Discussion](service-fabric-fabsrv-service-api-description.md)
- [Managing your Service Fabric applications in Visual Studio](service-fabric-manage-application-in-visual-studio.md) 
