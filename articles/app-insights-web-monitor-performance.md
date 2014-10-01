<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Monitor performance in web applications

*Application Insights is in preview.*


Make sure your application is performing well, and find out quickly about any failures. [Application Insights][start] will tell you about any performance issues and exceptions, and help you find and diagnose the root causes.

Application Insights can monitor ASP.NET web applications hosted on-premise or on virtual machines, as well as Microsoft Azure websites. 

* [Set up performance monitoring](#setup)
* [See the data](#view)
* [What does it all mean?](#metrics)
* [Diagnosing issues](#diagnosis)
* [Next steps](#next)

## <a name="setup"></a>Set up performance monitoring

If you haven't yet added Application Insights to your project (that is, if it doesn't have ApplicationInsights.config), choose one of these ways to get started:

* [Add Application Insights to your app project in Visual Studio][start] - Recommended. As well as passive performance monitoring, you can insert diagnostic logging and track usage.
* [Monitor performance of a live website now][redfield] - This way, you don't need to update the application project or redeploy the website. 


## <a name="view"></a>View reports

Run your application with F5 and try it out - open different pages.

In Visual Studio, you'll see a count of the events that have been received.

![](./media/appinsights/appinsights-09eventcount.png)


Open Application Insights from your project.

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)


Look for data in the **Application health** tiles. At first, you'll just see one or two points. For example:

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

When you run in debug mode, telemetry is expedited through the pipeline, so that you should see data appearing within seconds. When you deploy your app, data accumulates more slowly.

If you don't see data at first, wait a minute and then click Refresh.

### Exploring metrics

Click any tile to see more detail, and to see results for a longer period. For example, click the Requests tile and then select a time range:


![Click through to more data and select a time range](./media/appinsights/appinsights-48metrics.png)

Click a graph to choose which metrics it displays:

![Click a graph to choose metrics](./media/appinsights/appinsights-61perfchoices.png)

> [AZURE.NOTE] **Uncheck all the metrics** to see the full selection that is available. The metrics fall into groups; when any member of a group is selected, only the other members of that group appear.


## <a name="metrics"></a>What does it all mean? Performance tiles and reports

There's a variety of performance metrics you can get. Let's start with those that appear by default on the application blade.


### Requests

The number of HTTP requests received in a specified period. Compare this with the results on other reports to see how your app behaves as the load varies.

HTTP requests include all GET or POST requests for pages, data and images.

Click on the tile to get counts for specific URLs.

### Average response time

Measures the time between a web request entering your application and the response being returned.

The points show a moving average. If there are a lot of requests, there might be some that deviate from the average without an obvious peak or dip in the graph.

Look for unusual peaks. In general, expect response time to rise with a rise in requests. If the rise is disproportionate, your app might be hitting a resource limit such as CPU or the capacity of a service it uses.

Click on the tile to get times for specific URLs.

![](./media/appinsights/appinsights-42reqs.png)


### Slowest requests

![](./media/appinsights/appinsights-44slowest.png)

Shows which requests might need performance tuning.


### Failed requests

![](./media/appinsights/appinsights-46failed.png)

A count of requests that threw uncaught exceptions.

Click the tile to see the details of specific failures, and select an individual request to see its detail. 

Only a representative sample of failures is retained for individual inspection.

### Other metrics

To see what other metrics you can display, click a graph, and then deselect all the metrics to see the full available set. Click (i) to see each metric's definition.

![Deselect all metrics to see the whole set](./media/appinsights/appinsights-62allchoices.png)


Selecting any metric will hide the others that can't appear on the same chart.


## <a name="diagnosis"></a>Diagnosing issues

Here are a few tips for finding and diagnosing performance issues:

* Set up [web tests][availability] to be alerted if your web site goes down or responds incorrectly or slowly. 
* Compare the Request count with other metrics to see if failures or slow response are related to load.
* [Insert and search trace statements][diagnostic] in your code to help pinpoint problems.

## <a name="next"></a>Next steps

[Web tests][availability] - Have web requests sent to your application at regular intervals from around the world.

[Capture and search diagnostic traces][diagnostic] - Insert trace calls and sift through the results to pinpoint issues.

[Usage tracking][usage] - Find out how people use your application.

[Troubleshooting][qna] - and Q & A

## Learn more
* [Application Insights - get started][start]
* [Monitor a live web server now][redfield]
* [Monitor performance in web applications][perf]
* [Search diagnostic logs][diagnostic]
* [Availability tracking with web tests][availability]
* [Track usage][usage]
* [Track custom events and metrics][track]
* [Q & A and troubleshooting][qna]

<!--Link references-->


[start]: ../app-insights-start-monitoring-app-health-usage/
[redfield]: ../app-insights-monitor-performance-live-website-now/
[perf]: ../app-insights-web-monitor-performance/
[diagnostic]: ../app-insights-search-diagnostic-logs/ 
[availability]: ../app-insights-monitor-web-app-availability/
[usage]: ../app-insights-web-track-usage/
[track]: ../app-insights-web-track-custom-events/
[qna]: ../app-insights-troubleshoot-faq/
[webclient]: ../app-insights-start-monitoring-app-health-usage/#webclient

