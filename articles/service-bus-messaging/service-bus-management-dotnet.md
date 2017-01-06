---
  title: Service Bus Management | Microsoft Docs
  description: Manage Service Bus namespaces and entities from .NET
  services: service-bus-messaging
  cloud: na
  documentationcenter: na
  author: jtaubensee
  manager: sethm

  ms.assetid:
  ms.service: service-bus-messaging
  ms.workload: na
  ms.tgt_pltfrm: na
  ms.devlang: dotnet
  ms.topic: article
  ms.date: 1/6/2016
  ms.author: jotaub

---

# Service Bus Management

The Service Bus management libraries provide functionality to dynamically provision Service Bus namespaces and entities. This allows for complex deployments and messaging scenarios, allowing users to programmatically determine what entities to provision. These libraries are currently available for .NET and PowerShell.

## Supported functionality

* Namespace creation, update, deletion
* Queue creation, update, deletion
* Topic creation, update, deletion
* Subscription creation, update, deletion

## Prerequisites

In order to get started using the Service Bus management libraries, you must authenticate with Azure Active Directory (AAD). AAD requires that you authenticate as a Service Principal which provides access to your Azure resources. For information on creating a Service Principal follow one of these articles:  

* [Use the Azure Portal to create Active Directory application and service principal that can access resources](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Use Azure PowerShell to create a service principal to access resources](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Use Azure CLI to create a service principal to access resources](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

The above tutorials will provide you with an `AppId` (Client ID), `TenantId`, and `ClientSecret` (Authentication Key), all of which will be used to authenticate by the management libraries. You must have 'Owner' permissions for the resource group that you wish to run on.

You will also need [.NET Core](http://www.microsoft.com/net/core) installed. 

## Programming pattern

The pattern to manipulate any Service Bus resource is similar and follows a common protocol:

1. Obtain a token from Azure Active Directory using the `Microsoft.IdentityModel.Clients.ActiveDirectory` library
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Create the `ServiceBusManagementClient` object
    ```csharp
    var creds = new TokenCredentials(token);
    var sbClient = new ServiceBusManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Set the CreateOrUpdate parameters to your specified values
    ```csharp
    var queueParams = new QueueCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"],
        EnablePartitioning = true
    };
    ```

1. Execute the call
    ```csharp
    await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
    ```

## Next steps
* [.NET Management sample](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Microsoft.Azure.Management.ServiceBus Reference](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus/) 

[Create Namespace]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.servicebus.namespacesoperationsextensions#Microsoft_Azure_Management_ServiceBus_NamespacesOperationsExtensions_BeginCreateOrUpdateAsync_Microsoft_Azure_Management_ServiceBus_INamespacesOperations_System_String_System_String_Microsoft_Azure_Management_ServiceBus_Models_NamespaceCreateOrUpdateParameters_System_Threading_CancellationToken_
[Create Queue]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.servicebus.queuesoperationsextensions#Microsoft_Azure_Management_ServiceBus_QueuesOperationsExtensions_CreateOrUpdateAsync_Microsoft_Azure_Management_ServiceBus_IQueuesOperations_System_String_System_String_System_String_Microsoft_Azure_Management_ServiceBus_Models_QueueCreateOrUpdateParameters_System_Threading_CancellationToken_
[Create Topic]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.servicebus.itopicsoperations#Microsoft_Azure_Management_ServiceBus_ITopicsOperations_CreateOrUpdateWithHttpMessagesAsync_System_String_System_String_System_String_Microsoft_Azure_Management_ServiceBus_Models_TopicCreateOrUpdateParameters_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_
[Create Subscription]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.servicebus.isubscriptionsoperations#Microsoft_Azure_Management_ServiceBus_ISubscriptionsOperations_CreateOrUpdateWithHttpMessagesAsync_System_String_System_String_System_String_System_String_Microsoft_Azure_Management_ServiceBus_Models_SubscriptionCreateOrUpdateParameters_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_
[Delete Namespace]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.servicebus.namespacesoperationsextensions#Microsoft_Azure_Management_ServiceBus_NamespacesOperationsExtensions_DeleteAsync_Microsoft_Azure_Management_ServiceBus_INamespacesOperations_System_String_System_String_System_Threading_CancellationToken_
[Delete Queue]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.servicebus.queuesoperationsextensions#Microsoft_Azure_Management_ServiceBus_QueuesOperationsExtensions_DeleteAsync_Microsoft_Azure_Management_ServiceBus_IQueuesOperations_System_String_System_String_System_String_System_Threading_CancellationToken_
[Delete Topic]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.servicebus.itopicsoperations#Microsoft_Azure_Management_ServiceBus_ITopicsOperations_DeleteWithHttpMessagesAsync_System_String_System_String_System_String_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_
[Delete Subscription]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.servicebus.isubscriptionsoperations#Microsoft_Azure_Management_ServiceBus_ISubscriptionsOperations_DeleteWithHttpMessagesAsync_System_String_System_String_System_String_System_String_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_