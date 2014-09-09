<properties linkid="web-sites-staged-publishing" urlDisplayName="How to stage sites on Microsoft Azure" pageTitle="Staged Deployment on Microsoft Azure Websites" metaKeywords="Microsoft Azure Web Sites, Staged Deployment, Site Slots" description="Learn how to use staged publishing on Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Websites" authors="cephalin"  solutions="" writer="cephalin" manager="paulettm" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="9/9/2014" ms.author="cephalin" />

<a name="Overview"></a>
#Staged Deployment on Microsoft Azure Websites#
When you deploy your application to Azure Websites, you can deploy to a separate deployment slot instead of the default production slot, which are actually live sites with their own hostnames. This option is available in the **Standard** web hosting plan. Furthermore, you can swap the sites and site configurations between two deployment slots, including the production slot. Deploying your application to a deployment slot has the following benefits:

- You can validate website changes in a staging deployment slot before swapping it with the production slot.

- After a swap, the slot with previously staged site now has the previous production site. If the changes swapped into the production slot are not as you expected, you can perform the same swap immediately to get your "last known good site" back. 
 
- Deploying a site to a slot first and swapping it into production ensures that all instances of the slot are warmed up before being swapped into production. This eliminates downtime when you deploy your site. The traffic redirection is seamless, and no requests are dropped as a result of swap operations. 

Four deployment slots in addition to the production slot are supported for each website in the **Standard** plan. 

