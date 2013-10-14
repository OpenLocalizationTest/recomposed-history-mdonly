<properties linkid="manage-services-hdinsight-upload-data" urlDisplayName="Upload data to HDInsight" pageTitle="How to upload data to HDInsight - Windows Azure Services" metaKeywords="hdinsight, hdinsight upload, hdinsight upload data, upload data azure, Windows Azure blob storage, wasb, wasbs, powershell" metaDescription="Learn how to upload data to the the HDInsight service." umbracoNaviHide="0" disqusComments="1" writer="jgao" editor="cgronlun" manager="paulettm" />

#Upload data to HDInsight

Windows Azure HDInsight Service provides a full featured HDFS file system over Windows Azure Blob storage. It has been designed as an HDFS extension to provide a seamless experience to customers by enabling the full set of components in the Hadoop ecosystem to operate directly on the data it manages. Both Windows Azure Blob storage and HDFS are distinct file systems that are optimized for storage of data and computations on that data. For the benefits of using Windows Azure Blob storage, see [Using Windows Azure Blob Storage with HDInsight][hdinsight-storage]. 

Windows Azure HDInsight clusters are typically deployed to execute MapReduce jobs and are dropped once these jobs have been completed. Keeping the data in the HDFS clusters after computations have been completed would be an expensive way to store this data. Windows Azure Blob storage is a highly available, highly scalable, high capacity, low cost, and shareable storage option for data that is to be processed using HDInsight. Storing data in a Blob enables the HDInsight clusters used for computation to be safely released without losing data. 

Windows Azure Blob storage can either be accessed through [Windows Azure PowerShell][azure-powershell], [Windows Azure Storage Client Library for .NET][azure-storage-client-library] or through explorer tools. Here are some of the tools available:

