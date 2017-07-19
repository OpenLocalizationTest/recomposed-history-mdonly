---
title: Portals for creating and editing log queries in Azure Log Analytics | Microsoft Docs
description: This article describes the portals that you can use in Azure Log Analytics to create and edit log searches.  
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren

---
# Portals for creating and editing log queries in Azure Log Analytics

> [!NOTE]
> This article describes log searches in Azure Log Analytics using the next generation query language which is currently in public preview.  You can learn more about it and get the procedure to upgrade your workspace at Upgrade your [Azure Log Analytics workspace to next generation log search](log-analytics-log-search-upgrade.md).  
>
> If your workspace hasn't been upgraded to the next generation query language, you should refer to [Find data using log searches in Log Analytics](log-analytics-log-searches.md).

You use log searches in a variety of places throughout Log Analytics to retrieve data from the workspace.  For actually creating and editing queries in addition to working interactively with returned data though, you have two options that are described below.  

## Log search portal
The Log Search portal is accessible from the Azure portal or the OMS portal.  It's suitable for creating basic queries that can be created on a single line.  The Log Search portal can be used without launching an external portal, and you can use it to perform a variety of functions with log searches including creating alert rules, creating computer groups, and exporting the results of the query.  

The Log Search portal provides multiple features for editing the query without having a full knowledge of the query language.  You can get a summary of these features in [Create log searches in Azure Log Analytics using the Log Search portal](log-analytics-log-search-log-search-portal.md).


![Log Search portal](media/log-analytics-log-search-portals/log-search-portal.png)

## Advanced Analytics portal
The Advanced Analytics portal is a dedicated portal that provides advanced functionality not available in the Log Search portal.  Features include the ability to edit a query on multiple lines, selectively execute code, context sensitive Intellisense, and Smart Analytics.  The Advanced Analytics portal is most suitable for designing complex queries that are either saved as a log search or copied and pasted into other Log Analytics elements.

![Advanced Analytics portal](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


Because of its advanced features, it's a common scenario to edit queries in the Advanced Analytics console and then copy and paste them elsewhere such as the Log Search portal or View Designer.  Because the Advanced Analytics portal supports queries with multiple lines though, you need to take the following into consideration when copying a query from this portal.

- Comments must be removed from the query before it's copied and pasted into another location.  You can comment a line by preceding it with two slashes (//).  When you paste a multiple line query into a single line, line breaks are removed.  If comments are included, all characters after the first comment are considered part of the comment.


## Next steps

- Walk through a tutorial on using the [Log Search portal](log-analytics-log-search-log-search-portal.md) or the [Advanced Analytics portal](https://docs.loganalytics.io/learn/tutorial_getting_started_with_analytics_portal.html) to create queries.
- Check out a [tutorial on writing queries](https://docs.loganalytics.io/learn/tutorial_getting_started_with_queries.html) using the next generation query language.