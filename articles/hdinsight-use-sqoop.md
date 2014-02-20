<properties linkid="manage-services-hdinsight-use-sqoop" urlDisplayName="Use Sqoop with HDInsight Samples" pageTitle="Use Sqoop with HDInsight | Windows Azure" metaKeywords="" description="Learn how to run Sqoop import and export on HDInsight." umbracoNaviHide="0" disqusComments="1" writer="jgao" editor="cgronlun" manager="paulettm" title="Use Sqoop with HDInsight" />

# Use Sqoop with HDInsight
 
Learn how to use Windows Azure PowerShell from a workstation to run Sqoop import and export between an HDInsight cluster and a Windows Azure SQL database.

**Estimated time to complete:** 30 minutes


##In this article

- [What is Sqoop?](#whatissqoop)
- [Prerequisites](#prerequisites)
- [Understand the tutorial scenario](#scenario)
- [Prepare the tutorial](#prepare)
- [Use Sqoop export](#export)
- [Use Sqoop import](#import)
- [Clean up the tutorial](#cleanup)
- [Next steps](#nextsteps)


## <a id="whatissqoop"></a> What is Sqoop?

While Hadoop is a natural choice for processing unstructured and semi-structured data, such as logs and files, there may also be a need to process structured data stored in relational databases.

Sqoop is a tool designed to transfer data between Hadoop clusters and relational databases. You can use it to import data from a relational database management system (RDBMS) such as SQL or MySQL or Oracle into the Hadoop Distributed File System (HDFS), transform the data in Hadoop with MapReduce or Hive, and then export the data back into an RDBMS. In this tutorial, you are using a SQL Database for your relational database.

Sqoop is an open source software product of Cloudera, Inc. Software development for Sqoop moved in 2011 from gitHub to the [Apache Sqoop](http://sqoop.apache.org/) site.

For supported Sqoop versions on HDInsigth clusters, see [What's new in the cluster versions provided by HDInsight?][hdinsight-versions].




##<a id="prerequisites"></a>Prerequisites

Before you begin this tutorial, you must have the following:

- An Windows Azure HDInsight cluster. For instructions on cluster provision, see [Get started using HDInsight][hdinsight-get-started] or [Provision HDInsight clusters][hdinsight-provision]. You will need the following data to go through the tutorial:

	<table>
	<tr><th>Cluster property</th><th>PowerShell variable name</th><th>Value</th><th>Description</th></tr>
	<tr><td>HDInsight cluster name</td><td>$clusterName</td><td></td><td></td></tr>
	<tr><td>Azure storage account name</td><td>$storageAccountName</td><td></td><td>This is the storage account used as the default HDInsight file system.</td></tr>
	<tr><td>Azure Blob container name</td><td>$containerName</td><td></td><td>This is the blob storage container used as the default HDInsight file system.</td></tr>
	<tr><td></td><td></td><td></td><td></td></tr>
	</table>

- A Windows Azure SQL Database. This article provides a PowerShell script to create the SQL database table needed for this tutorial.  For instructions on creating a SQL database, see [Get started using Windows Azure SQL database][sqldatabase-get-started]. 

	<table>
	<tr><th>SQL database property</th><th>PowerShell variable name</th><th>Value</th><th>Description</th></tr>
	<tr><td>SQL database server name</td><td>$sqlDatabaseServer</td><td></td><td></td></tr>
	<tr><td>SQL database user name</td><td>$sqlDatabaseUsername</td><td></td><td></td></tr>
	<tr><td>Sql database user password</td><td>$sqlDatabasePassword</td><td></td><td></td></tr>
	<tr><td>SQL database name</td><td>$sqlDatabaseName</td><td></td><td></td></tr>
	</table>

> [WACOM.NOTE] Fill the values into the tableS.  It will be helpful for going through this tutorial.

## <a id="scenario"></a>Understand the scenario
HDInsight cluster comes with some sample data. You will use the following two:

- a log4j log file located at */example/data/sample.log*. Here are some log4j logs:

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656

- a hive table named *hivesampletable* with the data file located at */hive/warehouse/hivesampletable*. The schema of the table is:

	<table>
	<tr><th>Field</th><th>Type</th></tr>
	<tr><td>clientid</td><td>string</td></tr>
	<tr><td>querytime</td><td>string</td></tr>
	<tr><td>market</td><td>string</td></tr>
	<tr><td>deviceplatform</td><td>string</td></tr>
	<tr><td>devicemake</td><td>string</td></tr>
	<tr><td>devicemodel</td><td>string</td></tr>
	<tr><td>state</td><td>string</td></tr>
	<tr><td>country</td><td>string</td></tr>
	<tr><td>querydwelltime</td><td>double</td></tr>
	<tr><td>sessionid</td><td>bigint</td></tr>
	<tr><td>sessionpagevieworder</td><td>bigint</td></tr>
	</table>

You will first export both sample.log and hivesampletable to SQL database, and then import the table containing the sample.log data back to HDInsight using the following path:

	/tutorials/usesqoop/importeddata

###Understand HDInsight storage

HDInsight uses Windows Azure Blob storage for data storage.  It is called *WASB* or *Windows Azure Storage - Blob*. WASB is Microsoft's implementation of HDFS on Windows Azure Blob storage. For more information see [Use Windows Azure Blob storage with HDInsight][hdinsight-storage]. 

When you provision an HDInsight cluster, a Blob storage container is designated as the default file system, just like in HDFS. In addition to this container, you can add additional containers from either the same Windows Azure storage account or different Windows Azure storage accounts during the provision process. For instructions on adding additional storage accounts, see [Provision HDInsight clusters][hdinsight-provision]. To simply the PowerShell script used in this tutorial, all of the files are stored in the default file system container, located at */tutorials/useoozie*. By default this container has the same name as the HDInsight cluster name. 
The WASB syntax is:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Only the *wasb://* syntax is supported in HDInsight cluster version 3.0. The older *asv://* syntax is supported in HDInsight 2.1 and 1.6 clusters, but it is not supported in HDInsight 3.0 clusters and it will not be supported in later versions.

> [WACOM.NOTE] The WASB path is virtual path.  For more information see [Use Windows Azure Blob storage with HDInsight][hdinsight-storage]. 

For a file stored in the default file system container. it can be accessed from HDInsight using any of the following URIs (use sample.log as an example):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

If you want to access the file directly from the storage account, the blob name for the file is:

	example/data/sample.log


##<a id="prepare"></a>Prepare the tutorial

You will create two SQl database tables used by Sqoop export later in the tutorial. You will also need to pre-process the sample.log files before it can be processed by Sqoop.

**To create a SQL database tables**

1. Open Windows PowerShell ISE (On Windows 8 Start screen, type **PowerShell_ISE** and then click **Windows PowerShell ISE**. See [Start Windows PowerShell on Windows 8 and Windows][powershell-start]).

2. Copy the following script into the script pane, and then set the first four variables:
		
		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>" 
		$sqlDatabaseUsername = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>" 

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	See the [Prerequisite](#prerequisite) section in this tutorial. 

3. Append the following script into the script pane. These are the SQL statements defining the two tables and their clustered indexes.  SQL Database requires clustered index.

		# SQL query strings for creating tables and clustered indexes
		$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
		    [t1] [nvarchar](50), 
		    [t2] [nvarchar](50), 
		    [t3] [nvarchar](50), 
		    [t4] [nvarchar](50), 
		    [t5] [nvarchar](50), 
		    [t6] [nvarchar](50), 
		    [t7] [nvarchar](50))"
		
		$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
		
		$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
		[clientid] [nvarchar](50), 
		[querytime] [nvarchar](50), 
		[market] [nvarchar](50), 
		[deviceplatform] [nvarchar](50), 
		[devicemake] [nvarchar](50), 
		[devicemodel] [nvarchar](50), 
		[state] [nvarchar](50), 
		[country] [nvarchar](50), 
		[querydwelltime] [float],
		[sessionid] [bigint],
		[sessionpagevieworder][bigint])"
		
		$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

	

4. Append the following script into the script pane for running the SQL commands:

		Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = $sqlDatabaseConnectionString
		$conn.Open()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.Connection = $conn
		$cmd.CommandText = $cmdCreateLog4jTable
		$ret = $cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateLog4jClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd.CommandText = $cmdCreateMobileTable
		$cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Close connection ..." -ForegroundColor Green		
		$conn.close()
		
		Write-Host "Done" -ForegroundColor Green
	
5. Click **Run Script** or press **F5** to run the script. 

In this tutorial, you will export a log4j log file (delimited file) and a Hive table to SQL Database.  The delimited file is */example/data/sample.log*. Earlier in the tutorial, you see a few samples of log4j logs. In the log file, there are some empty lines and some other lines similar to:

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

You might want to remove *java.lang.Exception:* from the row, so the record can be parsed correctly. 

Sqoop export will fail if there is an empty string, or a string with few number of elements than the number of fields defined in the SQL database table. The log4jlogs table has 7 string type fields.

**To pre-process the sample.log file**

1. Open Windows PowerShell ISE.
2. In the bottom pane, run the following command to connect to your Windows Azure subscription:

		Add-AzureAccount

	You will be prompted to enter your Windows Azure account credentials.

3. Copy the following script into the script pane, and then set the first two variables:
		
		$storageAccountName = "<WindowsAzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	See the [Prerequisite](#prerequisite) section in this tutorial.
 
4. Append the following script into the script pane:

		# Define the connection string
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		# Create block blob objects referencing the source and destination blob.
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		
		# Define a MemoryStream and a StreamReader for reading from the source file
		$stream = New-Object System.IO.MemoryStream
		$stream = $sourceBlob.OpenRead()
		$sReader = New-Object System.IO.StreamReader($stream)
		
		# Define a MemoryStream and a StreamWriter for writing into the destination file
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		
		# process the source blob
		$exString = "java.lang.Exception:"
		while(-Not $sReader.EndOfStream){
		    $line = $sReader.ReadLine()
		    $split = $line.Split(" ")
		
		    # remove the "java.lang.Exception" from the first element of the array
		    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
		    if ($split[0] -eq $exString){
		        #create a new ArrayList to remove $split[0]
		        $newArray = [System.Collections.ArrayList] $split
		        $newArray.Remove($exString)
		
		        # update $split and $line
		        $split = $newArray
		        $line = $newArray -join(" ")
		    }
		
		    # remove the lines that has less than 7 elements
		    if ($split.count -ge 7){
		        write-host $line
		        $writeStream.WriteLine($line)
		    }
		}
		
		# Write to the destination blob
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream)

5. Click **Run Script** or press **F5** to run the script.  











































































##<a id="export"></a>Use Sqoop export


**To export the Hive table**

1. Open Windows PowerShell ISE.
2. In the bottom pane, run the following command to connect to your Windows Azure subscription:

		Add-AzureAccount

	You will be prompted to enter your Windows Azure account credentials.

3. Copy the following script into the script pane, and then set the first seven variables:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<WindowsAzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"
		
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_log4j = "/tutorials/usesqoop/data/sample.log"
	
	See the [Prerequisite](#prerequisite) section in this tutorial. 

4. Append the following script into the script pane:

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Click **Run Script** or press **F5** to run the script.  

**To export the log4j log file**

1. Open Windows PowerShell ISE.
2. In the bottom pane, run the following command to connect to your Windows Azure subscription:

		Add-AzureAccount

	You will be prompted to enter your Windows Azure account credentials.

3. Copy the following script into the script pane, and then set the first seven variables:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<WindowsAzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"
		
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	See the [Prerequisite](#prerequisite) section in this tutorial. 

4. Append the following script into the script pane:
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
		
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Click **Run Script** or press **F5** to run the script.   










##<a id="next-steps"></a>Next Steps

In this tutorial, you saw how to transfer data between an HDInsight cluster and a Windows Azure SQL database.  To learn more, see:

 

