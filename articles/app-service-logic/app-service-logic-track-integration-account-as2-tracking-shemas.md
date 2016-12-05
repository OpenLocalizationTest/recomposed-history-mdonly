---
title: AS2 tracking schemas | Microsoft Docs
description: Learn more about AS2 tracking schemas
author: padmavc
manager: erikre
editor: ''
services: logic-apps
documentationcenter: ''

ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc

---
# AS2 tracking schemas
You can use these AS2 tracking schemas in your Azure integration account to help you monitor business-to-business (B2B) transactions:

* AS2 message tracking schema
* AS2 MDN tracking schema

## AS2 message tracking schema
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| Property | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | AS2 message sender's partner name. (Optional) |
| receiverPartnerName | String | AS2 message receiver's partner name. (Optional) |
| as2To | String | AS2 message receiver’s name, from the headers of the AS2 message. (Mandatory) |
| as2From | String | AS2 message sender’s name, from the headers of the AS2 message. (Mandatory) |
| agreementName | String | Name of the AS2 agreement to which the messages are resolved. (Optional) |
| direction | String | Direction of the message flow, receive or send. (Mandatory) |
| messageId | String | AS2 message ID, from the headers of the AS2 message (Optional) |
| dispositionType |String | Message Disposition Notification (MDN) disposition type value. (Optional) |
| fileName | String | File name, from the header of the AS2 message. (Optional) |
| isMessageFailed |Boolean | Whether the AS2 message failed. (Mandatory) |
| isMessageSigned | Boolean | Whether the AS2 message was signed. (Mandatory) |
| isMessageEncrypted | Boolean | Whether the AS2 message was encrypted. (Mandatory) |
| isMessageCompressed |Boolean | Whether the AS2 message was compressed. (Mandatory) |
| correlationMessageId | String | AS2 message ID, to correlate messages with MDNs. (Optional) |
| incomingHeaders |Dictionary of JToken | Incoming AS2 message header details. (Optional) |
| outgoingHeaders |Dictionary of JToken | Outgoing AS2 message header details. (Optional) |
| isNrrEnabled | Boolean | Use default value if the value is not known. (Mandatory) |
| isMdnExpected | Boolean | Use default value if the value is not known. (Mandatory) |
| mdnType | Enum | Allowed values are NotConfigured, Sync, and Async. (Mandatory) |

## AS2 MDN tracking schema
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": {
                },
                "outgoingHeaders": {
                }
            }
    }
````

| Property | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | AS2 message sender's partner name. (Optional) |
| receiverPartnerName | String | AS2 message receiver's partner name. (Optional) |
| as2To | String | Partner name who receives the AS2 message. (Mandatory) |
| as2From | String | Partner name who sends the AS2 message. (Mandatory) |
| agreementName | String | Name of the AS2 agreement to which the messages are resolved. (Optional) |
| direction |String | Direction of the message flow, receive or send. (Mandatory) |
| messageId | String | AS2 message ID. (Optional) |
| originalMessageId |String | AS2 original message ID. (Optional) |
| dispositionType | String | MDN disposition type value. (Optional) |
| isMessageFailed |Boolean | Whether the AS2 message failed. (Mandatory) |
| isMessageSigned |Boolean | Whether the AS2 message was signed. (Mandatory) |
| isNrrEnabled | Boolean | Use default value if the value is not known. (Mandatory) |
| statusCode | Enum | Allowed values are **Accepted**, **Rejected**, and **AcceptedWithErrors**. (Mandatory) |
| micVerificationStatus | Enum | Allowed values are **NotApplicable**, **Succeeded**, and **Failed**. (Mandatory) |
| correlationMessageId | String | Correlation ID. The original messaged ID (the message ID of the message for which MDN is configured). (Optional) |
| incomingHeaders | Dictionary of JToken | Indicates incoming message header details. (Optional) |
| outgoingHeaders |Dictionary of JToken | Indicates outgoing message header details. (Optional) |

## Next steps
* Learn more about the [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md).    
* Learn more about [monitoring B2B messages](app-service-logic-monitor-b2b-message.md).   
* Learn more about [B2B custom tracking schemas](app-service-logic-track-integration-account-custom-tracking-shema.md).   
* Learn more about [X12 tracking schemas](app-service-logic-track-integration-account-x12-tracking-shemas.md).   
* Learn about [tracking B2B messages in the Operations Management Suite portal](app-service-logic-track-b2b-messages-omsportal.md).
