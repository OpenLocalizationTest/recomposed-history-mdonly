<properties 
	pageTitle="Move Resources to New Resource Group" 
	description="Use Azure PowerShell or REST API to move resources to a new resource group for Azure Resource Manager." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/11/2016" 
	ms.author="tomfitz"/>

# Move resources to new resource group or subscription

This topic shows how to move resources from one resource group to another resource group. You can also move resources to a new subscription (however the subscription must exist within the same [tenant](./active-directory/active-directory-howto-tenant.md)). You may need to move resources when you decide that:

1. For billing purposes, a resource needs to live in a different subscription.
2. A resource no longer shares the same lifecycle as the resources it was previously grouped with. You want to move it to a new resource group so you can manage that resource separately from the other resources.

When moving resources, both the source group and the target group are locked for the duration of the operation. Write and delete operations are blocked on the groups until the move completes.

You cannot change the location of the resource. Moving a resource only moves it to a new resource group. The new resource group may have a different location, but that does not change the location of the resource.

## Checklist before moving resources

There are some important steps to perform before moving a resource. By verifying these conditions, you can avoid errors.

1. The service must support the ability to move resources. See the list below for information about which [services support moving resources](#services-that-support-move).
2. The destination subscription must be registered for the resource provider of the resource being moved. If not, you will receive an error stating that the **subscription is not registered for a resource type**. You might encounter this problem when moving a resource to a new subscription, but that subscription has never been used 
with that resource type. To learn how to 
check the registration status and register resource providers, see [Resource providers and types](../resource-manager-supported-services/#resource-providers-and-types).
3. If you are using Azure PowerShell or Azure CLI, use the latest version. To update your version, run the Microsoft Web Platform Installer and check if a 
new version is available. For more information, see [How to install and configure Azure PowerShell](powershell-install-configure.md) and [Install the Azure CLI](xplat-cli-install.md).
4. If you are moving App Service app, you have reviewed [App Service limitations](#app-service-limitations).

## Services that support move

For now, the services that support moving to both a new resource group and subscription are:

- API Management
- App Service apps (see [App Service limitations](#app-service-limitations) below)
- Automation
- Batch
- CDN
- Data Factory
- DocumentDB
- HDInsight clusters
- Key Vault
- Mobile Engagement
- Notification Hubs
- Operational Insights
- Redis Cache
- Scheduler
- Search
- SQL Database server (see [SQL Database limitations](#sql-database-limitations) below)

## Services that partially support move

The services that support moving to a new resource group but not a new subscription are:

- Cloud Services
- Storage (classic)
- Virtual Machines (classic)
- Virtual Networks

## Services that do not support move

The services that currently do not support moving a resource are:

- Application Insights
- Express Route
- Storage
- Virtual Machines

## App Service limitations

When working with App Service apps, you cannot move only an App Service plan. To move App Service apps, your options are:

- Move the App Service plan and all other App Service resources in that resource group to a new resource group that does not already have App Service resources. This means moving even the resources that are not associated with the App Service plan. 
- Move the apps to a different resource group, but keep all App Service plans in the original resource group.

If your original resource group also includes an Application Insights resource, you cannot move that resource because Application Insights does not currently support the move operation. If you 
include the Application Insights resource when moving App Service apps, the entire move operation will fail. However, the Application Insights and App Service plan do not need to 
reside in the same resource group as the app for the app to function correctly.

For example, if your resource group contains:

- **web-app-a** which is associated with **app-service-plan-a** and **app-insights-a**
- **web-app-b** which is associated with **app-service-plan-b** and **app-insights-b**

Your options are:

- Move **web-app-a**, **app-service-plan-a**, **web-app-b**, and **app-service-plan-b**
- Move **web-app-a** and **web-app-b**
- Move **web-app-a**
- Move **web-app-b**

All other combinations involve either moving a resource type that can't move (Application Insights) or leaving behind a resource type that can't be left behind when moving an App Service plan (any type of App Service resource).

## SQL Database limitations

You cannot move a SQL database separately from its server. The database and server must reside in the same resource group. When you move a SQL server, all of its databases are also moved. 

## Using portal to move resources

You can move some resources through the portal; however, not all resource providers that support the move operation provide that functionality through the portal. 

To move a resource, select the resource and then select the **Move** button.

![move resource](./media/resource-group-move-resources/move-resources.png)

You specify the destination subscription and resource group when moving the resource. If other resources must be moved with the resource, they are listed.

![select destination](./media/resource-group-move-resources/select-destination.png)

In **Notifications**, you will see that the move operation is running.

![show move status](./media/resource-group-move-resources/show-status.png)

When it has completed, you are notified of the result.

![show move result](./media/resource-group-move-resources/show-result.png)

For another option to move resources to a new resource group (but not subscription), select the resource you wish to move.

![select resource to move](./media/resource-group-move-resources/select-resource.png)

Select its **Properties**.

![select properties](./media/resource-group-move-resources/select-properties.png)

If available for this resource type, select **Change resource group**.

![change resource group](./media/resource-group-move-resources/change-resource-group.png)

You can select which resources to move, and the resource group to move them to.

![move resources](./media/resource-group-move-resources/select-group.png)

You will be notified of its status when the move operation completes.

## Using PowerShell to move resources

To move existing resources to another resource group or subscription, use the **Move-AzureRmResource** command.

The first example shows how to move one resource to a new resource group.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

The second example shows how to move multiple resources to a new resource group.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

To move to a new subscription, include a value for the **DestinationSubscriptionId** parameter.

You will be asked to confirm that you want to move the specified resources.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## Using Azure CLI to move resources

To move existing resources to another resource group or subscription, use the **azure resource move** command. The following example shows how to move a Redis Cache to a new resource group. In the **-i** parameter, provide a comma-separated list of the resource id's to move.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
	
You will be asked to confirm that you want to move the specified resource.
	
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Using REST API to move resources

To move existing resources to another resource group or subscription, run:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

In the request body, you specify the target resource group and the resources to move. For more information about the move REST operation, see [Move resources](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## Next steps
- To learn about PowerShell cmdlets for managing your subscription, see [Using Azure PowerShell with Resource Manager](powershell-azure-resource-manager.md).
- To learn about Azure CLI commands for managing your subscription, see [Using the Azure CLI with Resource Manager](xplat-cli-azure-resource-manager.md).
- To learn about portal features for managing your subscription, see [Using the Azure Portal to manage resources](./azure-portal/resource-group-portal.md).
- To learn about applying a logical organization to your resources, see [Using tags to organize your resources](resource-group-using-tags.md).
