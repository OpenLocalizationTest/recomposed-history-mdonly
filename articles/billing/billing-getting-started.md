---
title: Getting started with Azure billing and cost management | Microsoft Docs
description: Learn about best practices and first things to do to optimize your bill
services: ''
documentationcenter: ''
author: jlian
manager: mattstee
editor: ''
tags: billing

ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: jlian

---
# Getting started with Azure billing and cost management

When you sign up for Azure, there are several things you should do to get a better idea of your spend. In the Azure portal, you can see your current cost breakdown and burn rate. You can also download past invoices and detail usage files. If you want to group costs for resources used for different projects or teams, look at resource tagging. If your organization has a reporting system that you prefer to use, check out the billing APIs. 

If you're an Enterprise Agreement (EA), Cloud Solution Provider (CSP), or Azure Sponsorship customer, then many features in this article do not apply for you. Instead, we have a different set of tools you can use, see [Additional resources for EA, CSP, and Sponsorship](#other-offers). 

## Before you add Azure services

### Estimate cost online using the pricing calculator

Check out the [pricing calculator](https://azure.microsoft.com/pricing/calculator/) and [total cost of ownership calculator](https://aka.ms/azure-tco-calculator) to get an estimate the monthly cost of the service you're interested in. For example, an A1 Windows Virtual Machine (VM) is estimated to cost $66.96 USD/month in compute hours if you leave it running the whole time:

![Screenshot of the pricing calculator showing that an A1 Windows VM is estimated to cost $66.96 USD per month](./media/billing-getting-started/pricing-calc.PNG)

For more information, see [pricing FAQ](https://azure.microsoft.com/pricing/faq/). Or if you want to talk to a person, call 1-800-867-1389.

### Check your subscription and access

Viewing costs require [subscriptions-level access](../active-directory/role-based-access-control-configure.md), but only the Account admin can access the [Account Center](https://account.windowsazure.com/Home/Index), change billing info, and manage subscriptions. The Account admin is the person who went through the sign-up process. To learn more, see [How to add or change Azure administrator roles](../billing-add-change-azure-subscription-administrator.md).

To see if you're the Account admin, go to the [Subscriptions blade in the Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) and look at the list of subscriptions you have access to. Look under **My Role**. If it says *Account admin*, then you're ok. If it says something else like *Owner*, then you don't have full privileges.

![Screenshot of your role in the Subscriptions view in the Azure portal](./media/billing-getting-started/sub-blade-view.PNG)

If you're not the Account admin, then somebody probably gave you partial access via [Azure Active Directory Role-based Access Control](../active-directory/role-based-access-control-configure.md) (RBAC). To manage subscriptions and change billing info, [find the Account admin](../billing-subscription-transfer.md#whoisaa) and ask them to perform the tasks or [transfer the subscription to you](../billing-subscription-transfer.md).

If your Account admin is no longer with your organization and you need to manage billing, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 

### Check if you have a spending limit on 

If you are using an Azure subscription that includes free credits, like the [monthly Azure credits via Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), then the spending limit is turned on for you by default. This way, when you spend all your credits, your credit card doesn't get charged. 

If you hit your spending limit, your services get disabled. That means your VMs are deallocated. To avoid service downtime, you must turn off the spending limit. Any overage gets charged onto your credit card on file. 

To see if you've got spending limit on, go to the [Subscriptions view in the Account Center](https://account.windowsazure.com/Subscriptions). A banner appears if your spending limit is on:

![Screenshot that shows a warning about spending limit being on in the Account Center](./media/billing-getting-started/spending-limit-banner.PNG)

Click the banner and follow prompts to remove the spending limit. For more information, see [Azure spending limit – How it works and how to enable or remove it](https://azure.microsoft.com/pricing/spending-limits/).

### Set up billing alerts

Set up billing alerts to get emails when your usage costs exceed an amount that you specify. If you have monthly credits, set up alerts for when you use up a specified amount. To learn more, see [Set up billing alerts for your Microsoft Azure subscriptions](https://docs.microsoft.com/azure/billing-set-up-alerts).

![Screenshot of a billing alert email](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> This feature is still in preview so you should check your usage regularly.

You might want to use the cost estimate from the pricing calculator as a guideline for your first alert.

### Understand limits and quotas for your subscription

There are default limits to each subscription for things like the number of CPU cores, IP address, etc. Be mindful of these limits. To learn more, see [Azure subscription and service limits, quotas, and constraints](../azure-subscription-service-limits.md). You can request an increase to your limit or quota by [contacting support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## As you add services

### Review the estimated cost in the portal

Typically when you add a service in the Azure portal, there's a view that shows you a similar estimated cost per month. For example, when you choose the size of your Windows VM you see the estimated monthly cost for the compute hours:

![Example: an A1 Windows VM is estimated to cost $66.96 USD per month](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="tags"></a> Add tags to your resources to group your billing data

You can use tags to group billing data for supported services. For example, if you run several VMs for different teams, then you can use tags to categorize costs by cost center (HR, marketing, finance) or environment (production, pre-production, test). 

![Screenshot that shows setting up tags in the portal](./media/billing-getting-started/tags.PNG)

The tags show up throughout different cost reporting views. For example, they're visible in your [cost analysis view](#costs) right away and [detail usage CSV](#invoice-and-usage) after your first billing period.

For more information, see [Using tags to organize your Azure resources](../azure-resource-manager/resource-group-using-tags.md).

### Consider enabling cost-cutting features like auto-shutdown for VMs

Depending on your scenario, you could configure auto-shutdown for your VMs in the Azure portal. To learn more, see [Auto-shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Screenshot of auto-shutdown option in the portal](./media/billing-getting-started/auto-shutdown.PNG)

Auto-shutdown isn't the same as when you shut down within the VM with power options. Auto-shutdown stops and deallocates your VMs. to stop additional usage charges. To learn more, see pricing FAQ for [Linux VMs](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) and [Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) to learn more about VM states.

For more cost-cutting features for your dev-test environments, check out [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

## <a name="cost-reporting"></a> After using services, view usage

### <a name="costs"></a> Regularly check the portal for cost breakdown and burn rate

After you get your services running, regularly check how much they're costing you. You can see the current spend and burn rate in Azure portal. 

1. Visit the [Subscriptions blade in Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Select your subscription you want to see. You might only have one to select.

3. You should see the cost breakdown and burn rate in the popup blade. It may not be supported for your offer (a warning would be displayed near the top). Wait 24 hours after you add a service for the data to populate.
    
    ![Screenshot of burn rate and breakdown in the Azure portal](./media/billing-getting-started/burn-rate.PNG)

4. You might want to pin the view to your dashboard.

    ![Screenshot of pinning a view to the dashboard](./media/billing-getting-started/pin.PNG)

5. Click **Cost analysis** in the list to the left to see the cost breakdown by resource.

    ![Screenshot of the cost analysis view in Azure portal](./media/billing-getting-started/cost-analysis.PNG)

6. You can filter by different properties like [tags](#tags), resource group, and timespan. Click **Apply** to confirm the filters and **Download** to export the view to a Comma Separated Values (.csv) file.

7. Click a resource to see spend history and how much it was costing you each day.

    ![Screenshot of the spend history view in Azure portal](./media/billing-getting-started/spend-history.PNG)

We recommend that you check the costs you see with the estimates you saw when you selected the services. If the costs wildly differ from estimates, double check the pricing plan (A1 vs A0 VM, for example) that you've selected for your resources. 

#### View costs for all your subscriptions in the Billing blade

If you manage multiple subscriptions as the Account admin, you can see the aggregate bill amount and breakdown for all of your subscriptions in the [Billing blade](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade). 

<!-- Add screenshots of multiple subs each with billed usage -->

### <a name="invoice-and-usage"></a> Download invoice and detail usage after your first billing period

After your first billing period, you can download your Portable Document Format (.pdf) invoice and Comma-Separated Values (.csv) usage details. These files help to understand what is ultimately billed to you after tax, discounts, and credits. For more information, see [How to download your Azure billing invoice and daily usage data](https://docs.microsoft.com/azure/billing-download-azure-invoice-daily-usage-date) and [Understand your bill for Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill).

![Screenshot of a PDF invoice](./media/billing-getting-started/invoice.png)

The tags that you set earlier show up in the detail usage .csv files:

![Screenshot that shows tags in the usage CSV](./media/billing-getting-started/csv.png)

### Billing API

Use our billing API to programmatically get usage data. Use the RateCard API and the Usage API together to get your billed usage. For more information, see [Gain insights into your Microsoft Azure resource consumption](https://docs.microsoft.com/azure/billing-usage-rate-card-overview).

## <a name="other-offers"></a> Additional resources for EA, CSP, and Sponsorship

Talk to your account manager or Azure partner to get started.

| Offer | Resources |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [EA portal](https://ea.azure.com/) and [help docs](https://ea.azure.com/helpdocs) |
| Cloud Solution Provider (CSP) | Talk to your provider |
| Azure Sponsorship | [Sponsorship portal](https://www.microsoftazuresponsorships.com/) |

If you're working IT for a large organization, we recommend reading [Azure enterprise scaffold](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance#resource-group) and the [enterprise IT white paper](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (PDF download, English only).