## Table of Contents ##
- [To Add a Deployment Slot to a Web Site](#Add)
- [About Configuration for Deployment Slots](#AboutConfiguration)
- [To Swap Deployment Slots](#Swap)
- [To Rollback a Production Site to Staging](#Rollback)
- [To Delete a Site Slot](#Delete)
- [Azure PowerShell cmdlets for Site Slots](#PowerShell)
- [Azure Cross-Platform Command-Line Interface (xplat-cli) commands for Site Slots](#CLI)

<a name="Add"></a>
##To Add a Deployment Slot to a Website##

The website must be running in the **Standard** hosting plan to enable multiple deployment slots. 

1. On the Quick Start page, or in the Quick Glance section of the Dashboard page for your website, click **Add a new deployment slot**. 
	
	![Add a new deployment slot][QGAddNewDeploymentSlot]
	
	> [WACOM.NOTE]
	> If the website is not already in **Standard** mode, you will receive the message **You must be in the standard mode to enable staged publishing**. At this point, you have the option to select **Upgrade** and navigate to the **Scale** tab of your website before continuing.
	
2. In the **Add New Deployment Slot** dialog, give the slot a name, and select whether to clone website configuration from another existing deployment slot. Click the check mark to continue. 
	
	![Configuration Source][ConfigurationSource1]
	
	The first time you create a slot, you will only have two choices: clone configuration from the default slot in production or not at all. 
	
	After you have created several slots, you will be able to clone configuration from a slot other than the one in production:
	
	![Configuration sources][MultipleConfigurationSources]

5. In the list of websites, expand the mark to the left of your website name to reveal the deployment slot. It will have the website name followed by the deployment slot name. 
	
	![Site List with Deployment Slot][SiteListWithStagedSite]
	
4. When you click the name of the deployment site slot, a page will open with a set of tabs just like any other website. <strong><i>your-website-name</i>(<i>deployment-slot-name</i>)</strong> will appear at the top of the portal page to remind you that you are viewing the deployment site slot.
	
	![Deployment Slot Title][StagingTitle]
	
5. Click the site URL in the dashboard view. Notice the the deployment slot has its own hostname and is also a live site. To limit public access to the deployment slot, see [Azure Web Sites – block web access to non-production deployment slots](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

	-	 

There is no content. You can deploy to the slot from a different repository branch, or an altogether different repository. You can also change the slot's configuration. Use the publish profile or deployment credentials associated with the deployment slot for content updates.  For example, you can [publish to this slot with git](http://azure.microsoft.com/en-us/documentation/articles/web-sites-publish-source-control/).

<a name="AboutConfiguration"></a>
##About Configuration for Deployment Slots##
When you clone configuration from another deployment slot, the cloned configuration is editable. The following lists show the configuration that will change when you swap slots.

**Configuration that will change on slot swap**:

- General settings
- Connection strings
- Handler mappings
- Monitoring and diagnostic settings

**Configuration that will not change on slot swap**:

- Publishing endpoints
- Custom Domain Names
- SSL certificates and bindings
- Scale settings

**Notes**:

- Multiple deployment slots are only available for sites in the **Standard** web hosting plan.

- When you site has multiple slots, you cannot change the hosting plan.

- A slot that you intend to swap into production needs to be configured exactly as you want to have it in production.

- By default, a deployment slot will point to the same database as the production site. However, you can configure the deployment slot to point to an alternate database by changing the database connection string(s) for the deployment slot. You can then restore the original database connection string(s) on the deployment slot right before you swap it into production.


<a name="Swap"></a>
##To Swap Deployment Slots##

1. To swap deployment slots, select the deployment slot in the websites list that you want to swap and click the **Swap** button in the command bar. 
	
	![Swap Button][SwapButtonBar]
	
2. The Swap Deployments dialog appears. The dialog lets you choose which site slot should be the source and which site should be the destination.
	
	![Swap Deployments Dialog][SwapDeploymentsDialog]
	
3. Click the checkmark to complete the operation. When the operation finishes, the site slots have been swapped.


<a name="Rollback"></a>
##To Rollback a Production Site to Staging##
If any errors are identified in production after a slot swap, roll the slots back to their pre-swap states by swapping the same two slots immediately. 

<a name="Delete"></a>
##To Delete a Site Slot##

In the command bar at the bottom of the Azure Websites portal page, click **Delete**. You will be given the option to delete the website and all deployment slots, or delete only the deployment slot. 

![Delete a Site Slot][DeleteStagingSiteButton]

**Notes**:

- Scaling is not available for non-production slots. It is only available for production slots.

- Linked resource management is not supported for non-production slots. 

- You can still publish directly to your production slot if you wish.

- By default, your deployment slots (sites) share the same resources as your production slots (sites) and run on the same VMs. If you run stress testing on a stage slot, your production environment will experience a comparable stress load. 
	
	> [WACOM.NOTE] In the [Azure Preview Portal](https://portal.azure.com) only, you can avoid this potential impact on a production slot by temporarily moving the non-production slot to a different Web Hosting Plan. Note that the test and production slots must once again share the same Web Hosting Plan before you can swap the test slot into production.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Azure PowerShell cmdlets for Site Slots 

Azure PowerShell is a module that provides cmdlets to manage Azure through Windows PowerShell, including support for managing Azure Websites deployment slots. 

- For information on installing and configuring Azure PowerShell, and on authenticating Azure PowerShell with your Windows Azure subscription, see [How to install and configure Windows Azure PowerShell](http://www.windowsazure.com/en-us/documentation/articles/install-configure-powershell).  

- To list the cmdlets available for Azure Websites in PowerShell, call `help AzureWebsite`. 

----------

###Get-AzureWebsite
The **Get-AzureWebsite** cmdlet presents information about Azure websites for the current subscription, as in the following example. 

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
You can create a site slot for any website in Standard mode by using the **New-AzureWebsite** cmdlet and specifying the names of both the site and slot. Also indicate the same region as the site for deployment slot creation, as in the following example. 

`New-AzureWebsite siteslotstest -Slot staging -Location "West US"`

----------

###Publish-AzureWebsiteProject
You can use the **Publish-AzureWebsiteProject** cmdlet for content deployment, as in the following example. 

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

###Show-AzureWebsite
After content and configuration updates have been applied to the new slot, you can validate the updates by browsing to the slot using the **Show-AzureWebsite** cmdlet, as in the following example.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

----------

###Switch-AzureWebsiteSlot
The **Switch-AzureWebsiteSlot** cmdlet can perform a swap operation to make the updated deployment slot the production site, as in the following example. The production site will not experience any down time, nor will it undergo a cold start. 

`Switch-AzureWebsiteSlot -Name siteslotstest`

----------

###Remove-AzureWebsite
If a deployment slot is no longer needed, it can be deleted by using the **Remove-AzureWebsite** cmdlet, as in the following example.

`Remove-AzureWebsite -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
##Azure Cross-Platform Command-Line Interface (xplat-cli) commands for Site Slots

The Azure Cross-Platform Command-Line Interface (xplat-cli) provides cross-platform commands for working with Azure, including support for managing deployment slots on Azure Websites. 

- For instructions on installing and configuring the xplat-cli, including information on how to connect xplat-cli to your Azure subscription, see [Install and Configure the Azure Cross-Platform Command-Line Interface](http://www.windowsazure.com/en-us/documentation/articles/xplat-cli). 

-  To list the commands available for Azure Websites in the xplat-cli, call `azure site -h`. 

----------
###azure site list
For information about the Azure websites in the current subscription, call **azure site list**, as in the following example.
 
`azure site list siteslotstest`

----------
###azure site create
To create a site slot for any website in Standard mode, call **azure site create** and specify the name of an existing site and the name of the slot to create, as in the following example.

`azure site create siteslotstest --slot staging`

To enable source control for the new slot, use the **--git** option, as in the following example.
 
`azure site create --git siteslotstest --slot staging`

----------
###azure site swap
To make the updated deployment slot the production site, use the **azure site swap** command to perform a swap operation, as in the following example. The production site will not experience any down time, nor will it undergo a cold start. 

`azure site swap siteslotstest`

----------
###azure site delete
To delete a deployment slot that is no longer needed, use the **azure site delete** command, as in the following example.

`azure site delete siteslotstest --slot staging`

----------
## Next Steps ##
[Azure Web Sites – block web access to non-production deployment slots](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure Free Trial](http://azure.microsoft.com/en-us/pricing/free-trial/)


<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
