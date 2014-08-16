<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop-3.0" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />




# Get started using Hadoop 2.2 in HDInsight

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight" class="current">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a-->
</div>

HDInsight makes Apache Hadoop, a MapReduce software framework, available in a simpler, more scalable, and more cost-efficient Azure environment. HDInsight also provides a cost-efficient approach to the managing and storing of data using Azure Blob storage.

> [WACOM.NOTE] If you are new to Hadoop and Big Data, you might want to read more about the terms [Apache Hadoop][apache-hadoop], [MapReduce][apache-mapreduce], [HDFS][apache-hdfs], and  [Hive][apache-hive].

In conjunction with the general availability of Azure HDInsight, Microsoft also provides HDInsight Emulator for Azure, formerly known as *Microsoft HDInsight Developer Preview*. The Emulator targets developer scenarios and only supports single-node deployments. For using HDInsight Emulator, see [Get Started with the HDInsight Emulator][hdinsight-emulator].   

## What does this tutorial achieve? ##

Assume you have a large unstructured data set and you want to run queries on it to extract some meaningful information. That's exactly what we are going to do in this tutorial. Here's how we achieve this:

   ![HDI.GetStartedFlow][image-hdi-getstarted-flow]

You can also watch a demo video of this tutorial:

<center><iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen></iframe></center>

<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->

**Prerequisites:**

Before you begin this tutorial, you must have the following:

- An Azure subscription. For more information about obtaining a subscription, see [Purchase Options][azure-purchase-options], [Member Offers][azure-member-offers], or [Free Trial][azure-free-trial].
- A computer with Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone, or Office 2010 Professional Plus.

**Estimated time to complete:** 30 minutes

##In this tutorial

