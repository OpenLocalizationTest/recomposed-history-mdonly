---
title: Track B2B messages in the Operations Management Suite portal by using a query | Microsoft Docs
description: Learn how to track B2B messages in the Operations Management Suite portal by using a query.
author: padmavc
manager: erikre
editor: ''
services: logic-apps
documentationcenter: ''

ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2016
ms.author: padmavc

---
# Track B2B messages in the Operations Management Suite portal by using a query
To track business-to-business (B2B) messages in the Operations Management Suite portal, you can create a query that filters data for a specific interchange control number.

## Prerequisites

For debugging and for more detailed diagnostics information, turn on diagnostics in your [integration account](app-service-logic-monitor-b2b-message.md) for your [logic apps](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) that have X12 connectors. Then, do the steps to [publish diagnostic data](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) to the Operations Management Suite portal.

## To create a query to search for a specific interchange control number

1. On the start page, select **Log Search**.  
![Select log search](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. In the search box, type **Type="AzureDiagnostics"**. To filter data, select **Add**.  
![Select query](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Type **interchange**, select **event_record_messageProperties_interchangeControlNumber_s**, and then select **Add**.  
![Add filter](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Select the control number that you want to filter data for, and then select **Apply**.  
![Search on the control number](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Find the query that you created to filter data for the selected control number.   
![Find the query](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Type a name for the query, and then save it.   
![Save the query](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. To view the query, and to use it in future searches, select **Favorites**.  
![View the query](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. You can update the query to search for a new interchange control number.  
![Update the query](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## Next steps
* Learn more about [custom tracking schemas](app-service-logic-track-integration-account-custom-tracking-shema.md).   
* Learn more about [AS2 tracking schemas](app-service-logic-track-integration-account-as2-tracking-shemas.md).    
* Learn more about [X12 tracking schemas](app-service-logic-track-integration-account-x12-tracking-shemas.md).  
* Learn more about the [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
