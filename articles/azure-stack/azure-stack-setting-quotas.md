﻿<properties
	pageTitle="Quotas in Azure Stack | Microsoft Azure"
	description="Administrators set quotas to restrict the maximum amount of resources that tenants have access to."
	services="azure-stack"
	documentationCenter=""
	authors="mattmcg"
	manager="byronr"
	editor=""/>

<tags
	ms.service="azure-stack"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/21/2016"
	ms.author="mattmcg"/>



#Setting quotas in Azure Stack

Quotas define the limits of resources that a tenant subscription
can provision or consume. For example, a quota may allow a tenant to
create up to five VMs. To add a service to a plan, the
administrator must configure the quota settings for that service.

Quotas are configurable per service and per location, allowing the administrator to provide granular control over the resource
consumption. An administrator can create one or more quota
resources and associate them with plans, allowing them to provide
differentiated offerings for their services. Quotas for a given service can be created 
from the Resource Provider administration blade for that service. 

A tenant that subscribes to an offer containing multiple
plans can use all resources available in each plan.

##To create an IaaS quota:

1.  In an internet browser, navigate to
    [https://portal.azurestack.local](https://portal.azurestack.local/).
    
    Sign in to the Azure Stack portal as an administrator (using the credentials you provided during deployment). 

2.  Click **New** then **Quota**.

3.  Select the first service for which you want to create a quota. For an IaaS quota, you need to follow these steps for each of Compute, Network, and Storage.
In this example, we first create a quota for the Compute Service, so select the **Microsoft.Compute.Admin** namespace from the **Namespace** list. 

    > ![Creating a new Compute quota](/articles/azure-stack/media/azure-stack-setting-quota/NewComputeQuota.PNG)

4.  Choose the location where the quota is defined (for example, 'local').

5.  On the **Quota Settings** item, you see it says **Set the
    Capacity of Quota**. Click this item to configure the quota settings.

6.  On the Set Quotas blade, you see all the Compute resources for which
    you can configure limits. Each type has a default
    value associated with it. You can change these values if you choose,
    or click the **Ok** button at the bottom of the blade to accept
    the defaults.

    > ![Setting a Compute quota](/articles/azure-stack/media/azure-stack-setting-quota/SetQuotasBladeCompute.PNG)

7.  Once you have configured the values and clicked **Ok**, the **Quota
    Settings** item now appears as **Configured**. Click **Ok** to
    create the **Quota** resource.

    You should see a notification indicating that the quota resource is
    being created.

8.   Once the Quota set has been successfully created, you receive a second notification. The Compute service quota is now ready to be associated with a
    Plan. Repeat these steps with the Network and Storage services and you are ready to create an IaaS plan!

    >   ![Notification upon quota creation success](/articles/azure-stack/media/azure-stack-setting-quota/QuotaSuccess.png)

##Compute Quota Types

|**Type**                    |**Default Value**| **Description**|
|--------------------------- | ------------------------------------|------------------------------------------------------------------|
|Max Number of Virtual Machines   |50|The maximum number of Virtual Machines that a subscription can create in this location. |
|Max Number of Virtual Machine Cores              |100|The maximum number of cores that a subscription can create in this location (for example, an A3 VM has 4 cores).|
|Max Amount of Virtual Machine Memory (GB)         |150|The maximum amount of RAM that can be provisioned in megabytes (for example, an A1 VM consumes 1.75 GB in RAM)|

> [AZURE.NOTE] Compute quotas are not enforced in this technical preview.

##Storage Quota Types

|**Item**                           |**Default Value**   |**Description**|
|---------------------------------- |------------------- |-----------------------------------------------------------|
|Maximum Capacity (GB)              |500                 |Total storage capacity allowed to be consumed by a subscription in this location.|
|Total number of storage accounts   |20                  |The maximum number of storage accounts that a subscription can create in this location.|

##Network Quota Types

|**Item**                                                   |**Default Value**   |**Description**|
|----------------------------------------------------------| ------------------- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Max Public IPs                         |50                  |The maximum number of Public IPs that a subscription can create in this location. |
| Max Virtual Networks                   |50                  |The maximum number of virtual networks that a subscription can create in this location. |
| Max Virtual Network Gateways           |1                   |The maximum number of virtual network gateways (VPN Gateways) that a subscription can create in this location. |
| Max Network Connections                |2                   |The maximum number of network connections (point-to-point or Site-to-Site) that a subscription can create in across all Virtual Network Gateways in this location. |
| Max Load Balancers                     |50                  |The maximum number of load balancers that a subscription can create in this location. |
| Max NICs                               |100                 |The maximum number of network interfaces that a subscription can create in this location. |
| Max Network Security Groups            |50                  |The maximum number of Network Security Groups that a subscription can create in this location. |

