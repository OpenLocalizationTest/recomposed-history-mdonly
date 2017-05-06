﻿---
title: Get started with Azure Data Lake Analytics using Azure PowerShell | Microsoft Docs
description: 'Use Azure PowerShell to create a Data Lake Analytics account, create a Data Lake Analytics job using U-SQL, and submit the job. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun

ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: edmaca

---
# Tutorial: get started with Azure Data Lake Analytics using Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Learn how to use Azure PowerShell to create Azure Data Lake Analytics accounts and then submit and run U-SQL jobs. For more information about Data Lake Analytics, see [Azure Data Lake Analytics overview](data-lake-analytics-overview.md).

In this tutorial, you will develop a job that reads a tab separated values (TSV) file and converts it into a comma-separated values (CSV) file. To go through the same tutorial using other supported tools, click the tabs on the top of this section.

## Prerequisites
Before you begin this tutorial, you must have the following information:

* **An Azure subscription**. See [Get Azure free trial](https://azure.microsoft.com/pricing/free-trial/).
* **A workstation with Azure PowerShell**. See [How to install and configure Azure PowerShell](/powershell/azure/overview).
* **An Azure Resource Group**. 

## Preparing for the tutorial
To create a Data Lake Analytics account, you first need to define:

* **Azure Resource Group**: A Data Lake Analytics account must be created within an Azure Resource group.
* **Data Lake Analytics account name**: The Data Lake account name must only contain lowercase letters and numbers.
* **Location**: one of the Azure data centers that supports Data Lake Analytics.
* **Default Data Lake Store account**: each Data Lake Analytics account has a default Data Lake Store account. These accounts must be in the same location.

The PowerShell snippets in this tutorial use these variables to store this information

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## Create a Data Lake Analytics account
Create a Resource Group, Data Lake Store account, and a Data Lake Analytics account.

```
New-AzureRmResourceGroup -Name  $rg -Location $location

New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location

New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## Get information about a Data Lake Analytics account

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## Upload data to Data Lake Store

Download the SearchLog.tsv file from the location below.

    https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/SearchLog.tsv

Upload the file into the Data Lake Store account.

```
Import-AdlStoreItem -AccountName $adls -Path "D:\SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv" 
```

## Submit a U-SQL job

Create a text file with following U-SQL script.

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

Submit the script.

```
$job = Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

List all the jobs in the account. This includes the currently running jobs and those jobs that have recently completed.

```
Get-AdlJob -Account $adla
```

Get the status of a specific job.

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Instead of calling Get-AdlAnalyticsJob over and over until a job finishes, you can use the Wait-AdlJob cmdlet.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

After the job is completed, check if the output file exists by listing the files in a folder.

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

You can also test if the file exists

```
-Account $adls -Path "/data.csv"
```

Download the file

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```

## See also
* To see the same tutorial using other tools, click the tab selectors on the top of the page.
* To learn U-SQL, see [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md).
* For management tasks, see [Manage Azure Data Lake Analytics using Azure portal](data-lake-analytics-manage-use-portal.md).
