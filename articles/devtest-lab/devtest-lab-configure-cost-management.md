<properties
	pageTitle="Configure cost management | Microsoft Azure"
	description="Learn how to configure your DevTest Lab cost management features."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="tarcher"/>

# Configure cost management

## Overview

The Cost Management feature of DevTest Labs helps you track and control the cost of your lab. 
This article illustrates how to use the **Monthly Estimated Cost Trend** chart  
to view the current month's estimated cost-to-date as well as the projected end-of-month cost.

## Enabling the Monthly Estimated Cost Trend chart

In order to enable the Monthly Estimated Cost Trend chart, follow these steps: 

1. Sign in to the [Azure portal](https://portal.azure.com).

1. Tap **Browse**, and then tap **DevTest Labs** from the list.

1. From the list of labs, tap the desired lab.   

1. Tap **Settings**.

	![Settings](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. On the lab's **Settings** blade, under **Cost Policies**, tap **Cost Thresholds**.

	![Menu](./media/devtest-lab-configure-cost-management/menu.png)
 
1. Tap **On** to enable this policy, and **Off** to disable it.

1. Tap **Save**.

Once you enable this policy, it can take up to 24 hours before the chart will display your estimated and projected costs.
Currently, data is refreshed every 24 hours with 90–95% of the cost reported. 
In cases of live site issues - such as outages - it can take more than 24 hours to report the cost.
 
The following screen shot shows an example of a cost chart. 

![Cost chart](./media/devtest-lab-configure-cost-management/graph.png)

The **Estimated Cost** value is the current month's estimated cost-to-date while the **Projected Cost** is the estimated
cost for the entire month. 

As the it states above the chart, the costs you see in the chart are *estimated* costs using Pay-As-You-Go offer rates.
Additionally, the following are *not* included in the cost calculation:

- Your offer rates. Currently, we are not able to use your offer rates (shown under your subscription) that you have negotiated with Microsoft or Microsoft partners. We are using Pay-As-You-Go rates.
- Your taxes
- Your discounts
- Your billing currency. Currently, the lab cost is displayed only in USD currency.

## Next steps

Once you've defined and applied the various VM policy settings for your lab, here are some things to try next:

- [Define lab policies](./devtest-lab-set-lab-policy.md) - Learn how to set the various policies used to govern how your lab and its VMs are used. 
- [Create custom image](./devtest-lab-create-template.md) - When you create a VM, you specify a base, which can be either a custom image or a Marketplace image. This article illustrates
how to create a custom image from a VHD file.
- [Configure Marketplace images](./devtest-lab-configure-marketplace-images.md) - DevTest Labs supports creating new VMs based on Azure Marketplace images. This article
illustrates how to specify which, if any, Azure Marketplace images can be used when creating new VMs in a lab.
- [Create a VM in a lab](./devtest-lab-add-vm-with-artifacts.md) - Illustrates how to create a new VM from a base image (either custom or Marketplace), and how to work with
artifacts in your VM.