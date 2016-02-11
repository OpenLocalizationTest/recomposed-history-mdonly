<properties
   pageTitle="Create a on-premises or poly-cloud Azure Service Fabric cluster with "AzureServiceFabric-DeployAnywhere" package | Microsoft Azure"
   description="Learn how to create an Azure Service Fabric cluster on any machines - Physical or Virtual, be it on-premises or other clouds by using the AzureServiceFabric Deploy Anywhere package."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/08/2016"
   ms.author="chackdan"/>


#Create an Azure Service Fabric cluster on-premises or on ther clouds
 
Azure Service Fabric allows the creation of Service Fabric clusters on any Virtual Machines or computers running Windows Server (Linux support coming later). This means you can deploy and run Service Fabric applications in any environment where you have a set of Windows Server or Linux computers that are interconnected, be it on-premises or with any cloud provider. Service Fabric provides a setup package for you to create such Service Fabric clusters.


This article walks you through the steps for creating a cluster using "Deploy Anywhere" package on-premises, though it can be easily adapted for any other environment such as other clouds.


## Microsoft Azure Service Fabric "deploy anywhere" Package

The "Deploy anywhere" package for Windows Server 2012 R2 or Windows Server 2012 deployments is MicrosoftAzureServiceFabricWindowsServer&lt;version&gt;.zip

In the download package you will find the following files.

