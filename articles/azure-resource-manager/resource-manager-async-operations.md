---
title: Azure asynchronous operations | Microsoft Docs
description: Describes how to track asynchronous operations in Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/06/2017
ms.author: tomfitz

---
# Track asynchronous Azure operations
Some Azure REST operations run asynchronously because the operation cannot be completed quickly. This topic describes how to track the status of asynchronous operations through values returned in the response.  

## Status codes for asynchronous operations
An asynchronous operation intially returns an HTTP status code of either 201 (Created) or 202 (Accepted). When the operation successfully completes, it returns either 200 (OK) or 204 (No Content). Refer to the [REST API documentation](/rest/api/) to see the responses for the operation you are executing. 

## Monitor status of operation
The asynchronous REST operations return header values which you use to determine the current status of the operation. There are potentially three header values to examine:

* Azure-AsyncOperation - Returns the URL to use for checking the ongoing status of the operation.
* Location - Returns the URL to use for checking the ongoing status of the operation. 
* Retry-After - The number of seconds to wait before checking the status of the asynchronous operation.

However, not every asynchronous operation returns all of these values. For example, you may need to evaluate the **Azure-AsyncOperation** header value for one operation, and the **Location** header value for another operation. Again, refer to the [REST API documentation](/rest/api/) to determine which values are returned for your operation.

You retrieve the header values as you would retrieve any header value for a request. For example, in **C#**, you retrieve the header value from an **HttpWebResponse** object named **response** with the following code:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

The **Azure-AsyncOperation** or **Location** header value contain a URL you can use to determine the current status of the asynchronous operation. Send a GET request to that URL. If your operation returns both values, use the **Azure-AsyncOperation** value. The body of the response from this operation contains information about the operation.

## Example request and responses

### Start virtual machine (202 with Azure-AsyncOperation)
This example shows how to determine the status of **start** operation for virtual machines. The intial request is in the following format:

```
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

It returns status code 202. Amongst the header values, you see:

```
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

You check the ongoing status of the asynchronous operation by sending another request to that URL.

```
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

The body of that response contains:

```
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

You can use the status to determine when the operation completes.

### Deploy resources (201 with Azure-AsyncOperation)

This example shows how to determine the status of **deployments** operation for deploying resources to Azure. The intial request is in the following format:

```
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

It returns status code 201. The body of the response includes:

```
"provisioningState":"Accepted",
```

Amongst the header values, you see:

```
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

You check the ongoing status of the asynchronous operation by sending another request to that URL.

```
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

The body of the response contains:

```
{"status":"Running"}
```

When the deployment is finished, the response contains:

```
{"status":"Succeeded"}
```

### Create storage account (202 with Location and Retry-After)

This example shows how to determine the status of the **create** operation for storage accounts. The intial request is in the following format:

```
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

And the request body contains:

```
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

It returns status code 202. Amongst the header values, you see the following two values:

```
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

After waiting for number of seconds specified in Retry-After, you check the ongoing status of the asynchronous operation by sending another request to that URL.

```
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

If the request is still running, you receive a status code 202. If the request has completed, your receive a status code 200, and the body of the response contains the properties of the storage account that has been created.

