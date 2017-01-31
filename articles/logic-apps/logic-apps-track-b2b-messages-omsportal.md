---
title: Track B2B messages in Operations Management Suite portal - Azure | Microsoft Docs
description: How to track B2B messages in Operations Management Suite portal
author: padmavc
manager: anneta
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
# Track B2B messages in Operations Management Suite portal
B2B communication involves the exchange of messages between two running business processes or applications. Use the following web-based tracking features in Operations Management Suite portal to confirm whether messages are processed correctly:

* Message count and status
* Acknowledgments status
* Correlate messages with acknowledgments
* Detailed error descriptions for failures
* Search capabilities

## Prerequisites
* An Azure account. You can create a [free account](https://azure.microsoft.com/free).
* An integration account. You can create an [integration account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) and set up logging. To set up logging, see  [Monitor B2B messages](logic-apps-monitor-b2b-message.md).
* A logic app. You can create a [logic app](../logic-apps/logic-apps-create-a-logic-app.md) and set up logging. To set up logging, see [Azure Diagnostics and alerts](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts).

## Adding Logic Apps B2B solution to Operations Management Suite portal

1. In the Azure portal, select **More Services**, search for log analytics and then select **Log Analytics**.   
![Search log analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Select your **Log Analytics**.  
![Select log analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Select **OMS Portal**. The Operations Management Suite portal home page appears.   
![Browse Operations Management Suite portal](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Select **Solutions Gallery**.    
![Select Solutions Gallery](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Select **Logic Apps B2B**.     
![Select Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Select **Add** to add **Logic Apps B2B** messages to the home page.  
![Select Add](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. **Logic Apps B2B Messages** appears on the home page.   
![Select home page](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## Tracking data in Operations Management Suite portal

1. After the messages are processed, the updated message count appears.   
![Updated messages](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. Select **Logic Apps B2B Messages** on the home page to view the AS2 and X12 message status.  The data is based on a single day.
![Select Logic Apps B2B Messages](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. Select an AS2 or X12 message by status to go to the message list. The next screenshot shows the AS2 message status. The AS2 and X12 message status property descriptions appear later in this article.  
![Select AS2 message status](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)
4. Select a row in the AS2 or X12 message list to go to the log search.  Log search lists all of the actions that have the same run ID.
![Select message status](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

### AS2 Message list property descriptions
| Property | Description |
| --- | --- |
| Sender | The guest partner that is configured in the receive settings, or the host partner that is configured in the send settings for an AS2 agreement. |
| Receiver | The host partner that is configured in the receive settings, or the guest partner that is configured in the send settings for an AS2 agreement. |
| Logic App | Logic App where the AS2 actions are configured. |
| Status | AS2 message status. Success = Received or sent a good AS2 message, no MDN configured; Success = Received or sent a good AS2 message, MDN configured and received or sent MDN; Failed = Received a bad AS2 message, no MDN configured; Pending = Received or sent a good AS2 message, MDN configured and expecting a functional ack. |
| Ack | MDN message status. |
| Direction | AS2 message direction. |
| Correlation ID | ID to correlate all the triggers and actions within a Logic App. |
| Message ID |  AS2 message ID, from the headers of the AS2 message. |
| Timestamp | Time at which AS2 action processes the message. |

### X12 message list property descriptions

| Property | Description |
| --- | --- |
| Sender | The guest partner configured in the receive settings or the host partner configured in the send settings for an AS2 agreement. |
| Receiver | The host partner configured in the receive settings or the guest partner configured in the send settings for an AS2 agreement. |
| Logic App | Logic App where the AS2 actions are configured. |
| Status | X12 message status. Success = Received or sent a good X12 message, no functional ack configured; Success = Received or sent a good X12 message, functional ack configured and received or sent a functional ack; Failed = Received or sent a bad X12 message; Pending: Received or sent a good X12 message, functional ack configured and expecting a functional ack. |
| Ack | Functional Ack (997) status.  Accepted = Received or sent a positive functional ack; Rejected = Received or sent a negative functional ack; Pending = Expecting a functional ack but didn't receive it; Pending = Generated a functional ack but couldn't send it to partner. |
| Direction | X12 message direction. |
| Correlation ID | ID to correlate all the triggers and actions within a Logic App. |
| Msg type |  EDI X12 message type. |
| ICN | Interchange Control Number of the X12 message. |
| TSCN | Transactional Set Control Number of the X12 message. |
| Timestamp | Time at which X12 action process the message. |

## Queries in Operations Management Suite portal

On the search page, you can create a query, and then when you search, you can filter the results by using facet controls.

### Creating a query

1. In the log search, write a query and select **Save**. To write a query, see  [Track B2B messages in the Operations Management Suite portal by using a query](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md).
![Select home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. **Save Search** appears.  Add a **name** and **category**, and then select **Save**.   
![Select home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. To view the query, select **favorites**    
![Select home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Select home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### Using a saved query

* In the log search, select **favorites** to view saved queries.  To view query results, select a query.
![Select home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## Next steps
[Custom Tracking Schema](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[AS2 Tracking Schema](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[X12 Tracking Schema](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Learn more about the Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")
