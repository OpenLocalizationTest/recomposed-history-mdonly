---
title: Develop U-SQL scripts using Data Lake Tools for Visual Studio | Microsoft Docs
description: 'Learn how to install Data Lake Tools for Visual Studio, how to develop and test U-SQL scripts. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun

ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: edmaca, yanacai

---
# Tutorial: develop U-SQL scripts using Data Lake Tools for Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Learn how to use the Visual Studio to create Azure Data Lake Analytics accounts, define jobs in [U-SQL](data-lake-analytics-u-sql-get-started.md), and submit jobs to the Data Lake Analytics service. For more
information about Data Lake Analytics, see [Azure Data Lake Analytics overview](data-lake-analytics-overview.md).


## Prerequisites

* **Visual Studio**: all editions except Express are supported
    * Visual Studio 2017
    * Visual Studio 2015 update 4
    * Visual Studio 2013
* **Microsoft Azure SDK for .NET** version 2.7.1 or above.  Install it using the [Web platform installer](http://www.microsoft.com/web/downloads/platform.aspx).
* A **Data Lake Analytics Account** account. To create an account, see [Get Started with Azure Data Lake Analytics using Azure portal](data-lake-analytics-get-started-portal.md).

## Install Azure Data Lake Tools for Visual Studio (ADLToolsForVS)

Download and install ADLToolsForVS [from here](http://aka.ms/adltoolsvs). After installation:
* The **Server Explorer > Azure** node contains a **Data Lake Analytics** node. 
* The **Tools** menu has a **Data Lake** item.

## Connect to an Azure Data Lake Analytics account

1. Open Visual Studio.
2. From the **View > Server Explorer** to open Server Explorer.
3. Right-click **Azure**. Select **Connect to Microsoft Azure Subscription**, and then follow the instructions.
4. In **Server Explorer**, select **Azure > Data Lake Analytics**. You shall see a list of your Data Lake Analytics accounts.


## Your first U-SQL script

The following text is a simple U-SQL script. It defines a small dataset and  writes that dataset to the default Data Lake Store as a file called `/data.csv`.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

**Submit a Data Lake Analytics job**

1. Click **File > New > Project**.
2. Select the **U-SQL Project** type.

    ![new U-SQL Visual Studio project](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Click **OK**. Visual studio creates a solution with a **Script.usql** file.
4. Paste the previous script into the **Script.usql** window.
5. Specify the Data Lake Analytics account, Database, and Schema. 

    ![Submit U-SQL Visual Studio project](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

6. From **Solution Explorer**, right-click **Script.usql**, and then click **Submit Script**. 
7. Verify the **Analytics Account**, and then click **Submit**. Submission results are available in the Data Lake Tools for Visual Studio Results window when the submission is completed.

    ![Submit U-SQL Visual Studio project](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
8. Click the Refresh button to see the latest job status and refresh the screen. When the job succeeds, it shows the **Job Graph**, **Meta Data Operations**, **State History**, **Diagnostics**:

    ![U-SQL Visual Studio Data Lake Analytics job performance graph](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * Job Summary. Show the summary information of current job: State, Progress, Execution Time, Runtime Name, Submitter etc.   
   * Job Details. Detailed information on this job is provided, including script, resource, Vertex Execution View.
   * Job Graph. Four graphs are provided to visualize the job’s information: Progress, Data Read, Data Written, Execution Time, Average Execution Time Per Node, Input Throughput, Output Throughput.
   * Metadata Operations. It shows all the metadata operations.
   * State History.
   * Diagnostics. ADLToolsForVS diagnoses job execution automatically. Alerts appear when there are some errors or performance issues in their jobs.

**To check job state**

1. From **Server Explorer**, select **Azure > Data Lake Analytics**. 
2. Expand the Data Lake Analytics account name
3. Double-click **Jobs**.
4. Select the job you previously submitted.

**To see the output of a job**

1. Using **Server Explorer** navigate to the job you submitted.
2. Click on the **Data** tab.
3. In the **Job Outputs** tab, click the `"/data.csv"` file.

## Next steps

* Get started with Data Lake Analytics [using Azure portal](data-lake-analytics-get-started-portal.md) | [using Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [.NET SDK](data-lake-analytics-get-started-net-sdk.md)
* [Debug C# code in U-SQL jobs](data-lake-analytics-debug-u-sql-jobs.md)
* [Use the Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
