<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />
 
# Monitor your application's health and usage with Application Insights

*Application Insights is in preview.*


Make sure your application is performing well and satisfying your users. Application Insights will alert you to any performance issues and help you find and diagnose the root causes. It will let you trace user activities so that you can tune your app to their needs.

Application Insights can monitor Windows Phone apps, Windows Store apps, and web applications hosted on-premise or on virtual machines, as well as Microsoft Azure websites. 

You'll need [Visual Studio Update 3](http://go.microsoft.com/fwlink/?LinkId=397827) and an account in [Microsoft Azure](http://azure.com) (there's a free trial period).

1. [Add Application Insights](#add)
+ [Run your project](#run)
+ [See monitor data](#monitor)
+ [Deploy your application](#deploy)
+ [Next steps](#next)


## <a name="add"></a>1. Add Application Insights

### If it's a new project...

When you create a new project in Visual Studio 2013, make sure Application Insights is selected. 


![Create an ASP.NET project](./media/appinsights/appinsights-01-vsnewp1.png)

If this is your first time, you'll be asked to provide your login for Microsoft Azure Preview, or to sign up. (It's different from your Visual Studio Online account.)

> No Application Insights option? Check you're using Visual Studio 2013 Update 3, and that you're creating a web, Windows Store, or Windows Phone project.

### ... or if it's an existing project

To add Application Insights to a new project, right click the project in Solution Explorer, and choose Add Application Insights.

![Choose Add Application Insights](./media/appinsights/appinsights-03-addExisting.png)


## <a name="run"></a>2. Run your project

Run your application with F5 and try it out - open different pages.

In Visual Studio, you'll see a count of the events that have been received.

![](./media/appinsights/appinsights-09eventcount.png)

## <a name="monitor"></a>3. See monitor data

Open Application Insights from your project.

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)


Look for data in the  Application health and Usage analytics tiles. For example:

![Click through to more data](./media/appinsights/appinsights-05-usageTiles.png)

Click any tile to see more detail. [Learn more about the tiles and reports.][monitor]

> [WACOM.NOTE] Many of the tiles show limited detail in this preview version. 

## <a name="deploy"></a>4. Deploy your application

Deploy your application and watch the data accumulate.


## <a name="next"></a>Next steps

[Learn more about the tiles and reports][monitor]

[Web tests]

[Capture and search diagnostic logs][diagnostics]

[Troubleshooting][trouble]


<!--Link references-->
[Web tests]: ../appinsights-10Avail/
[monitor]: ../appinsights-04monitor/
[existing]: ../appinsights-02-existing/
[diagnostics]: ../appinsights-24diagnostics/
[trouble]: ../appinsights-09qna/




