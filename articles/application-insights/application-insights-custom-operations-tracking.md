---
title: Track custom operations with Azure Application Insights .NET SDK | Microsoft Docs
description: Tracking custom operations with Azure Application Insights .NET SDK
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm

ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/31/2017
ms.author: sergkanz

---

# Track custom operations with Application Insights .NET SDK

Azure Application Insights SDKs automatically track incoming HTTP requests and calls to dependent services, such as HTTP requests and SQL queries. Tracking and correlation of requests and dependencies give you visibility into the whole application's responsiveness and reliability across all micro-services. We're going to gradually expand the list and give an automatic collection of other well-known platforms and frameworks. 

One class of application patterns can't be supported generically. Proper monitoring of such patterns requires manual code instrumentation. This article covers a few patterns that might require manual instrumentation, such as custom queue processing and running long-running background tasks.

This document provides guidance on how to track custom operations with the Application Insights SDK. This documentation is relevant for:

- Application Insights for .NET (aka Base SDK) version `2.4+`.
- Application Insights for Web Applications (running ASP.NET) version `2.4+`.
- Application Insights for AspNetCore version `2.1+`.

## Overview
An operation is a logical piece of work run by an application. It has a name, start time, duration, and a context of execution like user name, properties, and result. If operation `A` was initiated by operation `B`, then operation `B` is set as a parent for `A`.  An operation can have only one parent, but it can have many children operations. For more information on operations and telemetry correlation, see [Azure Application Insights telemetry correlation](application-insights-correlation.md).

In the Application Insights .NET SDK, the operation is described by the abstract class [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) and its descendants [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) and [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs).

## Incoming operations tracking 
The Application Insights web SDK automatically collects HTTP requests for ASP.NET applications that run in an IIS pipeline and all ASP.NET Core applications. There are community-supported solutions for other platforms and frameworks. However, if the application isn't supported by any of the standard or community-supported solutions, you can instrument it manually.

Another example that requires custom tracking is the worker that receives items from the queue. For some queues, the call to add a message to this queue is tracked as a dependency. However, the high-level operation that describes message processing is not automatically collected.

Let's see how we can track such operations.

On a high level, the task is to create `RequestTelemetry` and set known properties. After the operation is finished, you track the telemetry. The following example demonstrates this task.

### HTTP request in Owin self-hosted app
In this example, we follow the [HTTP Correlation Protocol](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). You should expect to receive headers that are described there:

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // if there is Request-Id recevied from the upstream service, set telemetry context accordingly
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation id from the Request-Id (if you follow the 'HTTP Protocol for Correlation')
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows to correlate 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry item
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // process request
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // update status code and success as appropriate
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // now it's time to stop operation (and track telemetry)
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // returns root Id from the '|' to first '.' if any
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

The HTTP Correlation Protocol also declares the `Correlation-Context` header. However, it's omitted for simplicity.

## Queue instrumentation
For HTTP communication, we've created a protocol to pass correlation details. Some queues' protocols allow you to pass additional metadata along with the message, and others do not.

### Service Bus Queue
The [Service Bus Queue](../service-bus-messaging/index.md) allows you to pass a property bag along with the message. We use it to pass the correlation ID.

The Service Bus Queue uses TCP-based protocols. Application Insights doesn't automatically track queue operations, so we track them manually. The dequeue operation is a push-style API, and we're unable to track it.

#### Enqueue

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes telemetry item
    // and allows to correlate this operation with its parent and children
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus Queue allows the property bag to pass along with the message
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### Process
```C#
public async Task Process(BrokeredMessage message)
{
    // Once the message is taken from the queue, create ReqeustTelemetry to track its processing
    // It may also make sense to get the name from the message
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation id from the Request-Id (if you follow the 'HTTP Protocol for Correlation')
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

### Azure Storage Queue
The following example shows how to track the [Azure Storage Queue](../storage/storage-dotnet-how-to-use-queues.md) operations and correlate telemetry between the producer, the consumer, and Azure Storage. 

The Azure Storage Queue has an HTTP API. All calls to the queue are tracked by the ApplicationInsights DependencyCollector for HTTP requests.
Make sure you have `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` in the `applicationInsights.config`. Add it programmatically as described in [Filtering and Preprocessing in the Azure Application Insights SDK](app-insights-api-filtering-sampling.md).

If you configure ApplicationInsights manually, make sure you create and initialize `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` similarly to:
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add an Azure Storage endpoint, otherwise you might experience request signature validation issues on the Storage service side
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown
```

