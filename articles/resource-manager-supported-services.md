<properties
   pageTitle="Resource Manager supported services and supported regions | Microsoft Azure"
   description="Describes the resource providers that support Resource Manager and the regions that can host the resources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/21/2015"
   ms.author="tomfitz"/>

# Resource Manager support for services, regions, and API versions

Azure Resource Manager provides a new way for you to deploy and manage the services that make up your applications. 
Most, but not all, services support Resource Manager, and some services support Resource Manager only partially. Microsoft will enable Resource Manager for every service that is important for future solutions, but until the 
support is consistent, you need to know the current status for each service. This topic provides a list of supported resource providers for Azure Resource Manager.

When deploying your resources, you also need to know which regions support those resources and which API versions are available for the resources. The section [Supported regions](#supported-regions) shows you how to find out which regions will work for your subscription and resources. The section [Supported API versions](#supported-api-versions) shows you how to determine which API versions you can use.

The following tables list which services support deployment and management through Resource Manager and which do not. The column titled **Move Resources** refers to whether resources of this type can be moved to both a 
new resource group and a new subscription. The column titled **Preview Portal** indicates whether you can create the service through the preview portal.


## Compute

| Service | Resource Manager Enabled | Preview Portal | Move Resources | REST API | Schema |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) | Yes | Yes, many options | No       | [Create VM](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| [Batch](https://azure.microsoft.com/documentation/services/batch/)   | Yes     | [Create (classic only)](https://portal.azure.com/#create/Microsoft.BatchAccount) |               | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) |        |
| Dynamics Lifecycle Services | Yes | No |    |      |        |
| [Virtual Machines (classic)]((https://azure.microsoft.com/documentation/services/virtual-machines/)) | Limited | Yes, many options | Partial (see below) | - | - |
| [Remote App](https://azure.microsoft.com/documentation/services/remoteapp/) | No   | No | -              | -        | -      |
| [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric/) | No | No | -           | -        | -      |

Virtual Machines (classic) refers to resources that were deployed through the classic deployment model, instead of through the Resource Manager deployment model. In general, these resources do not support Resource Manager operations, but there 
are some operations that have been enabled. For more information about these deployment models, see [Understanding Resource Manager deployment and classic deployment](resource-manager-deployment-model.md). 

Virtual Machines (classic) resources can be moved to new resource group, but not a new subscription.

## Web & Mobile

| Service | Resource Manager Enabled | Preview Portal | Move Resources | REST API | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| [API Management](https://azure.microsoft.com/documentation/services/api-management/) | Yes | No | Yes  | [Create API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) |        |
| [API Apps](https://azure.microsoft.com/documentation/services/app-service/api/) | Yes | [Create](https://portal.azure.com/#create/microsoft_com.ApiApp.0.2.0-preview) |   |   | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| [Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/) | Yes | [Create](https://portal.azure.com/#create/Microsoft.WebSite)  | Yes, with limitations (see below) |          | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| [Notification Hubs](https://azure.microsoft.com/documentation/services/notification-hubs/) | Yes | [Create](https://portal.azure.com/#create/Microsoft.NotificationHub) |         | [Create Notification Hub](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| [Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) | Yes  | [Create](https://portal.azure.com/#create/Microsoft.EmptyWorkflow.0.2.0-preview) |       |          |        |
| [Mobile Engagements](https://azure.microsoft.com/documentation/articles/mobile-engagement-overview/) | Yes | No |  Yes  |          |        |

When working with web apps, you cannot move only an App Service plan. To move web apps, your options are:

- Move all of the resources from one resource group to a different resource group, if the destination resource group does not already have Microsoft.Web resources.
- Move the web apps to a different resource group, but keep the App Service plan in the original resource group.


## Data & Storage

| Service | Resource Manager Enabled | Preview Portal | Move Resources | REST API | Schema |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| [DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/) | Yes  | [Create](https://portal.azure.com/#create/Microsoft.DocumentDB) | Yes  | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) |   |
| [Storage](https://azure.microsoft.com/documentation/services/storage/) | Yes     | [Create](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) |         | [Create Storage](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Storage.json) |
| [Redis Cache](https://azure.microsoft.com/documentation/services/redis-cache/) | Yes | [Create](https://portal.azure.com/#create/Microsoft.Cache.1.0.4) | Yes |   | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) | Yes | [Create](https://portal.azure.com/#create/Microsoft.SQLDatabase.0.5.7-preview) | Yes  | [Create Database](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| [Search](https://azure.microsoft.com/documentation/services/search/) | Yes  | Yes | Yes   | [Search REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) |  |
| [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) | Yes | [Create](https://portal.azure.com/#create/Microsoft.SQLDataWarehouse.0.1.12-preview) |   |   |      |
| [StorSimple](https://azure.microsoft.com/documentation/services/storsimple/) | No   | No | -  | -        | -       |
| Managed cache | No | No | -             | -        | -       |

## Analytics

| Service | Resource Manager Enabled | Preview Portal | Move Resources | REST API | Schema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| [Event Hub](https://azure.microsoft.com/documentation/services/event-hubs/) | Yes   | No |         | [Create Event Hub](https://msdn.microsoft.com/library/azure/dn790676.aspx) |        |
| [Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/) | Yes | [Create](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) |        |          |        |
| [HDInsights](https://azure.microsoft.com/documentation/services/hdinsight/) | Yes  | [Create](https://portal.azure.com/#create/Microsoft.HDInsightCluster) |            |          |        |
| [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) | Yes | [Create](https://portal.azure.com/#create/Microsoft.DataFactory) | Yes | [Create Data Factory](https://msdn.microsoft.com/library/azure/dn906717.aspx) |    |
| [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) | No | No | -          | -        | -      |
| [Data Catalog](https://azure.microsoft.com/documentation/services/data-catalog/) | No | No |  -             | -        | -       |

## Networking

| Service | Resource Manager Enabled | Preview Portal | Move Resources | REST API | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| [Application Gateway](https://azure.microsoft.com/documentation/services/application-gateway/) | Yes |  |      |          |        |
| [DNS](https://azure.microsoft.com/documentation/services/dns/)     | Yes     |  |               | [Create DNS Zone](https://msdn.microsoft.com/library/azure/mt130622.aspx)         | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| [Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/) | Yes |    |          | [Create Load Balancer](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| [Virtual Networks](https://azure.microsoft.com/documentation/services/virtual-network/) | Yes | [Create](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) | No        | [Create Virtual Network](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| [Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/) | Yes | No |           | [Create Traffic Manager profile](https://msdn.microsoft.com/library/azure/mt163581.aspx) |        |
| [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) | Yes | No | No             | [ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx)  |       |

## Media & CDN

| Service | Resource Manager Enabled | Preview Portal | Move Resources | REST API | Schema |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| [Media Service](https://azure.microsoft.com/documentation/services/media-services/) | No | No |  |  |  |
| [CDN](https://azure.microsoft.com/documentation/services/cdn/) | No | No |  |  |  |

## Hybrid Integration

| Service | Resource Manager Enabled | Preview Portal | Move Resources | REST API | Schema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| [BizTalk Services](https://azure.microsoft.com/documentation/services/biztalk-services/) | Yes | No |        |          | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| [Service Bus](https://azure.microsoft.com/documentation/services/service-bus/) | Yes | No |     | [Service Bus REST](https://msdn.microsoft.com/library/azure/hh780717.aspx) |        |
| [Backup](https://azure.microsoft.com/documentation/services/backup/) | No | No | -              | -        | -       |
| [Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery/) | No | No | -             | -        | -       |

## Identity & Access Management 

| Service | Resource Manager Enabled | Preview Portal | Move Resources | REST API | Schema |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) | No | No | -  | - | - |
| [Azure Actice Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/) | No | No | - | - | - |
| [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/) | No | No | - | - | - |

## Developer Services 

| Service | Resource Manager Enabled | Preview Portal | Move Resources | REST API | Schema |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) | Yes | [Create](https://portal.azure.com/#create/Microsoft.AppInsights.0.2.3-preview) |       |          | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| [Bing Maps](https://msdn.microsoft.com/library/dd877180.aspx) | Yes   | [Create](https://portal.azure.com/#create/bingmaps.mapapis.1.0.4) |         |          |        |
| [Visual Studio account](https://www.visualstudio.com/get-started/overview-of-get-started-tasks-vs) | Yes |  |      |          | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## Management 

| Service | Resource Manager Enabled | Preview Portal | Move Resources | REST API | Schema |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| [Automation](https://azure.microsoft.com/documentation/services/automation/) | Yes  | [Create](https://portal.azure.com/#create/Microsoft.AutomationAccount.1.0.2-preview) |       |          |        |
| [Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) | Yes    | No | Yes            | [Key Vault REST](https://msdn.microsoft.com/library/azure/dn903609.aspx) |        |
| [Scheduler](https://azure.microsoft.com/documentation/services/scheduler/) | Yes   | No |        |          | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| [Operational Insights](https://azure.microsoft.com/documentation/services/operational-insights/) | Yes | No | Yes   |          |        |
| [IoTHubs](https://azure.microsoft.com/documentation/services/iot-hub/) | Yes     | [Create](https://portal.azure.com/#create/Microsoft.IotHub) |               |          |        |


## Supported regions

When deploying resources, you typically need to specify a region for the resources. Resource Manager is supported in all regions, but the resources you deploy might not be supported in all regions. In addition, there 
may be limitations on your subscription which prevent you from using some regions that support the resource. These limitations may be related to tax issues for your home country, or the result of a policy placed 
by your subscription administrator to use only certain regions. 

Before deploying your resources, check the supported regions for your resource type by running one of the following commands.

### REST API

To discover which regions are available for a particular resource type in your subscription, use the [List all resource providers](https://msdn.microsoft.com/library/azure/dn790524.aspx) operation. 

### PowerShell

The following example shows how to get the supported regions for web sites using Azure PowerShell 1.0 Preview. For more information about the 1.0 Preview release, see [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
The output will be similar to:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

For Azure PowerShell 0.9.8, use the following command:

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

### Azure CLI

The following example returns all of the supported locations for each resource type.

    azure location list

You can also filter the location results with a tool like **jq**. To learn about tools like jq, see [Useful tools to interact with Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Which returns:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## Supported API versions

When you deploy a template, you must specify an API version to use for creating each resource. The API version corresponds to a version of REST API operations that are released by the resource provider. 
As a resource provider enables new features, it will release a new version of the REST API. Therefore, the version of the API you specify in your template affects which properties you can specify in the 
template. In general, you will want to select the most recent API version when creating new templates. For existing templates, you can decide whether you want to continue using an earlier API version, or update your template for the latest version to take advantage of new features.

### REST API

To discover which API versions are available for resource types, use the [List all resource providers](https://msdn.microsoft.com/library/azure/dn790524.aspx) operation. 

### PowerShell

The following example shows how to get the available API versions for a paticular resource type using Azure PowerShell 1.0 Preview.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
The output will be similar to:
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

For Azure PowerShell 0.9.8, use:

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure CLI

You can save the information (including the available API versions) for a resource provider to a file with the following command.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

You can open the file and find the **apiVersions** element

## Next steps

- To learn about creating Resource Manager templates, see [Authoring Azure Resource Manager templates](resource-group-authoring-templates.md).
- To learn about deploying resources, see [Deploy an application with Azure Resource Manager template](./azure-portal/resource-group-template-deploy.md).
