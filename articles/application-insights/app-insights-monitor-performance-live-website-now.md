---
title: Diagnose performance issues on a running IIS website | Microsoft Docs
description: Monitor a website's performance without re-deploying it. Use standalone or with Application Insights SDK to get dependency telemetry.
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm

ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/03/2017
ms.author: awills

---
# Instrument web apps at runtime with Application Insights


You can instrument a live web app with Azure Application Insights, without having to modify or redeploy your code. If your apps are hosted by an on-premises IIS server, you install Status Monitor; or if they're Azure web apps or run in an Azure VM, you can install the Application Insights extension. (There are also separate articles about instrumenting [live J2EE web apps](app-insights-java-live.md) and [Azure Cloud Services](app-insights-cloudservices.md).)

![sample charts](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

You have a choice of three routes to apply Application Insights to your .NET web applications:

* **Build time:** [Add the Application Insights SDK][greenbrown] to your web app code.
* **Run time:** Instrument your web app on the server, as described below, without rebuilding and redeploying the code.
* **Both:** Build the SDK into your web app code, and also apply the run-time extensions. Get the best of both options.

Here's a summary of what you get by each route:

|  | Build time | Run time |
| --- | --- | --- |
| Requests & exceptions |Yes |Yes |
| [More detailed exceptions](app-insights-asp-net-exceptions.md) | |Yes |
| [Dependency diagnostics](app-insights-asp-net-dependencies.md) |On .NET 4.6+, but less detail |Yes, full detail: result codes, SQL command text, HTTP  Verb|
| [System performance counters](app-insights-performance-counters.md) |Yes |Yes |
| [API for custom telemetry][api] |Yes | |
| [Trace log integration](app-insights-asp-net-trace-logs.md) |Yes | |
| [Page view & user data](app-insights-javascript.md) |Yes | |
| No need to rebuild code |No | |

## Instrument your web app at run time
You need a [Microsoft Azure](http://azure.com) subscription.

### If your app is an Azure web app

* Select Application Insights on the app's control panel in Azure.

    ![Set up Application Insights for an Azure web app](./media/app-insights-monitor-performance-live-website-now/azure-web-setup.png)
* When the Application Insights summary page opens, click the link at the bottom to open the full Application Insights resource.


    ![Click through to Application Insights](./media/app-insights-monitor-performance-live-website-now/azure-web-view-more.png)

[Learn more about monitoring Azure apps with Application Insights](app-insights-azure.md).

### If your app is hosted on your IIS server
1. On your IIS web server, sign in with administrator credentials.
2. If Application Insights Status Monitor is not already installed, download and run the [Status Monitor installer](http://go.microsoft.com/fwlink/?LinkId=506648).
3. In Status Monitor, select the installed web application or website that you want to monitor. Sign in with your Azure credentials.

    Configure the resource where you want to see the results in the Application Insights portal. (Normally, it's best to create a new resource. Select an existing resource if you already have [web tests][availability] or [client monitoring][client] for this app.) 

    ![Choose an app and a resource.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Restart IIS.

    ![Choose Restart at the top of the dialog.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Your web service will be interrupted for a short while.


## When you re-publish your app


When you enable Application Insights for your app, DLLs and ApplicationInsights.config are added to your app. 

>[!IMPORTANT]
>When you re-publish the app, re-enable Application Insights monitoring. This is necessary because the Application Insights DLLs and .config file may be deleted by the deployment process. 

Alternatively, consider [adding Application Insights to your app in Visual Studio][greenbrown] before you republish. This provides you with more detailed monitoring, Live Stream monitoring, and the ability to code your own telemetry.


## Troubleshooting
### Can't connect? No telemetry?

* You need to open [some outgoing ports](app-insights-ip-addresses.md#outgoing-ports) in your server's firewall to allow Status Monitor to work.

* Open Status Monitor and select your application on left pane. Check if there are any diagnostics messages for this application in the "Configuration notifications" section:

  ![Open the Performance blade to see request, response time, dependency and other data](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* On the server, if you see a message about "insufficient permissions", try the following:
  * In IIS Manager, select your application pool, open **Advanced Settings**, and under **Process Model** note the identity.
  * In Computer management control panel, add this identity to the Performance Monitor Users group.
* If you have MMA/SCOM installed on your server, some versions can conflict. Uninstall both SCOM and Status Monitor, and re-install the latest versions.
* See [Troubleshooting][qna].

## System Requirements
OS support for Application Insights Status Monitor on Server:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows server 2012 R2
* Windows Server 2016

with latest SP and .NET Framework 4.5

On the client side Windows 7, 8, 8.1 and 10, again with .NET Framework 4.5

IIS support is: IIS 7, 7.5, 8, 8.5
(IIS is required)

## Automation with PowerShell
You can start and stop monitoring by using PowerShell on your IIS server.

First import the Application Insights module:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Find out which apps are being monitored:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Optional) The name of a web app.
* Displays the Application Insights monitoring status for each web app (or the named app) in this IIS server.
* Returns `ApplicationInsightsApplication` for each app:

  * `SdkState==EnabledAfterDeployment`: App is being monitored, and was instrumented at run time, either by the Status Monitor tool, or by `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: The app is not instrumented for Application Insights. Either it was never instrumented, or run-time monitoring was disabled with the Status Monitor tool or with `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: The app was instrumented by adding the SDK to the source code. Its SDK cannot be updated or stopped.
  * `SdkVersion` shows the version in use for monitoring this app.
  * `LatestAvailableSdkVersion`shows the version currently available on the NuGet gallery. To upgrade the app to this version, use `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` The name of the app in IIS
* `-InstrumentationKey` The ikey of the Application Insights resource where you want the results to be displayed.
* This cmdlet only affects apps that are not already instrumented - that is, SdkState==NotInstrumented.

    The cmdlet does not affect an app that is already instrumented, either at build time by adding the SDK to the code, or at run time by a previous use of this cmdlet.

    The SDK version used to instrument the app is the version that was most recently downloaded to this server.

    To download the latest version, use Update-ApplicationInsightsVersion.
* Returns `ApplicationInsightsApplication` on success. If it fails, it logs a trace to stderr.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` The name of an app in IIS
* `-All` Stops monitoring all apps in this IIS server for which `SdkState==EnabledAfterDeployment`
* Stops monitoring the specified apps and removes instrumentation. It only works for apps that have been instrumented at run-time using the Status Monitoring tool or Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Returns ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: The name of a web app in IIS.
* `-InstrumentationKey` (Optional.) Use this to change the resource to which the app's telemetry is sent.
* This cmdlet:
  * Upgrades the named app to the version of the SDK most recently downloaded to this machine. (Only works if `SdkState==EnabledAfterDeployment`)
  * If you provide an instrumentation key, the named app is reconfigured to send telemetry to the resource with that key. (Works if `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Downloads the latest Application Insights SDK to the server.

## <a name="next"></a>Next steps

View your telemetry:

* [Explore metrics](app-insights-metrics-explorer.md) to monitor performance and usage
* [Search events and logs][diagnostic] to diagnose problems
* [Analytics](app-insights-analytics.md) for more advanced queries
* [Create dashboards](app-insights-dashboards.md)

Add more telemetry:

* [Create web tests][availability] to make sure your site stays live.
* [Add web client telemetry][usage] to see exceptions from web page code and to let you insert trace calls.
* [Add Application Insights SDK to your code][greenbrown] so that you can insert trace and log calls

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md
