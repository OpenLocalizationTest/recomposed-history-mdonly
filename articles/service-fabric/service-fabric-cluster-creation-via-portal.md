<properties
   pageTitle="Setting up a Service Fabric Cluster from the Azure Portal"
   description="Setting up a Service Fabric Cluster from the Azure Portal."
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
   ms.date="08/26/2015"
   ms.author="chackdan"/>

# Setting up a Service Fabric Cluster from the Azure Portal

This page helps you with setting up of a Service Fabric Cluster. It is assumed that your subscription has enough cores to deploy the IaaS VMs that will make up this cluster

**Prerequisite**
- If you want to set up a secure cluster, make sure to have uploaded an X509 certificate to your key vault. you will need the Source Vault URL, Certificate URL and the Certificate thumbprint 
- instructions on how to is here (link to key vault documentation)

**Instructions to set up a service fabric cluster using the portal**

1. Log on to the Azure Portal [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

2. Click on the **+** to add a new Resource Template. Search for our Template under Everything - **Service Fabric Cluster**
(you can navigate to **Everything** by clicking into any of the top level categories - say Compute > scroll down to Azure Market Place > and  click it)  
    ![SearchforServiceFabricClusterTemplate][SearchforServiceFabricClusterTemplate]
 
3. Select "Service Fabric Cluster" from the list
4. Navigate to the Service Fabric Cluster Blade and click on **Create**
5. Create a **new Resource Group (RG)** - make it the same as the Cluster name - it is better for finding them later.


  Notes 

- Sometimes the RG blade takes time to load, but be patient, and create the new resource group.
- Wait for the new RG blade to close to the "Service Fabric Cluster" blade, trying to navigate away will result in RG not getting created and/or some unforeseen issues later.

![CreateRG][CreateRG] 

6. Make sure to select the a **subscription** (if you have multiple, or a default was not selected by the system).
7. Select a **location** from the dropdown (if you want to create it another location, else it will default to West US)
8. Configure your **Node Type**
	- Select the VM size /Pricing tier you need. (default is D4, if you are just going to use this for testing your application, it is fine to select D2 or any smaller size VM )
	- Choose the number of VMs (if you are unsure, just select 5 to start with)
	- Choose the VM remote desktop User Name and Password
	
![CreateNodeType][CreateNodeType]

8. **Application ports**- Let us now add ports you want to open for your applications. This is *not an optional step*.  Go to each of your applications, open up the service manifests and take note of all the  "input" endpoints your applications needs to communicate to the outside world… 
9. Add all the ports , comma separated  in the "Application input endpoints" field. The  TCP client connection end point - 19000 by default, so you do not need to specify them.

- for example my application needs port "83" to be open. You will find this in the servicemanifest.xml in your  application package (there could be more than one servicemanifest.xml).
	
Most of the sample application use port 80 and 8081, so add them if you plan to deploy samples to this cluster.

![Ports][Ports] 

![SFConfigurations][SFConfigurations]


9. **Securing your cluster** (*optional , but recommended step*)
	
-  Navigate to the Security Configurations blade from the Cluster Configurations blade
-  Default security for the cluster is "None".
-  It is strongly recommended that you secure your cluster. Use a x509 cert for this. you need to upload your cert to key vault.
**Note** -  click on the "Balloon help Icon" beside the fields for the format in which you will need to enter the values.
![SecurityConfigs][SecurityConfigs]

10. After you have completed cluster configuration, the create button will be enabled and you can start the cluster creation process.
11. You can see the progress in the NOTIFICATIONS (left hand side of the screen).

![Notifications]:[Notifications]

12. Once your deployment is complete, Go to **Browse** and click on the resource - **Service  Fabric Clusters**
Locate your cluster and click on it.. 

![BrowseCluster][BrowseCluster]

13. You can now see the details of your cluster on the Cluster Dashboard.. You can now deploy to the cluster using the Cluster Public IP Address. (hovering over that value will bring up a clipboard, which you can click to copy it).
![ClusterDashboard][ClusterDashboard]

15. **Deploying Applications** - Connect and deploy your applications now..
- From a machine that has the service fabric SDK installed or have the service fabric client package installed, run the following
- Open up PS/PS ISE and connect to the Azure Service Fabric Cluster	- 

![SecureConnetion][SecureConnetion]


 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Next steps
- [Managing your Service Fabric applications in Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnetion]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png
