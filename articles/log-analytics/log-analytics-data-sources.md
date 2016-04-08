<properties 
   pageTitle="Data sources in Log Analytics"
   description="Data sources define the data that Log Analytics collects from agents and other connected sources.  This article describes the concept of how Log Analytics uses data sources, explains the details of how to configure them, and provides a summary of the different data sources available."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/08/2016"
   ms.author="bwren" />

# Data sources in Log Analytics

Log Analytics collects data from the Connected Sources in your OMS workspace and stores it in OMS repository.  The data that is collected from each is defined by the Data Sources that you configure.  Data in the OMS repository is stored as a set of records.  Each data source creates records of a particular type with each type having its own set of properties.

![Log Analytics data collection](./media/log-analytics-data-sources/overview.png)

Data Sources are different than OMS Solutions which also collect data from Connected Sources and create records in the OMS repository.  Solutions can be added to your workspace from the Solutions Gallery and will typically provide additional analysis tools in the OMS portal.  

## Summary of data sources

The data sources that are currently available in Log Analytics are listed in the following table.  Each has a link to a separate article providing detail for that data source.

| Data Source | Event Type | Description |
|:--|:--|:--|
| [Custom logs](log-analytics-custom-logs.md) | \<LogName\>_CL | Text files on Windows or Linux agents containing log information. |
| [Windows Event logs](log-analytics-windows-events.md) | Event | Events collected from the event log on Windows computers. |
| [Windows Performance counters](log-analytics-windows-performance-counters.md) | Perf | Performance counters collected from Windows computers. |
| Linux Performance counters | Perf | Performance counters collected from Linux computers. |
| IIS logs | W3CIISLog | Internet Information Services logs in W3C format. |
| Syslog | Syslog | Syslog events on Windows or Linux computers. |

## Configuring data sources

You configure data sources from the **Data** menu in Log Analytics **Settings**.  Any configuration is delivered to all connected sources in your OMS workspace.  You cannot currently exclude any agents from this configuration.

![Configure Windows events](./media/log-analytics-data-sources/configure-events.png)

2. In the OMS console select the **Settings** tile.
3. Select **Data**.
4. Click on the data source to configure.
5. Follow the link to the documentation for each data source in the above table for details on their configuration.

## Data collection

Data source configurations are delivered to agents that are directly connected to OMS within a few minutes.  The specified data is collected from the agent and delivered directly to Log Analytics at intervals specific to each data source.  See the documentation for each data source for these specifics.

For System Center Operations Manager (SCOM) agents in a connected management group, data source configurations are translated into management packs and delivered to the management group every 5 minutes by default.  The agent downloads the management pack like any other,  collects the specified data and send it to a management server which forwards the data to the OMS.  The agent requires no communication directly with OMS for any of the data sources.    

## Log Analytics records

All data collected by Log Analytics is stored in the OMS repository as records.  All records have the properties in the following table.  Different types of records will add additional properties to this standard set.  See the documentation for each data source and solution for details.

| Property | Description |
|:--|:--|
| Type          | Type of the record |
| TimeGenerated | Date and time that the record was collected by Log Analytics. |
| SourceSystem  | Type of agent the record was collected from. <br> OpsManager – Windows agent, either direct connect or SCOM <br> Linux – All Linux agents <br> AzureStorage – Azure Diagnostics. |

## Next Steps

- Create a log query to retrieve and analyze records collected by the data sources. 