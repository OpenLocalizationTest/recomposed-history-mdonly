<properties title="Search diagnostic logs with Application Insights" pageTitle="Search diagnostic logs" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Diagnostic search in Application Insights

One of the most traditional debugging methods is to insert lines of code that emit a trace log. [Application Insights][start] can capture your web server logs and help you search and filter them. If you already use log4Net, NLog or System.Diagnostics.Trace, you can capture those logs with our adapter. Or you can use the TrackTrace and TrackException methods built into the Application Insights SDK.

Your search results can also include the regular page view and request events that are used to build the [usage][usage] and [performance][perf] reports, together with any [custom TrackEvent calls][track] that you have written.


2. [Install an adaptor for your logging framework?](#capture)
+ [Insert diagnostic log calls](#pepper)
+ [Exceptions](#exceptions)
+ [View log data](#view)
+ [Search log data](#search)
+ [Troubleshooting](#questions)
+ [Next steps](#next)



## <a name="capture"></a> Install an adapter for your logging framework?

If you haven't yet [installed Application Insights in your project][start], do that now.

If you'll use the built-in Application Insights SDK Track*() calls, you don't need an adapter - [skip to the next section](#pepper).

To search logs generated with log4Net, NLog, or System.Diagnostics.Trace, install the appropriate adapter:

1. If you plan to use log4Net or NLog, install it in your project. 
2. In Solution Explorer, right-click your project and choose **Manage NuGet Packages**.
3. Select Online > All, select **Include Prerelease** and search for "Microsoft.ApplicationInsights"

    ![Get the prerelease version of the appropriate adapter](./media/appinsights/appinsights-36nuget.png)

4. Select the appropriate package - one of:
  + Microsoft.ApplicationInsights.TraceListener (to capture System.Diagnostics.Trace calls)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

The NuGet package installs the necessary assemblies, and also modifies web.config or app.config.

## <a name="pepper"></a>3. Insert diagnostic log calls

Insert event logging calls using your chosen logging framework. 

For example, if you use the Application Insights SDK, you might insert:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Or if you use System.Diagnostics.Trace:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

If you prefer log4net or NLog:

    logger.Warn("Slow response - database01");

Run your app in debug mode, or deploy it to your web server.

### <a name="exceptions"></a>Exceptions

To send exceptions to the log:

JavaScript at client

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C# at server

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB at server

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

The properties and measurements parameters are optional, but are useful for filtering and adding extra information. For example, if you have an app that can run several games, you could find all the exception reports related to a particular game. You can add as many items as you like to each dictionary.

## <a name="view"></a>4. View log data


1. In Application Insights, open diagnostic search.

    ![Open diagnostic search](./media/appinsights/appinsights-30openDiagnostics.png)
   
2. Set the filter for the event types you'd like to see.

    ![Open diagnostic search](./media/appinsights/appinsights-331filterTrace.png)


The event types are:

* **Trace** - Search diagnostic logs that you've captured from your web server. This includes log4Net, NLog, System.Diagnostic.Trace, and ApplicationInsights TrackTrace calls.
* **Request** - Search HTTP requests received by the server component of your web app, including page requests, data requests, images, and so on. The events you'll see are the telemetry sent by the Application Insights server SDK, which are used to create the request count report.
* **Page View** - Search page view events. These events are sent by the web client and are used to create page view reports. (If you don't see anything here, set up [web client monitoring][usage].)
* **Custom Event** - If you inserted calls to TrackEvent() and TrackMetric() to [monitor usage][track], you can search them here.

Select any log event to see its detail. 

![Open diagnostic search](./media/appinsights/appinsights-32detail.png)

You can use plain strings (without wildcards) to filter the field data within an item.

The available fields depend on the logging framework and the parameters you used in the call.


## <a name="search"></a>5. Search the data

Set a time range and search for terms. Searches over a shorter range are faster. 

![Open diagnostic search](./media/appinsights/appinsights-311search.png)

Notice that you search for terms, not substrings. Terms are alphanumeric strings including some punctuation such as '.' and '_'. For example:

<table>
  <tr><th>term</th><th>is NOT matched by</th><th>but these do match</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>New Delay</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

Here are the search expressions you can use:

<table>
                    <tr>
                      <th>
                        <p>Sample query</p>
                      </th>
                      <th>
                        <p>Effect</p>
                      </th>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">slow</span>
                        </p>
                      </td>
                      <td>
                        <p>Find all events in the date range whose fields include the term "slow"</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">database??</span>
                        </p>
                      </td>
                      <td>
                        <p>Matches database01, databaseAB, ...</p>
                        <p>? is not allowed at the start of a search term.</p>
                      </td>
                    </tr>
                     <tr>
                      <td>
                        <p>
                          <span class="code">database*</span>
                        </p>
                      </td>
                      <td>
                        <p>Matches database, database01, databaseNNNN</p>
                        <p>* is not allowed at the start of a search term</p>
                      </td>
                    </tr>
                   <tr>
                      <td>
                        <p>
                          <span class="code">apple AND banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Find events that contain both terms. Use capital "AND", not "and".</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">apple OR banana</span>
                        </p>
                        <p>
                          <span class="code">apple banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Find events that contain either term. Use "OR", not "or".</p>
                        <p>Short form.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">apple NOT banana</span>
                        </p>
                        <p>
                          <span class="code">apple -banana</span>
                        </p>
                      </td>
                      <td>
                        <p>Find events that contain one term but not the other.</p>
                        <p>Short form.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>app* AND banana NOT (grape OR pear)</p>
                        <p>
                          <span class="code">app* AND banana -(grape pear)</span>
                        </p>
                      </td>
                      <td>
                        <p>Logical operators and bracketing.</p>
                        <p>Shorter form.</p>
                      </td>
                    </tr>
       <!-- -- fielded search feature not ready yet --
                    <tr>
                      <td>
                        <p>
                          <span class="code">message:slow</span>
                        </p>
                        <p>
                          <span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span>
                        </p>
                        <p>
                          <span class="code">properties.logEventInfo.level:Error</span>
                        </p>
                      </td>
                      <td>
                        <p>Match the specified field. By default, all fields are searched. To see what fields are available, select an event to look at its detail.</p>
                      </td>
                    </tr>
 -->
</table>


## <a name="questions"></a>Q & A

### <a name="emptykey"></a>I get an error "Instrumentation key cannot be empty"

Looks like you installed the logging adapter Nuget package without installing Application Insights.

In Solution Explorer, right-click `ApplicationInsights.config` and choose **Update Application Insights**. You'll get a dialog that invites you to sign in to Azure and either create an Application Insights resource, or re-use an existing one. That should fix it.

### <a name="limits"></a>How much data is retained?

Up to 500 events per second from each application. Events are retained for seven days.

### <a name="cani"></a>Can I...?

- Set alerts on events and exceptions
- Export logs for further analysis
- Search on specific properties

Not just yet, but all these features are on the backlog.

## <a name="add"></a>Next steps

* [Set up availability and responsiveness tests][availability]
* [Troubleshooting][qna]





[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




