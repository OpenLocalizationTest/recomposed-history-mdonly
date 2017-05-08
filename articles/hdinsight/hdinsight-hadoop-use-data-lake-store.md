---
title: Use Data Lake Store with Hadoop in Azure HDInsight | Microsoft Docs
description: Learn how to query data from Azure Data Lake Store and to store results of your analysis.
keywords: blob storage,hdfs,structured data,unstructured data, data lake store
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/04/2017
ms.author: jgao

---
# Use Data Lake Store with Hadoop in Azure HDInsight

To analyze data in HDInsight cluster, you can store the data either in [Azure Storage](hdinsight-hadoop-use-blob-storage.md), Azure Data Lake Store, or both. Both storage options enable you to safely delete HDInsight clusters that are used for computation without losing user data.

Hadoop supports a notion of the default file system. The default file system implies a default scheme and authority. It can also be used to resolve relative paths. During the HDInsight cluster creation process, you can specify a blob container in Azure Storage as the default file system, or with HDInsight 3.5, you can select either Azure Storage or Azure Data Lake Store as the default files system.

In this article, you learn how Data Lake Store works with HDInsight clusters. For more information about creating an HDInsight cluster, see [Get Started with HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).


> [!NOTE]
> Azure Data Lake Store is always accessed through a secure channel, so there is no `adls` filesystem scheme name. You always use `adl`.
> 
> 

## Availabilities for HDInsight clusters

HDInsight clusters can use Azure Data Lake Store in two ways:

* Azure Data Lake Store as the default storage
* Azure Data Lake Store as additional storage, with Azure Storage Blob as default storage.

As of now, only some of the HDInsight cluster types/versions support using Data Lake Store as default storage and additional storage accounts:

| HDInsight cluster type | Data Lake Store as default storage | Data Lake Store as additional storage| Note |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight version 3.5 | Yes | Yes | |
| HDInsight version 3.4 | No | Yes | |
| HDInsight version 3.3 | No | No | |
| HDInsight version 3.2 | No | Yes | |
| HDInsight Premium (tier)| No | No | |
| HBase (all HDInsight versions) | No | No| |
| Storm | | |You can use Data Lake Store to write data from a Storm topology. You can also use Data Lake Store for reference data that can then be read by a Storm topology.|

Adding a Data Lake Store account as additional and adding more than one Data Lake Store account are accomplished by giving the HDInsight cluster permission on data in one ore more Data Lake Store accounts. See [Configure Data Lake Store access](#configure-data-lake-store-access).

Using Data Lake Store as an additional storage account does not affect performance or the ability to read or write to Azure storage from the cluster.



## Use Azure Data Lake Store as default storage

When HDInsight is deployed with Azure Data Lake Store as default storage, the cluster-related files are stored in Azure Data Lake store in the following location:

	adl://mydatalakestore/<cluster_root_path>/

where `<cluster_root_path>` is the name of a folder you create in Azure Data Lake Store. By specifying a root path for each cluster, you can use the same Azure Data Lake Store account for more than one cluster. So, you can have a setup where:

* Cluster1 can use the path `adl://mydatalakestore/cluster1storage`
* Cluster2 can use the path `adl://mydatalakestore/cluster2storage`

Notice that both the clusters use the same Data Lake Store account **mydatalakestore**. Each cluster has access to its own root filesystem in Data Lake Store. The Azure portal deployment experience in particular prompts you to use a folder name such as **/clusters/\<clustername>** for the root path.



## Use Azure Data Lake Store as additional storage

You can use Data Lake Store as additional storage for the cluster as well. In such cases, the cluster default storage can either be an Azure Storage Blob or an Azure Data Lake Store account. If you are running HDInsight jobs against the data stored in Azure Data Lake Store as additional storage, you must use the fully-qualified path to the files. For example:

	adl://mydatalakestore.azuredatalakestore.net/<file_path>

Note that there's no **cluster_root_path** in the URL now. That's because Data Lake Store is not a default storage in this case so all you need to do is provide the path to the files.

## Use more than one Data Lake Store accounts


## Access files from the cluster

There are a number of ways you can access the files in Azure Data Lake Store from an HDInsight cluster.

* **Using the fully qualified name**. With this approach, you provide the full path to the file that you want to access.

		adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Using the shortened path format**. With this approach, you replace the path up to the cluster root with adl:///. So, in the example above, you can replace `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` with `adl:///`.

		adl:///<file path>

* **Using the relative path**. With this approach, you only provide the relative path to the file that you want to access. For example, if the complete path to the file is:

		adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

	You can access the same sample.log file by using this relative path instead.

		/example/data/sample.log

## Creating HDInsight clusters with access to Data Lake Store

Follow the links below for detailed instructions on how to create HDInsight clusters with access to Data Lake Store.

* [Using Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [Using PowerShell (with Data Lake Store as default storage)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Using PowerShell (with Data Lake Store as additional storage)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Using Azure templates](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


## Next steps
In this article, you learned how to use HDFS-compatible Azure storage and Azure Data Lake Store with HDInsight. This allows you to build scalable, long-term, archiving data acquisition solutions and use HDInsight to unlock the information inside the stored structured and unstructured data.

For more information, see:

* [Get started with Azure HDInsight][hdinsight-get-started]
* [Get started with Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
* [Upload data to HDInsight][hdinsight-upload-data]
* [Use Hive with HDInsight][hdinsight-use-hive]
* [Use Pig with HDInsight][hdinsight-use-pig]
* [Use Azure Storage Shared Access Signatures to restrict access to data with HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