|**File name**|**Short description**|
|-----------------------|--------------------------|
|AzureServiceFabricDeployAnywhere.cab|The cab file that contains the binaries that will be deployed to each machine in the cluster.|
|ClusterConfig.JSON|Cluster Configuration file that contains all the settings for the cluster including the information for each machine that is part of the cluster.|
|EULA.txt|The license terms for the use of Microsoft Azure Service Fabric "deploy anywhere" package. [Click here](http://go.microsoft.com/fwlink/?LinkID=733084) if you would like to download a copy of the EULA now.|
|Readme.txt|Link to the release notes and Basic installation instructions.It is a small subset of the instructions you will find on this page.|
|CreateServiceFabricCluster.ps1|PowerShell script that creates the cluster as per the settings in the ClusterConfig.JSON.|
|RemoveServiceFabricCluster.ps1|PowerShell script for cluster removal as per the settings in the ClusterConfig.JSON.|


## Planning and Preparation for cluster deployment
Review and act on the following steps prior to moving on to the next section - Deployment of your cluster.

####1. Plan your cluster Infrastructure 
You are about to deploy a service fabric cluster to the machines you own, so you get to decide on what kinds of failures you want the cluster to survive, say for example - do you need separate power lines, internet connections etc that feed these machines. These are in addition to thinking through, the physical security like the physical location of these machines, who needs access to them etc.  Once you make these decisions, you logically map the machines to the various Fault Domains (scroll down for definition). <br> The infrastructure planning for production clusters will be far more involved than the test only clusters. 

####2. Prepare the machines to meet the pre-requisites 

Pre-requisites for each machine that you want to be a part of the cluster:

- Minimum of 2 GB memory is recommended
- Network Connectivity – Make sure that the machines are on a secure network/or networks
- Windows Server 2012 R2 or Windows Server 2012 (you need to have KB2858668 installed for this).
- .NET Framework 4.5.1 or higher, full install
- Windows PowerShell 3.0
- Visual C++ 2012 (VC++ 11.0) Redistributable Package
- The cluster administrator deploying and configuring the cluster must have administrator privileges on each of the computers.

####3. Determine the Initial Cluster Size
Each node consists of a full Service Fabric stack and is an individual member of the Service Fabric cluster. In a typical Service Fabric deployment there is one node per OS instance (physical or virtual). The cluster size is determined by your business needs; however, you must have a minimum cluster size of three nodes (machines/VMs).
###### Note  
For development purposes, you can have more than one node on a given machine. In a production environment, Service Fabric supports only one node per physical or virtual machine.

#### 4. Determine the number of Fault Domains and Upgrade Domains
A **fault domain (FD)** is a physical unit of failure and is directly related to the physical infrastructure in the data centers. A fault domain consists of hardware components (computers, switches, and more) that share a single point of failure. Although there is no 1:1 mapping between fault domains and racks, loosely speaking, each rack can be considered a fault domain. When considering the nodes in your cluster, it is strongly recommended that the nodes be distributed amongst at least three fault domains. 

When you specify FDs in the ClusterConfig.JSON, You get to choose the name of the FD. Service fabric supports hierarchical FDs, so you can reflect you infra topology in them for example, the following are allowed

"faultDomain": "fd:/Room1/Rack1/Machine1"

"faultDomain": "fd:/FD1"

"faultDomain": "fd:/Room1/Rack1/PDU1/M1"


An **upgrade domain (UD)** is a logical unit of nodes. During a Service Fabric orchestrated upgrade (Application upgrade or Fabric/Cluster upgrades, all nodes in a UD are taken down to perform the upgrade while nodes in other UDs remain available to serve requests. The firmware upgrades you do to your machines will not honor UDs, so you must do them one machine at a time.

The simplest way to think about these concepts is to consider FDs as the unit of unplanned failure, and UDs as the unit of planned maintenance. 

When you specify UDs in the ClusterConfig.JSON, You get to choose the name of the UD. For example, the following are all allowed

"upgradeDomain": "UD0"

"upgradeDomain": "UD1A"

"upgradeDomain": "DomainRed"

"upgradeDomain": "Blue"

#### 5. Download the "Deploy Anywhere" package
Download the latest [MicrosoftAzureServiceFabricWindowsServer&lt;version&gt;.zip](http://go.microsoft.com/fwlink/?LinkId=730690) and unzip the downloaded package to your deployment machine or on one of the machines that will be the part of your cluster.

## Deploy your Cluster

After you have gone steps outlined in the planning and preparation section above, you are now ready to deploy your cluster. 

####1. Modify Cluster Configuration
open the ClusterConfig.JSON from the package you downloaded. you can use any editor of your choice. Modify the following settings. 

|**Configuration Setting**|**Description**|
|-----------------------|--------------------------|
|NodeTypes|Node types allow you to separate your cluster nodes into various groups. A cluster must have at least one NodeType. All nodes in a group have the following common characteristics. <br> *Name*  - This is the Node Type name. <br>*EndPoints* - These are various named end points (Ports) that are associated with this Node Type. You can use any port number that you wish, as long as they do not conflict with anything else in this manifest and are not already in use by any other program on the machine/VM <br> *PlacementProperties* - These describe properties for this node type that you will then use as placement constraints for system services or your services. These properties are user defined key/value pairs that provide extra metadata for a given node. Examples of node properties would be whether or not the node has a hard drive or graphics card, the number of spindles in it hard drive, cores, and other physical properties. <br> *Capacities* - Node capacities define the name and amount of a particular resource that a particular node has available for consumption. For example, a node may define that it has capacity for a metric called “MemoryInMb” and that it has 2048 available by default. These capacities are used at runtime to ensure that services which require particular amounts of resources are placed on nodes with those resources remaining available.|
|Nodes|The details for each of the nodes that will be part of the cluster (node type, node name, IP address, Fault Domain and Upgrade Domain of the node). The machines you want the cluster to be created on need to be listed here with their IP address. <br> If you use the same IP addresses for all the nodes, then a scale minimized or one-box cluster will be created, which you can use for test purposes. Scale minimized clusters should not be used for deploying production workloads..|

####2. Run the Create Cluster script
Once you have modified the cluster configuration in the JSON doc and added all the node information to it, run the cluster creation powershell script from the package folder and pass in the path to the configuration file and the location of the package root to it. 

This script can be run on any machine that has admin access to all the machines that are listed as nodes in the cluster configuration file. The machine that this script is run on, may or may not be part of the cluster.

```
C:\ServiceFabricDeployAnywherePackage> .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath C:\ServiceFabricDeployAnywherePackage\ClusterConfig.JSON -MicrosoftServiceFabricCabFilePath C:\ServiceFabricDeployAnywherePackage\MicrosoftAzureServiceFabric.cab
```


## Next steps

- [Cluster security](service-fabric-cluster-security.md)
- [ Service Fabric SDK and getting started](service-fabric-get-started.md)
- [Managing your Service Fabric applications in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Service Fabric Health model introduction](service-fabric-health-introduction.md)
- [Application Security and Runas](service-fabric-application-runas-security.md)
- [Overview of the "Deploy anywhere" feature and a comparison with Azure-managed clusters](service-fabric-deploy-anywhere.md)
