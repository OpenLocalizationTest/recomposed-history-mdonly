---
title: Manage Azure Data Lake Analytics using the Azure portal | Microsoft Docs
description: Learn how to manage Data Lake Analytics acounts, data sources, users, and jobs.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun

ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca

---
# Manage Azure Data Lake Analytics using Azure portal
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Learn how to manage Azure Data Lake Analytics accounts, account data sources, users, and jobs using the Azure portal. To see management topics using other tools, click the tab selector on the top of the page.

<!-- ################################ -->
<!-- ################################ -->
## Manage accounts

**To create a Data Lake Analytics account**

1. Sign on to the [Azure portal](https://portal.azure.com).
2. Click **New**, click **Intelligence + analytics**, and then click **Data Lake Analytics**.
3. Type or select the following values:   
   * **Name**: Name the Data Lake Analytics account.
   * **Subscription**: Choose the Azure subscription used for the Analytics account.
   * **Resource Group**. Select an existing Azure Resource Group or create a new one. Azure Resource Manager enables you to work with the resources in your application as a group. 
   * **Location**. Select an Azure data center for the Data Lake Analytics account. 
   * **Data Lake Store**: Each Data Lake Analytics account has a dependent Data Lake Store account. The Data Lake Analytics account and the dependent Data Lake Store account must be located in the same Azure data center. 
4. Click **Create**. 

**To delete a Data Lake Analytics account**

1. Open the Data Lake Analytics account in the Azure portal
2. Click **Delete** from the button menu on the top of the blade.
3. Type the account name, and then click **Delete**.

Before you delete the Data Lake Analytics account, delete its default Data Lake Store account.

<!-- ################################ -->
<!-- ################################ -->

## Manage account data sources
Data Lake Analytics currently supports the following data sources:

* Azure Data Lake Store
* Azure Storage

**To add additional data sources**

1. Open a Data Lake Analytics account
2. Click **Settings** and then click **Data Sources**. You shall see the default Data Lake Store account listed there. 
3. Click **Add Data Source**.
    
* To add an Azure Data Lake Store account, you need the account name, and access to the account to be able query it.
* To add an Azure Blob storage, you need the storage account and the account key, which can be found by navigating to the storage account in the portal.

**To explore data sources**    

1. Open the Analytics account that you want to manage. For instructions, see [Access Data Lake Analytics accounts].
2. Click **Settings** and then click **Data Explorer**. 
   
    ![Azure Data Lake Analytics data explorer](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-data-explorer.png)
3. Click a Data Lake Store account to open the account.
   
    ![Azure Data Lake Analytics data explorer storage account](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-adls.png)
   
    For each Data Lake Store account, you can
   
   * **New Folder**: Add new folder.
   * **Upload**: Upload files to the Storage account from your workstation.
   * **Access**: Configure access permissions.
   * **Rename Folder**: Rename a folder.
   * **Folder properties**: Show properties such as path, last modified time, and so on.
   * **Delete Folder**: Delete a folder.

**To upload files to Data Lake Store account**

1. From the Portal, click **Browse** from the left menu, and then click **Data Lake Store**.
2. Click the Data Lake Store account that you want to upload data to.
3. Click **Data Explorer** from the top menu.
4. Click **New Directory** to create a new folder, or click a folder name to change folder.
5. Click **Upload** from the top menu to upload file.

**To upload files to Azure Blob storage account**

See [Upload data for Hadoop jobs in HDInsight](../hdinsight/hdinsight-upload-data.md).  The information applies to Data Lake Analytics.

## Manage users
Data Lake Analytics uses role-based access control with Azure Active Directory. When you create a Data Lake Analytics 
account, a "Subscription Admins" role is added to the account. You can add additional users and security groups with 
the following roles:

| Role | Description |
| --- | --- |
| Owner |Let you manage everything, including access to resources. |
| Contributor |Access the portal; submit and monitor jobs. To be able to submit jobs, a contributor needs the read or write permission to the Data Lake Store accounts. |
| DataLakeAnalyticsDeveloper |Submit, monitor, and cancel jobs.  These users can only cancel their own jobs. They cannot manage their own account, for instance, add users, change permissions, or delete the account. To be able to run jobs, they need read or write access to the Data Lake Store accounts |
| Reader |Lets you view everything, but not make any changes. |
| DevTest Labs User |Lets you view everything, and connect, start, restart, and shutdown virtual machines. |
| User Access Administrator |Lets you manage user access to Azure resources. |

For information on creating Azure Active Directory users and security groups, See [What is Azure Active Directory](../active-directory/active-directory-whatis.md).

**To add users or security groups to a Data Lake Analytics account**

1. Open the Analytics account that you want to manage.
2. Click **Settings**, and then click **Users**.   
3. From the **User** blade, click **Add**.
4. Select a role and add a user, and then click **OK**.

>[!NOTE]
>If this user or security group needs to submit jobs, they need to be given permission on the Data Lake Store as well. For more information, see [Secure data stored in Data Lake Store](../data-lake-store/data-lake-store-secure-data.md)
>

<!-- ################################ -->
<!-- ################################ -->

## Manage jobs

**To submit a job**

1. Open the Analytics account that you want to manage. For instructions, see [Access Data Lake Analytics accounts].
2. Click **New Job**.
   
    For each job, you can configure

    |Name|Description|
    |----|-----------|
    |Job Name|Enter the name of the job.|
    |Priority|Lower number has higher priority. If two jobs are both queued, the one with lower priority runs first|
    |Parallelism |Max number of compute processes to reserve for this job.|
    |Script|Enter the U-SQL script for the job.|

    Using the same interface, you can also explore the link data sources, and add additional files to the linked data sources. 
3. Click **Submit Job**.

**To monitor jobs**

1. Click **Job Management** as shown in the previous screenshot.
2. Click a job from the lists. 
3. Optionally click **Filter** to help you to find the jobs by **Time Range**, **Job Name**, and **Author**. 

## See also
* [Overview of Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Get started with Data Lake Analytics using Azure portal](data-lake-analytics-get-started-portal.md)
* [Manage Azure Data Lake Analytics using Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitor and troubleshoot Azure Data Lake Analytics jobs using Azure portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

