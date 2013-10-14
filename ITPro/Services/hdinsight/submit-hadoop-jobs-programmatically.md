<properties linkid="manage-services-hdinsight-submit-hadoop-jobs-programmatically" urlDisplayName="HDInsight Administration" pageTitle="Submit Hadoop jobs programmatically - Windows Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hive, mapreduce, HDInsight .NET SDK, powershell, submit mapreduce jobs, submit hive jobs, development, hadoop, apache" metaDescription="Learn how to programmatically submit Hadoop jobs using the Windows Azure HDInsight Service." umbracoNaviHide="0" disqusComments="1" writer="jgao" editor="cgronlun" manager="paulettm" />

# Submit Hadoop jobs programmatically

In this article, you will learn how to submit MapReduce and Hive jobs using PowerShell and HDInsight .NET SDK.

**Prerequisites:**

Before you begin this article, you must have the following:

* A Windows Azure HDInsight cluster. For instructions, see [Getting started with HDInsight Service][hdinsight-getting-started] or [Provision HDInsight clusters][hdinsight-provision].
* Install and configure PowerShell for HDInsight. For instructions, see [Install and configure PowerShell for HDInsight][hdinsight-configure-powershell].


##In this article

* [Submit MapReduce jobs using PowerShell](#mapreduce-powershell)
* [Submit Hive jobs using PowerShell](#hive-powershell)
* [Submit MapReduce jobs using HDInsight .NET SDK](#mapreduce-sdk)
* [Submit Hive Jobs using HDInsight .NET SDK](#hive-sdk)
* [Next steps](#nextsteps)

##<a id="mapreduce-powershell"></a> Submit MapReduce jobs using PowerShell
Windows Azure PowerShell is a powerful scripting environment that you can use to control and automate the deployment and management of your workloads in Windows Azure. For more information on using PowerShell with HDInsight, see [Administer HDInsight using PowerShell][hdinsight-admin-powershell].

Hadoop MapReduce is a software framework for writing applications which process vast amounts of data. The HDInsight cluster distribution comes with a jar file, located at *\example\jars\hadoop-examples.far*, which contains several MapReduce examples. One of the examples is for counting word frequencies in source files. In this session, you will learn how to use PowerShell from a workstation to run the word count sample. For more information on developing and running MapReduce jobs, see [Using MapReduce with HDInsight][hdinsight-mapreduce].

**To run the word count MapReduce program using PowerShell**

1.	Open **Windows Azure PowerShell**. For instructions of opening Windows Azure PowerShell console window, see the [Install and configure PowerShell for HDInsight][hdinsight-configure-powershell].

3. Set the two variables in the following commands, and then run them:
		
		$subscriptionName = "<SubscriptionName>"   ### Windows Azure subscription name
		$clusterName = "<ClusterName>"             ### HDInsight cluster name

	The subscription is the one you used to create the HDInsight cluster. And the HDInsight cluster is the one you want to use to run the MapReduce job.

4. Run the following command.  It will prompt you to enter the HDInsight user credentials:

		$creds = Get-Credential 
	
	This is the credentials used to run the MapReduce job.
		
5. Run the following commands to create a MapReduce job definition:

		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" 
		$wordCountJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt") 
		$wordCountJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput") 

	There are two arguments. The first one is the source file name, and the second is the output file path. For more information of the WASB prefix, see [Using Windows Azure Blob Storage with HDInsight][hdinsight-storage].

6. Run the following command to run the MapReduce job:

		$wordCountJob = $wordCountJobDefinition | Start-AzureHDInsightJob -Credentials $creds -Cluster $clusterName  

	In addition to the MapReduce job definition, you also provide the HDInsight cluster name where you want to run the MapReduce job, and the credentials. 

7. Run the following command to check the completion of the MapReduce job:

		$wordCountJob | Wait-AzureHDInsightJob -Credentials $creds -WaitTimeoutInSeconds 3600  

8. Run the following command to check any errors with running the MapReduce job:		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $wordCountjob.JobId -StandardError
		
	The following screenshot shows the output of a successful run. Otherwise, you will see some error messages.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]

		
**To retrieve the results of the MapReduce job**

1. Open **Windows Azure PowerShell**.
2. Set the three variables in the following commands, and then run them:

		$subscriptionName = "<SubscriptionName>"       ### Windows Azure subscription name
		
		$storageAccountName = "<StorageAccountName>"   ### Windows Azure storage account name
		$containerName = "<ContainerName>"			   ### Blob storage container name

		The Windows Azure Storage account is the one you specified during the HDInsight cluster provision. The storage account is used to host the Blob container that is used as the default HDInsight cluster file system.  The Blob storage container name usually share the same name as the HDInsight cluster unless you specify a different name when you provision the cluster.

3. Run the following commands to create a Windows Azure storage context object:
		
		### Select the current subscription
		Select-AzureSubscription $subscriptionName

		### Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

	The Select-AzureSubscription is used to set the current subscription in case you have multiple subscriptions, and the default subscription is not the one to use. 

4. Run the following command to download the MapReduce job output from the Blob container to the workstation:

		Get-AzureStorageBlobContent -Container $ContainerName -Blob /example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	The */example/data/WordCountOutput* folder is the output folder specified when you run the MapReduce job. *part-r-00000* is the default file name for MapReduce job output.  The file will be download to the same folder structure on the local folder. For example, in the following sreenshoot, the current folder is the C root folder.  The file will be downloaded to the *C:\example\data\WordCountOutput\* folder.

5. Run the following command to print the MapReduce job output file:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	The MapReduce job produces a file named *part-r-00000* with the words and the counts.  The script uses the findstr command to list all of the words that contains "there".


<div class="dev-callout"> 
<b>Note</b> 
<p>If you open ./example/data/WordCountOutput/part-r-00000, a multi-line output from a MapReduce job, in Notepad, you will notice the line breaks are not renter correctly. This is expected.</p> 
</div>









































































































































##<a id="hive-powershell"></a> Submit Hive jobs using PowerShell
Apache [Hive][apache-hive] provides a means of running MapReduce job through an SQL-like scripting language, called *HiveQL*, which can be applied towards summarization, querying, and analysis of large volumes of data. 

The HDInsight cluster distribution comes with a sample Hive table called *hivesampletable*. In this session, you will use PowerShell to run a Hive job for listing the Hive tables created on HDInsight cluster. For a more information about Hive, see [Using Hive with HDInsight][hdinsight-hive].

**To run a Hive job using PowerShell**

1.	Open **Windows Azure PowerShell**. For instructions of opening Windows Azure PowerShell console window, see [Install and configure PowerShell for HDInsight][hdinsight-configure-powershell].

3. Set the first two variables in the following commands, and then run the commands:
		
		$subscriptionName = "<SubscriptionName>"   ### Windows Azure subscription name
		$clusterName = "<ClusterName>"             ### HDInsight cluster name

		$querystring = "show tables;"

	If you don't know these values, please ask your system administrator.  The querystring is the HiveQL query.

4. Run the following command.  It will prompt you to enter the HDInsight user credentials:

		$creds = Get-Credential 
	
	This is the credentials used to run the Hive job.
		
5. Run the following command to create a Hive job definition:

		$HiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $querystring 

	The $querystring variable is set earlier in the procedure to show hive tables.

6. Run the following command to run the Hive job:

		$HiveJob = Start-AzureHDInsightJob -Credentials $creds -Cluster $clustername -JobDefinition $HiveJobDefinition  

	In addition to the Hive job definition, you also provide the HDInsight cluster name where you want to run the Hive job, and the credentials. 

7. Run the following command to check the completion of the Hive job:

		$HiveJob | Wait-AzureHDInsightJob -Credentials $creds -WaitTimeoutInSeconds 3600

8. Run the following command to check the output of the Hive job:	
	
		Get-AzureHDInsightJobOutput -Cluster $clustername -Subscription $subscriptionname -JobId $HiveJob.JobId -StandardOutput
		
	A clean HDInsight cluster contains only one Hive table. The output shall be:

		hivesampletable






















##<a id="mapreduce-sdk"></a> Submit MapReduce Jobs Using HDInsight .NET SDK
The HDInsight .NET SDK provides .NET client libraries that makes it easier to work with HDInsight clusters from .NET. The HDInsight cluster distribution comes with a jar file, located at *\example\jars\hadoop-examples.far*, which contains several MapReduce examples. One of the examples is for counting word frequencies in source files. In this session, you will learn how to create a .NET application to run the word count sample. For more information on developing and running MapReduce jobs, see [Using MapReduce with HDInsight][hdinsight-mapreduce].


The following procedures are needed to provision an HDInsight cluster using the SDK:

- Install the HDInsight .NET SDK
- Create a console application
- Run the application


**To install the HDInsight .NET SDK**
You can install latest published build of the SDK from [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). The instructions will be shown in the next procedure.

**To create a Visual Studio console application**

1. Open Visual Studio 2012.

2. From the File menu, click **New**, and then click **Project**.

3. From New Project, type or select the following values:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
	</table>

4. Click **OK** to create the project.


5. From the **Tools** menu, click **Library Package Manager**, click **Package Manager Console**.

6. Run the following commands in the console to install the packages.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	This command adds .NET libraries and references to them to the current Visual Studio project.

7. From Solution Explorer, double-click **Program.cs** to open it.

8. Add the following using statements to the top of the file:

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;
		
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Add the following function definition to the class. This function is used to wait for a Hadoop job to complete.

        private static void WaitForJobCompletion(HadoopJobCreationResults jobDetails, IHadoopClient client)
        {
            HadoopJob jobInProgress = client.GetJob(jobDetails.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
9. In the Main() function, copy and paste the following code:
		
		// Set the variables
		string subscriptionID = "<Windows Azure subscription name>";
		string certFrientlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";
		
		string storageAccountName = "<Windows Azure storage account name>";
		string storageAccountKey = "<Windows Azure storage account key>";
		string containerName = "<Blob container name>";
		
	
	These are all of the variables you need to set for the program. You can get the Windows Azure Subscription name from you system administrator. 

	For information on certificate, see [Create and Upload a Management Certificate for Windows Azure][azure-certificate]. An easy way to configure the certificate is to run *Get-AzurePublishSettingsFile* and *Import-AzurePublishSettingsFile* PowerShell cmdlets. They will create and upload the management certificate automatically. After you run the PowerShell cmdlets, you can open *certmgr.msc* from the workstation, and find the certificate by expanding *Personal/Certificates*. The certificate created by the PowerShell cmdlets has *Windows Azure Tools* for both the *Issued To* and the *Issued By* fields.

	The Windows Azure Storage account name is the account you specify when you provision the HDInsight cluster. The default container name is the same as the HDInsight cluster name.
	
9. In the Main() function, append the following code to define the MapReduce job:

		// Define the MapReduce job
		HadoopMapReduceJobCreationDetails mrJobDefinition = new HadoopMapReduceJobCreationDetails(){
		    JarFile = "wasb:///example/jars/hadoop-examples.jar",
		    ApplicationName = "wordcount"
		};
		
		mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
		mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

	There are two arguments. The first one is the source file name, and the second is the output file path. For more information of the WASB prefix, see [Using Windows Azure Blob Storage with HDInsight][hdinsight-storage].
		
9. 	In the Main() function, append the following code to create a HDInsightHadoopCertificateCredentials object:
	
		// Get the certificate object from certificate store using the friendly name to identify it
		X509Store store = new X509Store();
		store.Open(OpenFlags.ReadOnly);
		X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFrientlyName);
		HDInsightHadoopCertificateCredentials creds = new HDInsightHadoopCertificateCredentials(new Guid(subscriptionID), cert, clusterName);
		
10. In the Main() function, append the following code to run the job and wait the job to complete:

		// Create a hadoop client to connect to HDInsight
		var hadoopClient = HadoopClient.Connect(creds);
		
		// Run the MapReduce job
		HadoopJobCreationResults mrJobResults = hadoopClient.CreateMapReduceJob(mrJobDefinition);
		
		// Wait for the job to complete
		WaitForJobCompletion(mrJobResults, hadoopClient);

11. In the Main() function, append the following code to print the MapReduce job output:

		// Print the MapReduce job output
		Stream stream = new MemoryStream();
		
		CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");
		
		blockBlob.DownloadToStream(stream);
		stream.Position = 0;
		
		StreamReader reader = new StreamReader(stream);
		Console.WriteLine(reader.ReadToEnd());
		
        Console.WriteLine("Press ENETER to continue.");
		Console.ReadLine();

	The output folder is specified when you define the MapReduce job. The default file name is *part-r-00000*.

**To run the application**

While the application is open in Visual Studio, press **F5** to run the application. A console window should open and display the status of the application and the application output. 


##<a id="hive-sdk"></a> Submit Hive Jobs Using HDInsight .NET SDK 
The HDInsight cluster distribution comes with a sample Hive table called *hivesampletable*. In this session, you will create a .NET application to run a Hive job for listing the Hive tables created on HDInsight cluster. For a more information on using Hive, see [Using Hive with HDInsight][hdinsight-hive].

The following procedures are needed to provision an HDInsight cluster using the SDK:

- Install the HDInsight .NET SDK
- Create a console application
- Run the application


**To install the HDInsight .NET SDK**
You can install latest published build of the SDK from [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). The instructions will be shown in the next procedure.

**To create a Visual Studio console application**

1. Open Visual Studio 2012.

2. From the File menu, click **New**, and then click **Project**.

3. From New Project, type or select the following values:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td></tr>
	</table>

4. Click **OK** to create the project.


5. From the **Tools** menu, click **Library Package Manager**, click **Package Manager Console**.

6. Run the following commands in the console to install the packages.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	This command adds .NET libraries and references to them to the current Visual Studio project.

7. From Solution Explorer, double-click **Program.cs** to open it.

8. Add the following using statements to the top of the file:

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Add the following function definition to the class. This function is used to wait for a Hadoop job to complete.

        private static void WaitForJobCompletion(HadoopJobCreationResults jobDetails, IHadoopClient client)
        {
            HadoopJob jobInProgress = client.GetJob(jobDetails.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
9. In the Main() function, copy and paste the following code:
		
		// Set the variables
		string subscriptionID = "<Windows Azure subscription name>";

		string clusterName = "<HDInsight cluster name>";
        
		string certfriendlyname = "<certificate friendly name>";		
		
	
	These are all of the variables you need to set for the program. You can get the Windows Azure Subscription ID from you system administrator. 

	For information on certificate, see [Create and Upload a Management Certificate for Windows Azure][azure-certificate]. An easy way to configure the certificate is to run *Get-AzurePublishSettingsFile* and *Import-AzurePublishSettingsFile* PowerShell cmdlets. They will create and upload the management certificate automatically. After you run the PowerShell cmdlets, you can open *certmgr.msc* from the workstation, and find the certificate by expanding *Personal/Certificates*. The certificate created by the PowerShell cmdlets has *Windows Azure Tools* for both the *Issued To* and the *Issued By* fields.
	
9. In the Main() function, append the following code to define the Hive job:

		// define the Hive job
		HadoopHiveJobCreationDetails hiveJobDefinition = new HadoopHiveJobCreationDetails()
		{
		    JobName = "show tables job",
		    StatusFolder = "/ShowTableStatusFolder",
		    Query = "show tables;"
		};

	There are two arguments. The first one is the source file name, and the second is the output file path. For more information of the WASB prefix, see [Using Windows Azure Blob Storage with HDInsight][hdinsight-storage].
		
9. 	In the Main() function, append the following code to create a HDInsightHadoopCertificateCredentials object:
	
		// Get the certificate object from certificate store using the friendly name to identify it
		X509Store store = new X509Store();
		store.Open(OpenFlags.ReadOnly);
		X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfrientlyname);
		HDInsightHadoopCertificateCredentials creds = new HDInsightHadoopCertificateCredentials(new Guid(subscriptionID), cert, clusterName);
		
10. In the Main() function, append the following code to run the job and wait the job to complete:

		// Submit the Hive job
		var hadoopClient = HadoopClient.Connect(creds);
		HadoopJobCreationResults jobResults = hadoopClient.CreateHiveJob(hiveJobDefinition);
		
		// Wait for the job to complete
		WaitForJobCompletion(jobResults, hadoopClient);
		
11. In the Main() function, append the following code to print the Hive job output:

		// Print the Hive job output
		System.IO.Stream stream = hadoopClient.GetJobOutput(jobResults.JobId);
		
		StreamReader reader = new StreamReader(stream);
		Console.WriteLine(reader.ReadToEnd());
		
		Console.WriteLine("Press ENTER to continue.");
		Console.ReadLine();

**To run the application**

While the application is open in Visual Studio, press **F5** to run the application. A console window should open and display the status of the application. The output shall be:

	hivesampletable




##<a id="nextsteps"></a> Next steps
In this article, you have learned several ways to provision an HDInsight cluster. To learn more, see the following articles:

* [Getting Started with Windows Azure HDInsight Service][hdinsight-getting-started]
* [Provision HDInsight clusters][hdinsight-provision]
* [Administer HDInsight using PowerShell][hdinsight-admin-powershell]
* [Using MapReduce with HDInsight][hdinsight-mapreduce]
* [Using Hive with HDInsight][hdinsight-hive]


[azure-certificate]: http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx

[hdinsight-provision]: /en-us/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-mapreduce]: /en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/
[hdinsight-hive]:/en-us/manage/services/hdinsight/using-hive-with-hdinsight/
[hdinsight-pig]: /en-us/manage/services/hdinsight/using-pig-with-hdinsight/

[hdinsight-getting-started]: /en-us/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-storage]: /en-us/manage/services/hdinsight/howto-blob-store/
[hdinsight-admin-powershell]: /en-us/manage/services/hdinsight/administer-hdinsight-powershell/
[hdinsight-configure-powershell]: /en-us/manage/services/hdinsight/configure-powershell-for-hdinsight/ 

[image-hdi-gettingstarted-runmrjob]: ../media/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ../media/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/
