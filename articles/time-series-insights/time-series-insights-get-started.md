---
title: Create an Azure Time Series Insights environment | Microsoft Docs
description: In this tutorial, you will learn how to create Time Series Environment, connect it to an Event Source and ready to analyze your event data in minutes.
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun

ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: omravi
---

# Create a new Time Series Insights environment in the Azure portal

Time Series Insights environment is an Azure resource with ingress and storage capacity. Customers provision environments via the Azure portal with the required capacity.

## Steps to create the environment

Follow these steps to create your environment:

1.	Sign in to the [Azure portal](https://portal.azure.com).
2.	Click the plus sign (“+”) in the top left corner.
3.	Search for “Time Series Insights” in the search box.
  
  ![Create the Time Series Insights environment](media/get-started/getstarted-create-environment1.png)
  
4.	Select “Time Series Insights”, click “Create”.
   
  ![Create the Time Series Insights resource group](media/get-started/getstarted-create-environment2.png)
  
5.	Specify environment name. This name will represent the environment in [time series explorer](https://insights.timeseries.azure.com).
6.	Select a subscription. Choose one that contains your event source. Time Series Insights can auto-detect Azure IoT Hub and Event Hub resources existing in the same subscription.
7.	Select or create a resource group. A resource group is a collection of Azure resources used together.
8.	Select a hosting location. To void moving data across data centers, choose location that contains your event source.
9.	Select a pricing tier.
10.	Select capacity. You can change capacity of an environment after creation.
11.	Create your environment. Remember to pin your environment to the dashboard for easy access whenever you sign in.

![Create the Time Series Insights pin to dashboard](media/get-started/getstarted-create-environment3.png)

## Next steps
After creation of the environment, you are ready to create an event source and start streaming data into the environment.
To access environment in the time series explorer, use the Time Series Insights URL as shown below or simply open [time series explorer](https://insights.timeseries.azure.com).

![Create the Time Series Insights explorer](media/get-started/getstarted-create-environment4.png)

