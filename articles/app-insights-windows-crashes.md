<properties 
	pageTitle="Detect and diagnose crashes in Windows Store and Phone apps with Application Insights" 
	description="Analyze performance issues in your Windows device app with Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2015" 
	ms.author="awills"/>

# Detect and diagnose crashes in Windows Store and Phone apps with Application Insights

*Application Insights is in preview.*

If your users experience crashes in your app, you'd like to know about it quickly, and you'd like details about what happened. With Application Insights, you can monitor how frequently crashes occur, get alerts when they occur, and investigate reports of individual incidents.

"Crash" means that the application terminates because of an uncaught exception. If your app catches an exception you can report it with the TrackException() API but continue running. In that case, it will not be logged as a crash.


## Monitor crash frequency

If you haven't done this already, add [Application Insights to your app project][windows], and republish it. 

Crashes show up on your application's overview blade in [the Application Insights portal][portal].

![](./media/appinsights/appinsights-d018-oview.png)

You can edit the time range shown by the chart.


## Set an alert to detect crashes

![From the crashes chart, click Alert rules and then Add Alert](./media/appinsights/appinsights-d023-alert.png)

## Diagnose crashes

To find out if some versions of your app crash more than others, click through the crashes chart and then segment by Application Version:

![](./media/appinsights/appinsights-d26crashSegment.png)


To discover the exceptions that are causing crashes, open Diagnostic Search. You might want to remove other types of telemetry, to focus on the exceptions:

![](./media/appinsights/appinsights-d26crashExceptions.png)

[Learn more about filtering in Diagnostic Search][diagnostic].
 

Click any exception to see its details, including associated properties and stack trace.

![](./media/appinsights/appinsights-d26crash.png)

See the other exceptions and events that occurred close to that exception:


![](./media/appinsights/appinsights-d26crashRelated.png)

## Insert trace logs and events

To help diagnose problems, you can [insert trace calls and search the logs in Application Insights][diagnostic].

## <a name="debug"></a>Debug vs Release mode

#### Debug

If you build in debug mode, events are sent as soon as they are generated. If you lose internet connectivity and then exit the app before regaining connectivity, offline telemetry is discarded.

#### Release

If you build in release configuration, events are stored in the device and sent when the application resumes. Data is also sent on the application’s first use. If there is no internet connectivity upon startup, previous telemetry as well as telemetry for the current lifecycle is stored and sent on the next resume.

## <a name="next"></a>Next steps

[Detect, triage and diagnose issues with Application Insights][detect]

[Capture diagnostic logs][trace]

[Troubleshooting](app-insights-windows-troubleshoot.md)




<!--Link references-->

[alerts]: app-insightss-alerts.md
[android]: https://github.com/Microsoft/AppInsights-Android
[api]: app-insights-custom-events-metrics-api.md
[apiproperties]: app-insights-custom-events-metrics-api.md#properties
[apiref]: http://msdn.microsoft.com/library/azure/dn887942.aspx
[availability]: app-insights-monitor-web-app-availability.md
[azure]: insights-perf-analytics.md
[azure-availability]: insights-create-web-tests.md
[azure-usage]: insights-usage-analytics.md
[azurediagnostic]: insights-how-to-use-diagnostics.md
[client]: app-insights-web-track-usage.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[data]: app-insights-data-retention-privacy.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[exceptions]: app-insights-web-failures-exceptions.md
[export]: app-insights-export-telemetry.md
[exportcode]: app-insights-code-sample-export-telemetry-sql-database.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[javareqs]: app-insights-java-track-http-requests.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[older]: http://www.visualstudio.com/get-started/get-usage-data-vs
[perf]: app-insights-web-monitor-performance.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-role-based-access-control.md
[start]: app-insights-get-started.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md

