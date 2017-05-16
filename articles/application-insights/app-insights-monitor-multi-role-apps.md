---
title: Azure Application Insights support for multiple roles, microservices, and containers | Microsoft Docs
description: Monitoring apps that consist of multiple components or roles for performance and usage.
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: carmonm

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: cfreeman

---
# Monitor multi-role applications with Application Insights (preview)

You can monitor apps that consist of multiple components, roles, or services with [Azure Application Insights](app-insights-overview.md). The health of the roles and the relationships between them are displayed on a single Application Map. You can trace individual operations through multiple roles with automatic HTTP correlation. Container diagnostics can be integrated and correlated with application telemetry. Use a single Application Insights resource for all the roles of your application. 

![Multi-role application map](./media/app-insights-monitor-multi-role-apps/app-map.png)

We use 'role' here in a broad sense to mean any application component or service that is built as a separate project. For example, a typical business application may consist of multiple roles that communicate through a REST API. Roles can be hosted in containers such as Docker or Service Fabric, or on cloud or on-premises hosts. 

### Sharing a single Application Insights resource 

The key technique here is to send telemetry from every role in your application to the same Application Insights resource, but use the `cloud_RoleName` property to distinguish roles when necessary. 

In some cases, this may not be appropriate, and you may prefer to use separate resources for different groups of roles. For example, you might need to use different resources for management or billing purposes. Using separate resources means that you don't see all the roles displayed on a single Application Map; and that you can't query across roles in [Analytics](app-insights-analytics.md). You also have to set up the separate resources.

With that caveat, we'll assume in the rest of this document that you want to send data from multiple roles to one Application Insights resource.

## Configure multi-role applications

To get a multi-role application map, you need to achieve these goals:

* **Install the latest pre-release** Application Insights package in each role of the application. 
* **Share a single Application Insights resource** for all the roles of your application.
* **Enable Multi-role Application Map** in the Previews blade.

Configure each role of your application using the appropriate method for its type. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md).)

### 1. Install the latest pre-release package

Update or install the Appication Insights packages in the project for each role. If you're using Visual Studio:

1. Right-click a project and select **Manage NuGet Packages**. 
2. Select **Include prerelease**.
3. If Application Insights packages appear in Updates, select them. 

    Otherwise, Browse for and install the appropriate package:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - for roles running as guest executables and Docker containers running a in Service Fabric application
    * Microsoft.ApplicationInsights.ServiceFabric.Native - for reliable services in ServiceFabric applications
    * Microsoft.ApplicationInsights.Kubernetes for roles running in Docker on Kubernetes

### 2. Share a single Application Insights resource

* In Visual Studio, right-click a project and select **Configure Application Insights**, or **Application Insights > Configure**. For the first project, use the wizard to create an Application Insights resource. For subsequent projects, select the same resource.
* If there is no Application Insights menu, configure manually:

   1. In [Azure portal](https://portal,azure.com), open the Application Insights resource you already created for another role.
   2. In the Overview blade, open the Essentials drop-down tab, and copy the **Instrumentation Key.**
   3. In your project, open ApplicationInsights.config and insert: `<InstrumentationKey>your copied key</InstrumentationKey>`

![Copy the instrumentation key to the .config file](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### 3. Enable multi-role Application Map

In the Azure portal, open the resource for your application. In the Previews blade, enable *Multi-role Application Map*.

### 4. Enable Docker metrics (Optional) 

If a role runs in a Docker hosted on an Azure Windows VM, you can collect additional metrics from the container. Insert this in your [Azure diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md) configuration file:

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## Use cloud_RoleName to separate roles

The `cloud_RoleName` property is attached to all telemetry. It identifies the role or service that originates the telemetry. (It is not the same as cloud_RoleInstance, which separates identical roles that are running in parallel on multiple server processes or machines.)

In the portal, you can filter or segment your telemetry using this property. In this example, the Failures blade is filtered to show just information from the front-end web service, filtering out failures from the CRM API backend:

![Metric chart segmented by Cloud Role Name](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## Trace operations between roles

You can trace the calls from one service to another made while processing an individual operation.


![Show telemetry for operation](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Click through to a correlated list of telemetry for this operation across the front-end web server and the back-end API:

![Search across roles](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## Next steps

* [Separate telemetry from Development, Test, and Production](app-insights-separate-resources.md)
