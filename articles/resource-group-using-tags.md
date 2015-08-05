<properties 
	pageTitle="Using tags to organize your Azure resources" 
	description="Shows how to apply tags to organize resources for billing and managing." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac"
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="AzurePortal" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/05/2015" 
	ms.author="tomfitz"/>


# Using tags to organize your Azure resources

Resource Manager enables you to logically organize resources by applying tags. The tags consist of key/value pairs that identify resources with properties that you define. To mark resources as belonging to the same category, apply the same tag to those resources. 

When you view resources with a particular tag, you see resources from all of your resource groups. You are not limited to only resources in the same resource group which enables you to organize your resources in a way that is independent of the deployment relationships. Tags can be particularly helpful when you need to organize resources for billing or management. 

> [AZURE.NOTE] You can only apply tags to resources that support Resource Manager operations. If you created a Virtual Machine, Virtual Network, or Storage through the classic deployment model (such as through the Azure portal or [Service Management API](https://msdn.microsoft.com/library/azure/dn948465.aspx)), you cannot apply a tag to that resource. You must re-deploy these resources through Resource Manager to support tagging. All other resources support tagging.

## Tags in the preview portal

Tagging resources and resource groups in the preview portal is easy. Use the Browse hub to navigate to the resource or resource group you’d like to tag and click the Tags part in the Overview section at the top of the blade. 

![Tags part on resource and resource group blades](./media/resource-group-using-tags/rgblade.png)

This will open a blade with the list of tags that have already been applied. If this is your first tag, the list will be empty. To add a tag, simply specify a name and value and press Enter. After you've added a few tags, you'll notice autocomplete options based on pre-existing tag names and values to better ensure a consistent taxonomy across your resources and to avoid common mistakes, like misspellings.

![Tag resources with name/value pairs](./media/resource-group-using-tags/tag-resources.png)

From here, you can click on each individual tag to view a list of all the resources with the same tag. Of course, if this is your first tag, that list won't be very interesting. For now, let's jump over to PowerShell to tag all of our resources quickly.


## Tagging with PowerShell

First thing's first, grab the latest [Azure PowerShell module](./install-configure-powershell.md). If this is your first time using the Azure PowerShell module, [read the documentation](./install-configure-powershell.md) to get up to speed. For the purposes of this article, we'll assume you've already added an account and selected a subscription with the resources you want to tag.

Tagging is only available for resources and resource groups available from [Resource Manager](http://msdn.microsoft.com/library/azure/dn790568.aspx), so the next thing we need to do is switch to use Resource Manager. For more information, see [Using Azure PowerShell with Azure Resource Manager](powershell-azure-resource-manager.md).

    Switch-AzureMode AzureResourceManager

Tags exist directly on resources and resource groups, so to see what tags are already applied, we can simply get a resource or resource group with `Get-AzureResource` or `Get-AzureResourceGroup`, respectively. Let's start with a resource group.

![Getting tags with Get-AzureResourceGroup in PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

This cmdlet returns several bits of metadata on the resource group including what tags have been applied, if any. To tag a resource group, we'll simply use `Set-AzureResourceGroup` and specify a tag name and value.

![Setting tags with Set-AzureResourceGroup in PowerShell](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

Remember that tags are updated as a whole, so if you are adding one tag to a resource that's already been tagged, you'll need to use an array with all the tags you want to keep. To remove one, simply save the array without the one you want to remove. 

The process is the same for resources, except you'll use the `Get-AzureResource` and `Set-AzureResource` cmdlets. To get resources or resource groups with a specific tag, use `Get-AzureResource` or `Get-AzureResourceGroup` cmdlet with the `-Tag` parameter.

![Getting tagged resources and resource groups with Get-AzureResource and Get-AzureResourceGroup in PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Tagging with REST API

The portal and PowerShell both use the [Resource Manager REST API](http://msdn.microsoft.com/library/azure/dn790568.aspx) behind the scenes. If you need to integrate tagging into another environment, you can get tags with a GET on the resource id and update the set of tags with a PATCH call.


## Managing your taxonomy

Earlier, we talked about how autocomplete helps you ensure consistency and avoid mistakes. Autocomplete is populated based on the taxonomy of available tags setup for the subscription. Each tag you add to a resource or resource group is automatically added to the subscription-wide taxonomy, but you can also prepopulate that taxonomy with tag names and values you'd like to use as resources are tagged in the future.

To get a list of all tags within a subscription using PowerShell, use the `Get-AzureTag` cmdlet.

![Get-AzureTag in PowerShell](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


You may see tags that start with "hidden-" and "link:". These are internal tags, which you should ignore and avoid changing. 

Use the `New-AzureTag` cmdlet to add new tags to the taxonomy. These tags will be included in the autocomplete even though they haven't been applied to any resources or resource groups, yet. To remove a tag name/value, first remove the tag from any resources it may be used with and then use the `Remove-AzureTag` cmdlet to remove it from the taxonomy.

To view your taxonomy of tags in the portal, use the Browse hub to view Everything and then select Tags.

![Find tags via the Browse hub](./media/resource-group-using-tags/browse-tags.png)

Pin the most important tags to your Startboard for quick access and you're ready to go. Have fun!

![Pin tags to the Startboard](./media/resource-group-using-tags/pin-tags.png)

## Tagging and billing

For supported services, you can use tags to group your billing data. For example, [Virtual Machines integrated with Azure Resource Manager](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) enable 
you to define and apply tags to organize the billing usage for virtual machines. If you are running multiple VMs for different organizations, you can use the tags to group usage by cost center.  
You can also use tags to categorize costs by runtime environment; such as, the billing usage for VMs running in production environment.

You can retrieve information about tags through the [usage api](billing-usage-rate-card-overview.md) or the usage comma-separated values (CSV) file that you can download from 
the [Azure accounts portal](https://account.windowsazure.com/) or [EA portal](https://ea.azure.com).

When you download the usage CSV for services that support tags with billing, the tags will appear in the **Tags** column. For more details, see [Understand your bill for Microsoft Azure](billing-understand-your-bill.md).

![See tags in billing](./media/resource-group-using-tags/billing_csv.png)

## Next Steps

- For an introduction to using Azure PowerShell when deploying resources, see [Using Azure PowerShell with Azure Resource Manager](./powershell-azure-resource-manager.md).
- For an introduction to using Azure CLI when deploying resources, see [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management](./xplat-cli-azure-resource-manager.md).
- For an introduction to using the preview portal, see [Using the Azure preview portal to manage your Azure resources](./resource-group-portal.md)  
  

  


