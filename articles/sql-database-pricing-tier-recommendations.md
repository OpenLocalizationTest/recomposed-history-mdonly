<properties 
   pageTitle="Pricing tier recommendations for Azure SQL Database" 
   description="When changing pricing tiers in the Azure portal, pricing tier recommendations are provided that recommend the tier that is best suited for running an existing Azure SQL Database’s workload." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="04/27/2015"
   ms.author="sstein"/>

# Pricing tier recommendations (preview) for SQL Database 

 When changing pricing tiers in the Azure portal, pricing tier recommendations are provided that recommend the tier that is best suited for running an existing Azure SQL Database’s workload.

> [AZURE.NOTE] Pricing tier recommendations are currently in preview and are only available for Web and Business databases when using the [Azure Portal](https://portal.azure.com/).


## Overview

Azure analyzes current performance and feature requirements by assessing historical resource usage for a SQL Database. In addition, the minimum acceptable service tier is determined based on the size of the database, and enabled [business continuity](https://msdn.microsoft.com/library/azure/hh852669.aspx) features. 

This information is analyzed and the service tier and performance level that is best suited for running the database’s typical workload and maintaining it's current feature set is recommended.

- The service examines the previous 15 to 30 days of historical data (resource usage, database size, and database activity) and performs a comparison between the amount of resources consumed and the actual limitations of the currently available service tiers and performance levels.
- Data is analyzed in 15 second intervals and each interval's resultset is categorized into the existing service tier and performance level that is best suited for handling that resultset's workload.
- These 15 second samples are then aggregated into the larger 15-30 day analysis and the service tier and performance level that can optimally handle 95% of the historical workload is recommended.

### Recommendations

Based on your database's usage, there are 4 categories of recommendations that can be encountered:


| Recommendation | Description |
| :--- | :--- |
| Scale Up       | Upgrading to a higher service tier / performance level will increase performance. |
| Scale Down     | Downgrading to a lower service tier / performance level should be sufficient for your typical workload. |
| No Change      | The current service tier / performance level is ideal for your typical workload. |
| Unavailable | A database requires a minimum workload or approximately 14 days of activity. There is not enough data to provide a valid recommendation. |



#### Effects of business continuity features and database size on recommendations

Recommendations are also based on the current size of your database and the feature sets that your database is using. The service will not recommend moving to a tier that provides less features than your database requires.

- If your database has Standard Geo-Replication enabled, then the minimum recommendation will be Standard tier (S0).
- If your database has Active Geo-Replication enabled, then the minimum recommendation will be Premium tier (P1). 

For a list of available business continuity features for each service tier, see [Azure SQL Database Business Continuity](https://msdn.microsoft.com/library/azure/hh852669.aspx).

Additionally, The service will not recommend a service tier that has a max database size limit that is smaller than your current database's size.  For a list of max database sizes for each service tier, see [Azure SQL Database Service Tiers and Performance Levels](https://msdn.microsoft.com/library/azure/dn741336.aspx).




## Getting pricing tier recommendations

Get pricing tier recommendations by selecting an existing database and clicking on the **Pricing tier** tile.

> [AZURE.NOTE] You must sign up for the preview to enable recommendations. You will be presented with the option to sign up for the preview after clicking the **Pricing tier** tile during step 5 below.

1. Sign in to the [Azure Portal](https://portal.azure.com/).
2. Click **BROWSE** in the left menu.
3. Click **SQL databases** in the **Browse** blade.
4. In the **SQL databases** blade, click the database that you want the service to analyze.

    ![Select database][1]

5. On the database blade, select the **Pricing tier** tile.

    ![Pricing tier][2]

    *If you haven't already signed up for the preview, clicking the Pricing tier tile will present you with the option to sign up for the service tier advisor preview. click the sign up message at the top of the **Choose your pricing tier** blade and sign up for the preview. You only need to sign up one time for each subscription. Subsequent visits will not require this sign-up step.



7. After clicking the **Pricing tier** tile you will be presented with the **Recommended pricing tiers** blade where you can click the suggested tier and then click the **Select** button to change to that tier.

    ![Sign up for the preview][4]

8. Optionally, click **View usage details** to open the **Pricing tier recommendations (preview)** blade where you can view the recommended tier for the database, a feature comparison between current and recommended tiers, and a graph of the  historical resource usage analysis.

    ![Sign up for the preview][5]



## Summary

Pricing tier recommendations provide an automated experience for gathering telemetry data for each SQL database and recommending the best service tier/performance level combination based on a database's actual performance needs and feature requirements. Click the **Pricing tier** tile on a database blade to see pricing tier recommendations.



## Next steps

Depending on the details of your specific database, performing an upgrade or downgrade usually does not happen instantaneously. The management portal will provide notifications as the database transitions to it's new tier, or you can monitor the upgrade status by querying the [sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx) view in the SQL Database Server's master database.


<!--Image references-->
[1]: ./media/sql-database-pricing-tier-recommendations/select-database.png
[2]: ./media/sql-database-pricing-tier-recommendations/pricing-tier.png
[3]: ./media/sql-database-pricing-tier-recommendations/preview-sign-up.png
[4]: ./media/sql-database-pricing-tier-recommendations/choose-pricing-tier.png
[5]: ./media/sql-database-pricing-tier-recommendations/usage-details.png


