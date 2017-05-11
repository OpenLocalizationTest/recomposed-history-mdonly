---
title: Use Azure Stream Analytics Tools for Visual Studio | Microsoft Docs
description: Getting-started tutorial for the Azure Stream Analytics Tools for Visual Studio
keywords: visual studio
documentationcenter: ''
services: stream-analytics
author: 
manager: 
editor: 

ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 
ms.author: 

---
# Use the Azure Stream Analytics Tools for Visual Studio
## Introduction
In this tutorial, you learn how to use Azure Stream Analytics Tools for Visual Studio to create, author, test locally, manage, and debug your Stream Analytics jobs. 

After completing this tutorial, you will be able to:
* Familiarize yourself with the Stream Analytics Tools for Visual Studio.
* Configure and deploy a Stream Analytics job.
* Test your job locally with local sample data.
* Use monitoring to troubleshoot issues.
* Export existing jobs to projects.

## Prerequisites
To complete this tutorial, you need the following prerequisites:
* Finish the steps that precede **Create a Stream Analytics job** in the [Build an IoT solution by using Stream Analytics tutorial](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Use Visual Studio 2015, Visual Studio 2013 update 4, or Visual Studio 2012. Enterprise (Ultimate/Premium), Professional, and Community editions are supported. Express edition is not supported. Visual Studio 2017 is not supported. 
* Use the Azure SDK for .NET version 2.7.1 or above. Install it by using the [Web platform installer](http://www.microsoft.com/web/downloads/platform.aspx).
* Install the [Stream Analytics Tools for Visual Studio](http://aka.ms/asatoolsvs).

## Create a Stream Analytics project
In Visual Studio, click the **File** menu and choose **New Project**. In the templates list on the left, choose **Stream Analytics** and then click **Azure Stream Analytics Application**.
Enter the project **Name**, **Location**, and **Solution name** as you do for other projects.

![Create a Stream Analytics project](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

A **Toll** project is generated in **Solution Explorer**.

![Create a Stream Analytics project](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## Choose the correct subscription
1. In Visual Studio, click the **View** menu and open **Server Explorer**.

2. Sign in with your Azure Account. 

## Define the input sources
1.	In **Solution Explorer**, expand the **Inputs** node and rename **Input.json** to **EntryStream.json**. Double-click **EntryStream.json**.
2.	The **Input Alias** is now **EntryStream**. Note that the input alias is used in the query script. 
3.	In **Source Type**, select **Data Stream**.
4.	In **Source**, select **Event Hub**.
5.	In **Service Bus Namescape**, select the **TollData** option.
6.	In **Event Hub Name**, select **entry**.
7.	In **Event Hub Policy Name**, select **RootManageSharedAccessKey** (the default value).
8.	In **Event Serialization Format**, select **Json**. 
9.  In **Encoding**, select **UTF8**.

    Your settings should look like this:

    ![Define input sources](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
10.	To finish the wizard, click **Save**. Now you can add another input source to create the exit stream. Right-click the **Inputs** node, and click **New Item**.

    ![Define input sources](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
11.	In the window, choose **Azure Stream Analytics Input**, and change the **Name** to **ExitStream.json**. Click **Add**.

    ![Define input sources](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
12.	Double-click **ExitStream.json** in the project, and follow the same steps as you did for the entry stream. Be sure to enter **exit** for the **Event Hub Name** as shown in the following screenshot:

    ![Define input sources](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

Now you have defined two input streams:

    ![Define input sources](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
Next, add reference data input for the blob file that contains car registration data.

13.	Right-click the **Inputs** node in the project, and then follow the same steps as you did for the stream inputs. In **Input Alias**, enter **Registration**, and in **Source Type**, select **Reference data**.

    ![Define input sources](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

14.	In **Storage Account**, select the **tolldata** option. In **Container**, select **tolldata**, and in **Path Pattern**, enter**registration.json**. This file name is case sensitive and should be lowercase.
15.	Click **Save** to finish the wizard.

Now all the inputs are defined.

## Define the output
1.	In **Solution Explorer**, expand the **Inputs** node and double-click **Output.json**.

2.	In **Output Alias**, enter **output**. 
3.  In **Sink**, select **SQL Database**.
4.	In **Database**, select **TollDataDB**.
5.	In  **User Name**, enter **tolladmin**.  
6.  In **Password**, enter **123toll!**.
7.  In **Table**, enter **TollDataRefJoin**.
8.	Click **Save**.

![Define output](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## Create a Stream Analytics query
This tutorial attempts to answer several business questions that are related to toll data. It also constructs Stream Analytics queries that can be used in Stream Analytics to provide relevant answers.
Before you start your first Stream Analytics job, let’s explore a simple scenario and the query syntax.

### Introduction to the Stream Analytics query language
Let’s say that you need to count the number of vehicles that enter a toll booth. Because this example is a continuous stream of events, you have to define a period of time. Modify the question to be “How many vehicles enter a toll booth every three minutes?” This way to count data is commonly referred to as the tumbling count.

Look at the Stream Analytics query that answers this question:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Stream Analytics uses a query language that's like SQL and adds a few extensions to specify time-related aspects of the query.

For more information, see [Time Management](https://msdn.microsoft.com/library/azure/mt582045.aspx) and [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) constructs used in the query from MSDN.

Now that you have written your first Stream Analytics query, it's time to test it by using sample data files located in your TollApp folder in the following path:

**..\TollApp\TollApp\Data**

This folder contains the following files:
*	Entry.json
*	Exit.json
*	Registration.json

## Question: Number of vehicles entering a toll booth
In the project, double-click **Script.asaql** to open the script in the **Query Editor**, and paste the script in the previous section into the editor. The Query Editor supports IntelliSense, syntax coloring, and Error marker.

![Edit query](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### Test Stream Analytics queries locally

1.	To compile the query to see if there is a syntax error, right-click the project and choose **Build**. 

2.	To validate this query against sample data, you can use local sample data. Right-click the input, and select **Add local input** from the shortcut menu.

    ![Add local input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
3. In the pop-up window, select the sample data from your local path. Click **Save**.

    ![Add local input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    A file named **local_EntryStream.json** is added automatically to your inputs folder.

    ![Add local input](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
4.	Click **Run Locally** in the **Query Editor**. Or you can press the F5 key.

    ![Local run](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![Local run](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    Press any key to view the output in the **ASA Local Run Result** window in Visual Studio. 

    ![Local run](./media/stream-analytics-tools-for-vs/local-testing-output.png)

5.  Click **Open Result Folder** to check the output files both in CSV and JSON format.

    ![Local run](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### Sample the input data
You can also sample input data from input sources to a local file. Right-click the input config file, and select **Sample Data**. 

![Sample data](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

You can sample only Event Hub or IoT Hub for now. Other input sources are not supported. In the pop-up window,enter the local path for saving the sample data. Click **Sample**.

![Sample data](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
You can see the progress in the **Output** window. 

![Sample data](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### Submit a Stream Analytics query to Azure
In the **Query Editor**, click **Submit To Azure** in the script editor.

![Submit job](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
Choose **Create a New Azure Stream Analytics Job**. Enter the **Job Name**, and choose the correct **Subscription**. Click **Submit**.

![Submit job](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### Start a job
Now that your job is created, the job view is automatically opened. Click the **green arrow** button to start the job.

![Start job](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
Choose the default setting, and click **Start**.
 
![Start job](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

The job **Status** changes to **Running**, and **Input Events** and **Output Events** appear.

![Start job](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## Check the results in Visual Studio
1. Open Visual Studio Server Explorer, and right-click the **TollDataRefJoin** table.
2. Click **Show Table Data** to see the output of your job.
   
    ![Selection of Show Table Data in Server Explorer](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### View the job metrics
Some basic job statistics can be found in **Job Metrics**. 

![Job metrics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## List the job in Server Explorer
Click **Stream Analytics Jobs** in Server Explorer, and then click **Refresh**. The job appears under **Stream Analytics jobs**.
![List jobs](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## Open the job view
To open the job view, expand your job node and double-click the **Job View** node.

![Job view](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## Export an existing job to a project
There are two ways you can export an existing job to a project.
1. Right-click the job node in the **Stream Analytics Jobs** node in Server Explorer. Click **Export to New Stream Analytics Project** on the shortcut menu.

    ![Export job](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

    The project is generated in Solution Explorer.

    ![Export job](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
2. In the job view, click **Generate Project**.

    ![Export job](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## Known issues and limitations
 
- There is no support for Power BI output and Azure Date Lake Store output.
- There is no editor support for adding or changing JavaScript user-defined functions.

## Next steps
* [Introduction to Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started by using Azure Stream Analytics](stream-analytics-get-started.md)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
