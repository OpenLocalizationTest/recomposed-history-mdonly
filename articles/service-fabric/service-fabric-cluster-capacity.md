<properties
   pageTitle="Planning the Service Fabric cluster capacity | Microsoft Azure"
   description="Service Fabric cluster capacity planning considerations."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/26/2016"
   ms.author="chackdan"/>


# Service Fabric cluster capacity planning considerations

For any production deployment, capacity planning is an important step. Here are some of the items that you have to consider as a part of that process.
  
1. The number of Node Types your Cluster needs to start out with
1. The properties of each of the Node Types (size, primary, internet facing, number of VMs etc)
1. The Initial Size of the cluster or the number of VMs you need
1. The reliability and durability characteristics of the cluster 

Let us briefly review each of these.
 
## The number of Node Types your Cluster needs to start out with

You need to figure out what this cluster is going to be used for and what kinds of applications you are planning to deploy into this cluster.If you are not clear on the purpose of the cluster you are going to deploy, then you are most likely not ready to enter the capacity planning process. Once you are clear on what this cluster is going to be used  


Establish the number of node types your Cluster needs to start out with –  Each Node type is mapped to a Virtual Machine Scale Set. Each node type can then be scaled up or down independently, have different sets of ports open, and can have different capacity metrics. So the decision of the number of node types essentially comes down to the following considerations.


1. Does your application have multiple services, and does any of those need to be public or internet facing? Typically, an application contains a front-end gateway service that receive input from a client, and one or more back-end services that just talk to the front-end services. So in this case, you end up having two node Types.

2. The application that you want to deploy contains a front-end service and a back-end service. You want to put the front-end service on smaller VMs (VM sizes like D2), and they have ports open to the Internet, but you want to put the back-end service, which is computation intensive, on larger VMs (with VM sizes like D4, D6, D15, and so on) that are not Internet facing. Although you can decide to put all the services on one node type, we recommended that you place them in a cluster with two node types.  this allows for each node type to have distinct properties like Internet connectivity, VM size, and the number of VMs that can be scaled independently.  

2. Since you cannot predict future, go with facts you know of and decide on the number of node types that your applications need to start with. you can always add or remove node types at a later time, however a Service fabric cluster will always need to have one Node Type.
 
## The properties of each of the Node Types 

The **Node Type** node type can be seen as equivalent to roles in cloud services. Node types define the VM sizes, the number of VMs, and their properties. Every node type that is defined in a Service Fabric cluster is setup as a separate Virtual Machine Scale Set. VM Scale Sets are an Azure Compute resource you can use to deploy and manage a collection of virtual machines as a set. Being defined as distinct VM Scale sets, each node type can then be scaled up or down independently, have different sets of ports open, and can have different capacity metrics.

Your cluster can have more than one node type, but the primary node type (the first one that you define on the portal) must have at least five VMs. If you are creating the cluster using an ARM template, then you will find a "is Primary" attribute under the node type definition. Primary node type is the node type were service fabric system services are placed.  


**Primary Node Type** For a cluster with multiple node types, you will need to choose one of them to be primary. here are the characteristics of a primary node type.

1. The minimum size of VMs for the primary node type is driven by the durability tier you choose. The default for the durability tier is Bronze. Scroll down for Details on what the durability tier is and the values it can take.  

2. The minimum number of VMs for the primary node type is driven by the reliability tier you choose. The default for the reliability tier is Silver. Scroll down for Details on what the reliability tier is and the values it can take. 

4.  The service fabric system services (for example -Cluster Manager Service, Image Store service etc ) are placed on the primary node type and so the reliability and durability of the cluster is determined by the reliability trier value and durability tier value you select for the primary node type.

![Screen shot of a cluster that has two Node Types ][SystemServices]


**other Node Types** For a cluster with multiple node types, there will be one primary node type and the rest of them are non-primary. Here are the characteristics of a non-primary node type

1. The minimum size of VMs for this node type is driven by the durability tier you choose. The default for the durability tier is Bronze. Scroll down for Details on what the durability tier is and the values it can take.  

2. The minimum number of VMs for this node type can be one. However you should choose this number based on the number of replicas of the application/services that you would like to run in this node type. The number of VMS in a node type can be increased after you have deployed the cluster. 


## Durability Tier in a Node Type

Durability Tier is used to indicate to the system, the privileges that your VMs have with the underlying  Azure infrastructure. In the primary node type, this privilege allows service fabric to pause any VM level infrastructure request like VM reboot, VM re-image, VM migration etc that impact the quorum requirements for the system services and your stateful services running in it. In the non-primary node types, this privilege allows service fabric to pause any VM level infrastructure request like VM reboot, VM re-image, VM migration etc that impact the quorum requirements for your stateful services running in it.

This privilege is expressed in the following values

1. Gold - The infrastructure Jobs can be paused for a duration of 2 hours per UD

2. Silver - The infrastructure Jobs can be paused for a duration of 30 mins per UD
  
3. Bronze - No privileges. 


## Reliability Tier in a Node Type

blah blah:



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Next steps
- [Service Fabric cluster security](service-fabric-cluster-security.md)
- [Service Fabric health model introduction](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