* [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/)
* [cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Windows Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Windows Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

**Prerequisites:**

Note the following requirements before you begin this article:

* A Windows Azure HDInsight cluster. For instructions, see [Getting started with Windows Azure HDInsight service][hdinsight-getting-started] or [Provision HDInsight clusters][hdinsight-provision].

##In this article

* [Upload data to Windows Azure Blob storage using Windows Azure PowerShell](#powershell)
* [Upload data to Windows Azure Blob storage using Azure Storage Explorer](#storageexplorer)
* [Upload data to Windows Azure Blob storage using Hadoop command line](#commandline)
* [Import data from Windows Azure SQL Database to Windows Azure Blob storage using Sqoop](#sqoop)
* [Access data stored in Windows Azure Blob storage](#blob)

##<a id="powershell"></a>Upload data to Windows Azure Blob storage using Windows Azure PowerShell##

Windows Azure PowerShell is a powerful scripting environment that you can use to control and automate the deployment and management of your workloads in Windows Azure. You can use Windows Azure PowerShell to upload data to Windows Azure Blob storage, so the data can be processed by MapReduce jobs. For information on configuring your workstation to run Windows Azure PowerShell, see [How to install and configure Windows Azure PowerShell][powershell-install-configure].

**To upload a local file to Windows Azure Blob storage**

1. Run Windows Azure PowerShell console window as instructed in [How to install and configure Windows Azure PowerShell][powershell-install-configure].
2. Set the values of the first five variables in the following script:

		# Set the variables.
		$subscriptionName = "<WindowsAzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"
		
		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"
		
		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		# Copy the file from local workstation to the Blob container		
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
		
3. Paste the script into the Windows Azure PowerShell console window to run it.

Blob storage containers store data as key/value pairs, and there is no directory hierarchy. However the ‘/’ character can be used within the key name to make it appear as if a file is stored within a directory structure. For example, a blob’s key may be ‘input/log1.txt’. No actual ‘input’ directory exists, but due to the presence of the ‘/’ character in the key name, it has the appearance of a file path. In the previous script, you can give the file a folder structure by setting the $blobname variable. For example *$blobname="myfolder/myfile.txt".

Using Windows Azure Explorer tools, you may notice some 0 bytes files. These files serve two purposes:

- In case of empty folders, they serve as a marker of the existence of the folder. WASB is clever enough to know that if a blob exists called foo/bar then there is a folder called foo. But if you want to have an empty folder called foo, then the only way to signify that is by having this special 0-byte file in place.
- They hold some special metadata needed by the Hadoop file system, notably the permissions and owners for the folders.








##<a id="storageexplorer"></a>Upload data to Windows Azure Blob storage using Azure Storage Explorer

*Azure Storage Explorer* is a useful tool for inspecting and altering the data in your Windows Azure Storage. It is a free tool that can be downloaded from [http://azurestorageexplorer.codeplex.com/](http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Before using the tool, you must know your Windows Azure storage account name and account key. For the instructions for get the information, see the *How to: View, copy and regenerate storage access keys* section of [How to Manage Storage Accounts][]azure-storage-account]. For the instructions for get the information, see the *How to: View, copy and regenerate storage access keys* section of [How to Manage Storage Accounts][azure-storage-account]. 

1. Run Azure Storage Explorer.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Click **Add Account**. After an account is added to Azure Storage Explorer, you don't need to go through this step again. 

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Enter **Storage account name** and **Storage account key**, and then click **Add Storage Account**. You can add multiple storage accounts, each account will be displayed on a tab. 
4. From **Storage Type**, click **Blobs** to display the Windows Azure Blob storage of the account.

	![HDI.ASEBlob][image-ase-blob]

5. From **Container**, click the container that is associated to your HDInsight cluster. When you create an HDInsight cluster, you must specify a container.  Otherwise, the cluster creation process creates one for you.
6. From **Blob**, click **Upload**.
7. Specify a file to upload, and then click **Open**.








































































##<a id="commandline"></a> Upload data to Windows Azure Blob storage using Hadoop Command Line

To use Hadoop command line, you must first connect to the cluster using remote desktop. 



1. Sign in to the [Management Portal][azure-management-portal].
2. Click **HDINSIGHT**. You will see a list of deployed Hadoop clusters.
3. Click the HDInsight cluster where you want to upload data.
4. Click **CONFIGURATION** from the top of the page.
5. Click **ENABLE REMOTE** if you haven't enabled remote desktop, and follow the instructions.  Otherwise, skip to the next step.
4. Click **CONNECT** on the bottom of the page.
7. Click **Open**.
8. Enter your credentials, and then click **OK**.
9. Click **Yes**.
10. From the desktop, click **Hadoop Command Line**.
12. The following sample demonstrates how to copy the davinci.txt file from the C:\temp\ directory to the /example/data directory.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	Because the default file system is on Windows Azure Blob storage, /example/datadavinci.txt is actually on Windows Azure Blob storage.  You can also refer to the file as:

		wasb:///example/data/davinci.txt 

	or

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	The FQDN is required when you use wasbs.

13. Use the following command to list the uploaded files:

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Import data to HDFS from SQL Database/SQL Server using Sqoop

Sqoop is a tool designed to transfer data between Hadoop and relational databases. You can use it to import data from a relational database management system (RDBMS) such as SQL or MySQL or Oracle into the Hadoop Distributed File System (HDFS), transform the data in Hadoop with MapReduce or Hive, and then export the data back into a RDBMS. For more information, see [Sqoop User Guide][apache-sqoop-guide].

Before importing data, you must know the Windows Azure SQL Database server name, database account name, account password and database name. You must also configure a firewall rule for the database server to allow connections from your HDInsight cluster head node. For instruction on creating SQL database and configuring firewall rules, see [How to use Windows Azure SQL Database in .NET applications][sqldatabase-howto]. To obtain the outward facing IP Address for your HDInsight cluster head node, you can use Remote Desktop to connect to the head node, and then browse to [www.whatismyip.com][whatismyip].

1. Sign in to the [Management Portal][azure-management-portal].
2. Click **HDINSIGHT**. You will see a list of deployed Hadoop clusters.
3. Click the Hadoop cluster where you want to upload data.
4. Click **Connect** on the bottom of the page.
7. Click **Open**.
8. Enter your credentials, and then click **OK**.
9. Click **Yes**.
10. From the desktop, click **Hadoop Command Line**.
12. Run the following commands:

		cd %sqoop_home%\bin
		sqoop import 
			--connect "jdbc:sqlserver://s6ok0p9kft.database.windows.net;username=user1@s6ok0p9kft;password=Pass@word1;database=AdventureWorks2012" 
			--table Sales.SalesOrderDetail 
			--columns "SalesOrderID,SalesOrderDetailID,CarrierTrackingNumber,OrderQty,ProductID,SpecialOfferID,UnitPrice,UnitPriceDiscount,LineTotal" 
			--target-dir /data/lineitemdata 
			-m 1
		hadoop fs -tail /data/lineitemdata/part-m-00000

	In the command, the SQL database server is *s6ok0p9kft*, username is *user1*, password is *Pass@word1*, and the database is *AdventureWorks2012*.  

Note: When specifying an escape character as delimiter with the arguments *--input-fields-terminated-by* and *--input-fields-terminated-by*, do not put quotes around the escape character.  For example. 

		sqoop export 
			--connect "jdbc:sqlserver://localhost;username=sa;password=abc;database=AdventureWorks2012" 
			--table Result 
			--export-dir /hive/warehouse/result 
			--input-fields-terminated-by \t 
			--input-lines-terminated-by \n

##<a id="blob"></a>Access data stored in Windows Azure Blob storage

Data stored in Windows Azure Blob Storage can be accessed directly by prefixing the protocol scheme of the URI for the assets you are accessing with WASB://. To secure the connection, use WASBS://. The scheme for accessing data in Windows Azure Blob Storage is:

	WASB[S]://[<container>@]<accountname>.blob.core.windows.net/<path>

The following is a sample PowerShell script for submitting a MapReduce job:

	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" 
	$wordCountJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt") 
	$wordCountJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput") 
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Credentials $creds -Cluster $clustername  | Wait-AzureHDInsightJob -Credentials $creds -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clustername -Subscription $subscriptionname -JobId $_.JobId -StandardError}

For more information on accessing the files stored in Windows Azure Blob storage, see [Using Windows Azure Blob Storage with HDInsight][hdinsight-storage].

## Next steps
Now that you understand how to get data into HDInsight Service, use the following articles to learn how to perform analysis:

* [Getting Started with Windows Azure HDInsight Service][hdinsight-getting-started]
* [Submit Hadoop jobs programmatically][hdinsight-submit-jobs]
* [Using MapReduce with HDInsight][hdinsight-mapreduce]
* [Using Hive with HDInsight][hdinsight-hive]
* [Using Pig with HDInsight][hdinsight-pig]


[powershell-install-configure]: /en-us/manage/install-and-configure-windows-powershell/

[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/en-us/library/windowsazure/jj152841.aspx
[azure-storage-client-library]: /en-us/develop/net/how-to-guides/blob-storage/
[azure-storage-account]: /en-us/manage/services/storage/how-to-manage-a-storage-account/

[hdinsight-storage]: /en-us/manage/services/hdinsight/howto-blob-store/
[hdinsight-submit-jobs]: /en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-getting-started]: /en-us/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-mapreduce]: /en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/
[hdinsight-hive]: /en-us/manage/services/hdinsight/using-hive-with-hdinsight/
[hdinsight-pig]: /en-us/manage/services/hdinsight/using-pig-with-hdinsight/
[hdinsight-provision]: /en-us/manage/services/hdinsight/provision-hdinsight-clusters/

[sqldatabase-howto]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/sql-database/

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.2/SqoopUserGuide.html

[whatismyip]: http://www.whatismyip.com
[image-azure-storage-explorer]: ../media/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ../media/HDI.ASEAddAccount.png
[image-ase-blob]: ../media/HDI.ASEBlob.png

