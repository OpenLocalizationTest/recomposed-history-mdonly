---
title: 'Data Factory tutorial: First data pipeline | Microsoft Docs'
description: This Azure Data Factory tutorial shows you how to create and schedule a data factory that processes data using Hive script on a Hadoop cluster.
services: data-factory
keywords: azure data factory tutorial, hadoop cluster, hadoop hive
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: ''

ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: spelluru

---
# Tutorial: Build your first pipeline to process data using Hadoop cluster
> [!div class="op_single_selector"]
> * [Overview and prerequisites](data-factory-build-your-first-pipeline.md)
> * [Azure portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

In this tutorial, you build your first Azure data factory with a data pipeline that transforms data by running Hive script on an Azure HDInsight (Hadoop) cluster. 

This article provides overview and prerequisites for the tutorial. After you complete the prerequisites, select one of the options in the drop-down list at the beginning (or) links at the end of this article to do the tutorial.   

## Tutorial overview
In this tutorial, you perform the following steps:

1. Create a **data factory**. A data factory can contain one or more data pipelines that move and transform data. 
2. Create **linked services**. You create a linked service to link a data store or a compute service to the data factory. A data store such as Azure Storage holds input/output data of activities in the pipeline. A compute service such as HDInsight Hadoop cluster processes/transforms data.    
3. Create input and output **datasets**. An input dataset represents the input for an activity in the pipeline and an output dataset represents the output for the activity.
4. Create the **pipeline**. A pipeline can have one or more activities (Examples: Copy Activity, HDInsight Hive Activity). This sample uses the HDInsight Hive activity that runs a Hive script on a HDInsight Hadoop cluster. The script first creates a table that references the raw web log data stored in Azure blob storage and then partitions the raw data by year and month.
  
Here is the **diagram view** of the sample data factory you build in this tutorial. 

![Diagram view in Data Factory tutorial](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

In this tutorial, **inputdata** folder of the **adfgetstarted** Azure blob container contains one file named input.log. This log file has entries from three months: January, February, and March of 2016. Here are the sample rows for each month in the input file. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

When the file is processed by the pipeline with HDInsight Hive Activity, the activity runs a Hive script on the HDInsight cluster that partitions input data by year and month. The script creates three output folders that contain a file with entries from each month.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

From the sample lines shown above, the first one (with 2016-01-01) is written to the 000000_0 file in the month=1 folder. Similarly, the second one is written to the file in the month=2 folder and the third one is written to the file in the month=3 folder.  

## Prerequisites
Before you begin this tutorial, you must have the following prerequisites:

1. Read through [Introduction to Azure Data Factory](../articles/data-factory/data-factory-introduction.md) article if you haven't already done so. 
2. **Azure subscription** - If you don't have an Azure subscription, you can create a free trial account in just a couple of minutes. See the [Free Trial](https://azure.microsoft.com/pricing/free-trial/) article on how you can obtain a free trial account.
2. **Azure Storage** – You use an Azure storage account for storing the data in this tutorial. If you don't have an Azure storage account, see the [Create a storage account](../articles/storage/storage-create-storage-account.md#create-a-storage-account) article. After you have created the storage account, note down the **account name** and **access key**. See [View, copy and regenerate storage access keys](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).
3. Download and review the Hive query file (**HQL**) located at: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionalweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql)
4. Download and review the sample input file (**input.log**) located at: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Create a blob container named **adfgetstarted** in your Azure Blob Storage. 
6. Upload **partitionweblogs.hql** file to the **script** folder in the **adfgetstarted** container. Use tools such as [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 
7. Upload **input.log** file to the **inputdata** folder in the **adfgetstarted** container. 

After you complete the prerequisites, select one of the following options to do the tutorial: 

- [Azure portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Azure portal and Visual Studio provides GUI way of building your data factories. Whereas, PowerShell, Resource Manager Template, and REST API options provides scripting/programming way of building your data factories.  