* [Create an Azure storage account](#storage)
* [Provision an HDInsight cluster](#provision)
* [Run a Hive job](#sample)
* [Connect to Microsoft business intelligence tools](#powerquery)
* [Next steps](#nextsteps)

##<a name="storage"></a>Create an Azure Storage account

HDInsight uses Azure Blob Storage for storing data. It is called *WASB* or *Azure Storage - Blob*. WASB is Microsoft's implementation of HDFS on Azure Blob storage. For more information see [Use Azure Blob storage with HDInsight][hdinsight-storage].

When you provision an HDInsight cluster, you specify an Azure Storage account. A specific Blob storage container from that account is designated as the default file system, just like in HDFS. The HDInsight cluster is by default provisioned in the same data center as the storage account you specified.

In addition to this storage account, you can add additional storage accounts when you custom-configure an HDInsight cluster. This additional storage account can either be from the same Azure subscription or different Azure subscriptions. For instructions, see [Provision HDInsight clusters using custom options][hdinsight-provision]. 

To simplify this tutorial, only the default blob container and the default storage account are used. In practice, the data files are usually stored in a designated storage account.

**To create an Azure Storage account**

1. Sign in to the [Azure Management Portal][azure-management-portal].
2. Click **NEW** on the lower left corner, point to **DATA SERVICES**, point to **STORAGE**, and then click **QUICK CREATE**.

	![HDI.StorageAccount.QuickCreate][image-hdi-storageaccount-quickcreate]

3. Enter **URL**, **LOCATION** and **REPLICATION**, and then click **CREATE STORAGE ACCOUNT**. Affinity groups are not supported. You will see the new storage account in the storage list.


	>[WA.COM NOTE]  An HDInsight cluster and the associated Azure storage account must be in the same datacenter. So, make sure you create your storage account in the locations supported for the cluster, which are:  **East Asia**, **Southeast Asia**, **North Europe**, **West Europe**, **East US**, **West US**, **North Central US**, **South Central US**.

 
4. Wait until the **STATUS** of the new storage account is changed to **Online**.
5. Select the new storage account from the list and click **MANAGE ACCESS KEYS** from the bottom of the page.
7. Make a note of the **STORAGE ACCOUNT NAME** and the **PRIMARY ACCESS KEY** (or the **SECONDARY ACCESS KEY**. Either of the keys work).  You will need them later in the tutorial.


For more information, see
[How to Create a Storage Account][azure-create-storageaccount] and [Use Azure Blob Storage with HDInsight][hdinsight-storage].
	
##<a name="provision"></a>Provision an HDInsight cluster

In this section you provision a HDInsight cluster version 3.0, which is based on Hadoop version 2.2. If you want to provision an HDInsight cluster with Hadoop version 2.4, click on the specific version tab at the beginning of this article. You can also create Hadoop clusters for other versions using HDInsight PowerShell cmdlets or by using the HDInsight .NET SDK. For instructions, see [Provision HDInsight clusters using custom options][hdinsight-provision]. For information about different HDInsight versions and their SLA, see [HDInsight component versioning](http://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/) page.

**To provision an HDInsight cluster** 

1. Sign in to the [Azure Management Portal][azure-management-portal]. 

2. Click **HDInsight** on the left to list the status of the clusters in your account. In the following screenshot, there are no existing HDInsight clusters.

	![HDI.ClusterStatus][image-hdi-clusterstatus]

3. Click **NEW** on the lower left side, click **Data Services**, click **HDInsight**, and then click **Custom Create**.

	![HDI.CustomCreateCluster][image-hdi-customcreatecluster]


    Enter or select the values as shown in the image above and then click the right arrow.

4. On the **Configure Cluster** page, enter or select the following values:

	<table border="1">
	<tr><th>Name</th><th>Value</th></tr>
	<tr><td>Data nodes</td><td>Number of data nodes you want to deploy. For testing purposes, create a single node cluster. <br />The cluster size limit varies for Azure subscriptions. Contact Azure billing support to increase the limit.</td></tr>
	<tr><td>Region/Virtual network</td><td>Choose the same region as the storage account you created in the last procedure. HDInsight requires the storage account located in the same region. Later in the configuration, you can only choose a storage account that is in the same region as you specified here. The available regions are: <strong>East Asia</strong>, <strong>Southeast Asia</strong>, <strong>North Europe</strong>, <strong>West Europe</strong>, <strong>East US</strong>, <strong>West US</strong>, <strong>North Central US</strong>, <strong>South Central US</strong></td></tr>
	</table>

	Click the right arrow.

5. On the **Configure Cluster User** page, provide the following values:

    ![HDI.CustomCreateCluster.ClusterUser][image-hdi-customcreatecluster-clusteruser]
	
    <table border='1'>
		<tr><th>Property</th><th>Value</th></tr>
		<tr><td>User name</td>
			<td>Specify the HDInsight cluster user name.</td></tr>
		<tr><td>Password/Confirm Password</td>
			<td>Specify the HDInsight cluster user password.</td></tr>
		<tr><td>Enter Hive/Oozie Metastore</td>
			<td>Select this checkbox to specify a SQL database on the same data center as the cluster, to be used as the Hive/Oozie metastore. This is useful if you want to retain the metadata about Hive/Oozie jobs even after a cluster has been deleted.</td></tr>
		<tr><td>Metastore Database</td>
			<td>Specify the Azure SQL database that will be used as the metastore for Hive/OOzie. This SQL database must be in the same data center as the HDInsight cluster. The list box only lists the SQL databases in the same data center as you specified on the <strong>Cluster Details</strong> page.</td></tr>
		<tr><td>Database user</td>
			<td>Specify the SQL database user that will be used to connect to the database.</td></tr>
		<tr><td>Database user password</td>
			<td>Specify the SQL database user password.</td></tr>
	</table>


	>[WA.COM NOTE] The Azure SQL database must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Windows Azure Services**, click **Yes**, and then click **Save**.   


    Click the right arrow.

6. On the **Storage Account** page, provide the following value:

    ![HDI.CustomCreateCluster.StorageAccount][image-hdi-customcreatecluster-storageaccount]

	<table border='1'>
		<tr><th>Property</th><th>Value</th></tr>
		<tr><td>Storage Account</td>
			<td>Specify the Azure storage account that will be used as the default file system for the HDInsight cluster. You can choose one of the three options:
			<ul>
				<li>Use Existing Storage</li>
				<li>Create New Storage</li>
				<li>Use Storage From Another Subscription</li>
			</ul>
			</td></tr>
		<tr><td>Account Name</td>
			<td><ul>
				<li>If you chose to use existing storage, for <strong>Account name</strong>, select an exising storage account. The drop-down only lists the storage accounts located in the same data center where you chose to provision the cluster.</li>
				<li>If you chose <strong>Create new storage</strong> or <strong>Use storage from another subscription</strong> option, you must provide the storage account name.</li>
			</ul></td></tr>
		<tr><td>Account Key</td>
			<td>If you chose the <strong>Use Storage From Another Subscription</strong> option, specify the account key for that storage account.</td></tr>	
		<tr><td>Default container</td>
			<td><p>Specifies the default container on the storage account that is used as the default file system for the HDInsight cluster. If you chose <strong>Use Existing Storage</strong> for the <strong>Storage Account</strong> field, the container is created by default with a the same name as the cluster name. If a container with the name fo the cluster already exists, a sequence number will be appended to the container name. For example, mycontainer1, mycontainer2, and so on.</p>
            <p>If you chose to create a new storage or use storage from another Azure subscription, you must specify the default container name</p>
        </td></tr>
		<tr><td>Additional Storage Accounts</td>
			<td>HDInsight supports multiple storage accounts. There is no limit on the additional storage account that can be used by a cluster. However, if you create a cluster using the Management Portal, you have a limit of seven due to the UI constraints. Each additional storage account you specify adds an extra Storage Account page to the wizard where you can specify the account information. For example, in the screenshot above, one additional storage account is selected, and hence page 5 is added to the dialog.</td></tr>		
	</table>

	Click the right arrow.

7. On the **Storage Account** page, enter the account information for the additional storage account:

	![HDI.CustomCreateCluster.AddOnStorage][image-hdi-customcreatecluster-addonstorage]

    Here again, you have the option to choose from existing storage, create new storage, or use storage from another Azure subscription. The procedure to provide the values is similar to the previous step.

    Click the check mark to start provisioning the cluster. When the provisioning completes, the  status column shows **Running**.

    
##<a name="sample"></a>Run a Hive job

Now that you have an HDInsight cluster provisioned, the next step is to run a Hive job to query a sample Hive table, *hivesampletable*, which comes with HDInsight clusters. The table contains data on mobile device manufacturer, platforms, and models. We query this table to retrieve data for mobile devices by a specific manufacturer.

**To run a Hive job from cluster dashboard**

1. Sign in to the [Azure Management Portal][azure-management-portal]. 
2. Click **HDINSIGHT** from the left pane. You shall see a list of clusters created, including the one you just created in the last section.
3. Click the cluster name where you want to run the Hive job and then click **MANAGE CLUSTER** from the bottom of the page. 
4. It opens a Web page on a different browser tab. Enter the Hadoop user account and password.  The default user name is **admin**; the password is what you entered while provisioning the cluster.  The dashboard looks like :

	![hdi.dashboard][img-hdi-dashboard]

	There are several tabs on the top.  The default tab is **Hive Editor**, while the other tabs are **Job History** and **File Browser**.  Using the dashboard, you can submit Hive queries, check Hadoop job logs, and browse WASB files.

	> [WACOM.NOTE] Note that the URL of the Web page is *&lt;ClusterName&gt;.azurehdinsight.net*. So, instead of opening the dashboard from the Management portal, you can also open the dashboard from a Web browser using the URL.

6. On the **Hive Editor** tab, for **Query Name**, enter **HTC20**.  The query name is the job title.

7. In the query pane, enter the following query: 

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![hdi.dashboard.query.select][img-hdi-dashboard-query-select]

4. Click **Submit**. It takes a few moments to get the results back. The screen refreshes every 30 seconds. You can also click **Refresh** to refresh the screen.

    Once completed, the screen looks like:

	![hdi.dashboard.query.select.result][img-hdi-dashboard-query-select-result]

5. Click the query name on the screen to see the output. Make a note of **Job Start Time (UTC)**. You will need it later. 

    ![hdi.dashboard.query.select.result.output][img-hdi-dashboard-query-select-result-output]

    The page also shows the **Job Output** and the **Job Log**. You also have the option to download the output file (\_stdout) and the log file \(_stderr).

	> [WA.COM NOTE] The **Job Session** table on the **Hive Editor** tab lists completed or running jobs as long as you stay on that tab. The table does not list any jobs if you navigate away from the page. The **Job History** tab maintains a list of all jobs, completed or running. 

**To browse to the output file**

1. From the cluster dashboard, click **File Browser** at the top. 
2. Click your storage account name, click your container name (which is the same as your cluster name), and then click **user**.
3. Click admin and then click the GUID number which has the last modified time a little after the job start time you noted earlier. Make a note of this GUID. You will need it in the next section.


     ![hdi.dashboard.query.browse.output][img-hdi-dashboard-query-browse-output]


##<a name="powerquery"></a>Connect to Microsoft business intelligence tools 

You can use the Power Query add-in for Microsoft Excel to import the job output from HDInsight into Excel, where Microsoft Business Intelligence (BI) tools can be used to further analysis of results. 

You must have Excel 2010 or 2013 installed to complete this part of the tutorial. 

**To download Microsoft Power Query for Excel**

- Download Microsoft Power Query for Microsoft Excel from the [Microsoft Download Center](http://www.microsoft.com/en-us/download/details.aspx?id=39379) and install it.

**To import HDInsight data**

1. Open Excel, and create a new blank workbook.
3. Click the **Power Query** menu, click **From Other Sources**, and then click **From Azure HDInsight**.

	![HDI.GettingStarted.PowerQuery.ImportData][image-hdi-gettingstarted-powerquery-importdata]

3. Enter the **Account Name** of the Azure Blob Storage Account associated with your cluster, and then click **OK**. This is the storage account you created earlier in the tutorial.
4. Enter the **Account Key** for the Azure Blob Storage Account, and then click **Save**. 
5. In the Navigator pane on the right, double-click the Blob storage container name. By default the container name is the same name as the cluster name. 

6. Locate **stdout** in the **Name** column. Verify the GUID in the corresponding Folder Path column matches the GUID you noted down earlier. Click **Binary** on the left of **stdout**.

	![HDI.GettingStarted.PowerQuery.ImportData2][image-hdi-gettingstarted-powerquery-importdata2]

9. Click **Close & Load** in the upper left corner to import the Hive job output into Excel.


##<a name="nextsteps"></a>Next steps
In this tutorial, you have learned how to provision a cluster with HDInsight, run a MapReduce job on it, and import the results into Excel where they can be further processed and graphically displayed using BI tools. To learn more, see the following articles:

- [Get started with the HDInsight Emulator][hdinsight-emulator]
- [Use Azure Blob storage with HDInsight][hdinsight-storage]
- [Administer HDInsight using PowerShell][hdinsight-admin-powershell]
- [Upload data to HDInsight][hdinsight-upload-data]
- [Use MapReduce with HDInsight][hdinsight-use-mapreduce]
- [Use Hive with HDInsight][hdinsight-use-hive]
- [Use Pig with HDInsight][hdinsight-use-pig]
- [Use Oozie with HDInsight][hdinsight-use-oozie]
- [Develop C# Hadoop streaming programs for HDInsight][hdinsight-develop-streaming]
- [Develop Java MapReduce programs for HDInsight][hdinsight-develop-mapreduce]

[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[azure-purchase-options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/en-us/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png

[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png
[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