You also might want to correlate the Application Insights operation ID with the Azure Storage RequestId. For information on how to set and get a storage request client and a server request ID, see [Monitor, diagnose and troubleshoot Azure Storage](../storage/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### Enqueue
Because Azure Storage Queues support the HTTP API, all operations with the queue are automatically tracked by Application Insights. In many cases, this instrumentation should be enough. However, to correlate traces on the consumer side with producer traces, you must pass some correlation context similar to how we do it in the HTTP Protocol for Correlation. 

In this example, we track the optional `Enqueue` operation. It allows you to

 - **Correlate retries (if any)**: They all have one common parent that's the `Enqueue` operation. Otherwise, they're tracked as children of the incoming request. If there are multiple logical requests to the queue, it might be difficult to find which call resulted in retries.
 - **Correlate Azure Storage logs (if and when needed)**: They're correlated with Application Insights telemetry.

The `Enqueue` operation is the child of a parent operation (for example, an incoming HTTP request). The HTTP dependency call is the child of the `Enqueue` operation and the grandchild of the incoming request:

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload
    // e.g. if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Azure Storage logs and ApplciationInsights telemetry
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}  
```

To reduce the amount of telemetry your application reports or if you don't want to track the `Enqueue` operation for other reasons, use the `Activity` API directly:

- Create (and start) a new `Activity` instead of starting the Application Insights operation. You do *not* need to assign any properties on it except the operation name.
- Serialize `yourActivity.Id` into the message payload instead of `operation.Telemetry.Id`. You can also use `Activity.Current.Id`.


#### Dequeue
Similar to `Enqueue`, an actual HTTP request to the Azure Storage Queue is automatically tracked by Application Insights. However, the `Enqueue` operation presumably happens in the parent context, such as an incoming request context. Application Insights SDKs automatically correlate such an operation (and its HTTP part) with the parent request and other telemetry reported in the same scope.

The `Dequeue` operation is tricky. The Application Insights SDK automatically tracks HTTP requests. However, it doesn't know the correlation context until the message is parsed. It's not possible to correlate the HTTP request to get the message with the rest of the telemetry.

In many cases, it might be useful to correlate the HTTP request to the queue with other traces as well. The following example demonstrates how to do it:

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing
            // However, we will only know what correlation Id to use AFTER we get it from the message
            // So we will report telemetry once we know the Ids.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // delete message
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### Process

In the following example, we trace an incoming message in a manner similar to how we trace an incoming HTTP request:

```C#
public async Task Process(MessagePayload message)
{
    // Once the message is dequeued from the queue, create ReqeustTelemetry to track its processing
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

Similarly, other queue operations can be instrumented. A peek operation should be instrumented in a similar way as a dequeue operation. Instrumenting queue management operations isn't necessary. Application Insights tracks operations such as HTTP, and in most cases, it's enough.

When instrumenting message deletion, make sure you set the operation (correlation) identifiers. Alternatively, you can use the `Activity` API. Then you don't need to set operation identifiers on the telemetry items because Application Insights does it for you:

- Create a new `Activity` after you've got an item from the queue.
- Use `Activity.SetParentId(message.ParentId)` to correlate consumer and producer logs.
- Start the `Activity`.
- Track dequeue, process, and delete operations by using `Start/StopOperation` helpers. Do it from the same asynchronous control flow (execution context). In this way, they're correlated properly.
- Stop the `Activity`.
- Use `Start/StopOperation`, or call Track telemetry manually.

### Batch processing
Some queues allow you to dequeue multiple messages with one request. Processing such messages is presumably independent and belongs to the different logical operations. In this case, it's not possible to correlate the `Dequeue` operation to particular message processing.

Each message should be processed in its own asynchronous control flow. For more information, see the [Outgoing dependencies tracking](#outgoing-dependencies-tracking) section.

## Long-running background tasks
Some applications start long-running operations that might be caused by user requests. From the tracing/instrumentation perspective, it's not different from request or dependency instrumentation: 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // process task
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // update status code and success as appropriate
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // update status code and success as appropriate
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

In this example, we use `telemetryClient.StartOperation` to create `RequestTelemetry` and fill the correlation context. Let's say you have a parent operation that was created by incoming requests that scheduled the operation. As long as `BackgroundTask` starts in the same asynchronous control flow as an incoming request, it's correlated with that parent operation. `BackgroundTask` and all nested telemetry items are automatically correlated with the request that caused it even after the request ends.

When the task starts from the background thread that doesn't have any operation (Activity) associated with it, `BackgroundTask` doesn't have any parent. However, it can have nested operations. All telemetry items reported from the task are correlated to the `RequestTelemetry` created in the `BackgroundTask`.

## Outgoing dependencies tracking
You can track your own dependency kind or an operation that's not supported by Application Insights.

The `Enqueue` method in the Service Bus Queue or the Azure Storage Queue can serve as examples for such custom tracking.

The general approach for custom dependency tracking is to:

- Call the `TelemetryClient.StartOperation` (extension) method that fills the `DependencyTelemetry` properties that are needed for correlation and some other properties (start timestamp, duration).
- Set other custom properties on the `DependencyTelemetry`, such as the name and any other context you need.
- Make a dependency call and wait for it.
- Stop the operation with `StopOperation` when it's finished.
- Handle exceptions.

`StopOperation` only stops the operation that was started. If the current running operation doesn't match the one you want to stop, StopOperation does nothing. This situation might happen if you start multiple operations in parallel in the same execution context:

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// this will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Make sure you always call `StartOperation` and run your task in its own context:
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // update status code and success as appropriate
    }
    catch(...) 
    {
        // update status code and success as appropriate
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## Next steps

- Learn the basics of [telemetry correlation](application-insights-correlation.md) in Application Insights.
- See the [data model](application-insights-data-model.md) for Application Insights types and data models.
- Report custom [events and metrics](app-insights-api-custom-events-metrics.md) to Application Insights.
- Check out standard context properties collection [configuration](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
- Check the [System.Diagnostics.Activity User Guide](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) to see how we correlate telemetry.
