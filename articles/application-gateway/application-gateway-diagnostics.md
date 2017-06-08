---
title: Monitor access, performance logs, back-end health, and metrics for Application Gateway | Microsoft Docs
description: Learn how to enable and manage access and performance logs for Application Gateway
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: tysonn
tags: azure-resource-manager

ms.assetid: 300628b8-8e3d-40ab-b294-3ecc5e48ef98
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: amitsriva

---
# Back-end health, diagnostic logging, and metrics for Application Gateway

By using Azure Application Gateway, you can monitor resources in the following ways:

[**Back-end health**](#back-end-health): Application Gateway provides the capability to monitor the health of the servers in the back-end pools through the Azure portal and through PowerShell. You can also find the health of the back-end pools through the performance diagnostic logs.

[**Logging**](#diagnostic-logging): Logging allows for performance, access, and other logs to be saved or consumed from a resource for monitoring purposes.

[**Metrics**](#metrics): Application Gateway currently has one metric. This metric measures the throughput of the application gateway in bytes per second.

## Back-end health

Application Gateway provides the capability to monitor the health of individual members of the back-end pools through the portal, PowerShell, and the command-line interface (CLI). You can also find an aggregated health summary of back-end pools through the performance diagnostic logs. 

The back-end health report reflects the output of the Application Gateway health probe to the back-end instances. When probing is successful and the back end can receive traffic, it's considered healthy. Otherwise, it's considered unhealthy.

> [!IMPORTANT]
> If there is a network security group (NSG) on an Application Gateway subnet, open port ranges 65503-65534 on the Application Gateway subnet for inbound traffic. These ports are required for the back-end health API to work.


### View back-end health through the portal

In the portal, back-end health is provided automatically. In an existing application gateway, select **Monitoring** > **Backend health**. 

Each member in the back-end pool is listed on this page (whether it's a NIC, IP, or FQDN). Back-end pool name, port, back-end HTTP settings name, and health status are shown. Valid values for health status are **Healthy**, **Unhealthy**, and ***Unknown**.

> [!NOTE]
> If you see a back-end health status of **Unknown**, ensure that the access to back end is not blocked by an NSG rule, a user-defined route (UDR), or a custom DNS in the virtual network.

![Back-end health][10]

### View back-end health through PowerShell

The following PowerShell code shows how to view back-end health by using the `Get-AzureRmApplicationGatewayBackendHealth` cmdlet:

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

The following snippet shows an example of the response:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## Diagnostic logging

You can use different types of logs in Azure to manage and troubleshoot application gateways. You can access some of these logs through the portal. All logs can be extracted from an Azure Blob storage and viewed in different tools, such as [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel, and Power BI. You can learn more about the different types of logs from the following list:

* **Activity log**: You can use [Azure Activity Log](../monitoring-and-diagnostics/insights-debugging-with-events.md) (formerly known as operational log and audit log) to view all operations that are submitted to your Azure subscription, and their status. Activity Log entries are collected by default, and you can view them in the Azure portal.
* **Access log**: You can use this log to view Application Gateway access patterns and analyze important information, including caller's IP, URL requested, response latency, return code, and bytes in and out. An access log is collected every 300 seconds. This log contains one record per instance of Application Gateway. The Application Gateway instance can be identified by the instanceId property.
* **Performance log**: You can use this log to view how Application Gateway instances are performing. This log captures performance information on per-instance basis, including total requests served, throughput in bytes, total requests served, failed request count, and healthy and unhealthy back-end instance count. A performance log is collected every 60 seconds.
* **Firewall log**: You can use this log to view the requests that are logged through either detection or prevention mode of an application gateway that is configured with the web application firewall.

> [!NOTE]
> Logs are available only for resources deployed in the Azure Resource Manager deployment model. You cannot use logs for resources in the classic deployment model. For a better understanding of the two models, see the [Understanding Resource Manager deployment and classic deployment](../azure-resource-manager/resource-manager-deployment-model.md) article.

There are three different options to choose for storing your logs:

* Storage account: Storage accounts are best used for logs when logs are stored for a longer duration and reviewed when needed.
* Event hubs: Event hubs are a great option for integrating with other security information and event management (SEIM) tools to get alerts on your resources.
* Log Analytics: Log Analytics is best used for general real-time monitoring of your application or looking at trends.

### Enable logging through PowerShell

Activity logging is automatically enabled for every Resource Manager resource. You must enable access and performance logging to start collecting the data available through those logs. To enable logging, use the following steps:

1. Note your storage account's resource ID, where the log data is stored. This value is of the form: /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>. You can use any storage account in your subscription. You can use the preview portal to find this information.

    ![Preview portal: Application Gateway Diagnostics](./media/application-gateway-diagnostics/diagnostics1.png)

2. Note your application gateway's resource ID for which logging will be enabled. This value is of the form: /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/applicationGateways/\<application gateway name\>. You can use the preview portal to find this information.

    ![Preview portal: Application Gateway Diagnostics](./media/application-gateway-diagnostics/diagnostics2.png)

3. Enable diagnostic logging using the following powershell cmdlet:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Activity logs do not require a separate storage account. The use of storage for access and performance logging incurs service charges.

### Enable logging through the Azure portal

1. In the Azure portal, find your resource and click **Diagnostic logs**.

   For Application Gateway, three logs are available:

   * Access log
   * Performance log
   * Firewall log

2. To start collecting data, click **Turn on diagnostics**.

   ![diagnostics setting blade][1]

3. The **Diagnostics settings** blade provides the settings for the diagnostic logs. In this example, Log Analytics is used to store the logs. Click **Configure** under **Log Analytics** to configure your workspace. You can also use event hubs and a storage account to save the diagnostic logs.

   ![diagnostics blade][2]

4. Choose an existing OMS Workspace or create a new one. This example uses an existing one.

   ![oms workspaces][3]

5. Confirm the settings and click **Save**.

   ![confirm selection][4]

### Activity log

Azure generates the activity log by default. The logs are preserved for 90 days in Azure’s Event Logs store. Learn more about these logs by reading the [View events and activity log](../monitoring-and-diagnostics/insights-debugging-with-events.md) article.

### Access log

This log is only generated if you've enabled it on each Application Gateway instance, as detailed in the preceding steps. The data is stored in the storage account you specified when you enabled the logging. Each access of Application Gateway is logged in JSON format, as seen in the following example:


|Value  |Description  |
|---------|---------|
|instanceId     | Application Gateway instance that served the request.        |
|clientIP     | Originating IP for the request.        |
|clientPort     | Originating port for the request.       |
|httpMethod     | The HTTP method used by the request.       |
|requestUri     | URI of the request received.        |
|RequestQuery     | **Server-Routed**: Back-end pool instance that was sent the request </br> **X-AzureApplicationGateway-LOG-ID**: Correlation ID used for the request, can be used to troubleshoot traffic issues on the back-end servers. </br>**SERVER-STATUS**: The HTTP response code Application Gateway received from the back end.       |
|UserAgent     | User-agent from the HTTP request header.        |
|httpStatus     | HTTP status code returned to the client from Application Gateway.       |
|httpVersion     | HTTP version of the request.        |
|receivedBytes     | Size of packet received in bytes.        |
| sentBytes|Size of packet sent in bytes.|
|timeTaken|The length of time (in milliseconds) that it takes for a request to be processed and its response to be sent. This is calculated as time interval from when Application Gateway receives the first byte of an HTTP request, to the time when response send operation completes. It is important to note that the Time-Taken field usually includes the time that the request and response packets are traveling over the network. |
|sslEnabled|Whether communication to the back-end pools used SSL. Valid values are on or off.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off"
    }
}
```

### Performance log

This log is only generated if you have enabled it on each Application Gateway instance, as detailed in the preceding steps. The data is stored in the storage account you specified when you enabled the logging. The performance log data is generated on 1 minute intervals. The following data is logged:


|Value  |Description  |
|---------|---------|
|instanceId     |  Application Gateway instance for which performance data is being generated. For a multi-instance application gateway, there is 1 row per instance.        |
|healthyHostCount     | Number of healthy hosts in the back-end pool        |
|unHealthyHostCount     | Number of unhealthy hosts in the back-end pool.        |
|requestCount     | Number of requests served.        |
|latency | Latency (in milliseconds) of requests from the instance to back end serving the requests. |
|failedRequestCount| Number of failed requests.|
|throughput|Average throughput since the last log measured in bytes per second.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Latency is calculated from the time first byte of the HTTP request is received to the time the last byte of the HTTP response is sent. It is sum of Application Gateway processing time plus the network cost to the back end, plus the time taken by the back end to process request.

### Firewall log

This log is only generated if you have enabled it on a per application gateway basis as detailed in the preceding steps. This log also requires that web application firewall is configured on an application gateway. The data is stored in the storage account you specified when you enabled the logging. The following data is logged:


|Value  |Description  |
|---------|---------|
|instanceId     | Application Gateway instance for which firewall data is being generated. For a multi-instance application gateway, there is 1 row per instance.         |
|clientIp     |   Originating IP for the request.      |
|clientPort     |  Originating port for the request.       |
|requestUri     | URL of the request received.       |
|ruleSetType     | Rule set type. Available values: OWASP.        |
|ruleSetVersion     | Rule set version used. Available values are 2.2.9 or 3.0.     |
|ruleId     | Rule ID of the triggering event.        |
|message     | User friendly message for the triggering event. More details are provided in the details section.        |
|action     |  Action taken on request Available values are Blocked or Allowed.      |
|site     | The site for which the log was generated. Currently only Global is listed since rules are global.|
|details     | Details of the triggering event.        |
|details.message     | Description of the rule.        |
|details.data     | Specific data found in request that matched the rule.         |
|details.file     | The configuration file that contained the rule.        |
|details.line     | The line number in the configuration file that triggered the event.       |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

### View and analyze the activity log

You can view and analyze activity log data using any of the following methods:

* **Azure tools**: Retrieve information from the activity log through Azure PowerShell, the Azure CLI, the Azure REST API, or the Azure preview portal.  Step-by-step instructions for each method are detailed in the [Activity operations with Resource Manager](../azure-resource-manager/resource-group-audit.md) article.
* **Power BI**: If you don't already have a [Power BI](https://powerbi.microsoft.com/pricing) account, you can try it for free. Using the [Azure Activity Logs content pack for Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) you can analyze your data with pre-configured dashboards that you can use as-is, or customize.

### View and analyze the access, performance, and firewall log

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) can collect the counter and event log files from your Blob storage account and includes visualizations and powerful search capabilities to analyze your logs.

You can also connect to your storage account and retrieve the JSON log entries for access and performance logs. Once you download the JSON files, you can convert them to CSV and view in Excel, PowerBI, or any other data visualization tool.

> [!TIP]
> If you are familiar with Visual Studio and basic concepts of changing values for constants and variables in C#, you can use the [log converter tools](https://github.com/Azure-Samples/networking-dotnet-log-converter) available from GitHub.
> 
> 

## Metrics

Metrics is a feature for certain Azure resources where you can view performance counters in the portal. For Application Gateway, one metric is available at the time of writing this article. This metric is throughput, and can be seen in the portal. Navigate to an application gateway and click **Metrics**. To view the values, select throughput in the **Available metrics** section. In the following image, you can see an example with the filters that can be used to display the data in different time ranges.

To see a list of the current support metrics, visit [Supported metrics with Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![metric view][5]

### Alert rules

Alert rules can be started based of on metrics on a resource. This means for Application Gateway, an alert can call a webhook or email an administrator if the throughput of the application gateway is above, below or at a threshold for a specified period of time.

The following example will walk you through creating an alert rule that sends an email to an administrator after a throughput threshold has been breached.

1. Click **Add metric alert**. You can also reach this blade from the metrics blade.

   ![alert rules blade][6]

2. On the **Add rule** blade, fill out the name, condition, and notify sections, and click **OK**.

   * The **Condition** selector allows for 4 values, **Greater than**, **Greater than or equal**, **Less than**, or **Less than or equal to**.

   * The **Period** selector allows for picking of a period from 5 minutes to 6 hours.

   * By selecting **Email owners, contributors, and readers**, the email can be dynamic based on the users that have access to that resource. Otherwise a comma separated list of users can be provided in the **Additional administrator email(s)** textbox.

   ![add rule blade][7]

If the threshold is breached, an email arrives similar to the one in the following image:

![threshold breached email][8]

A list of alerts is shown once a metric alert has been created and provides an overview of all the alert rules.

![alert rule view][9]

To learn more about alert notifications, visit [Receive alert notifications](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

To understand more about webhooks and how you can use them with alerts, visit [Configure a webhook on an Azure metric alert](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## Next steps

* Visualize counter and event logs with [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).
* [Visualize your Azure Activity Log with Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blog post.
* [View and analyze Azure Activity Log in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blog post.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png