<properties linkid="manage-services-hdinsight-using-mapreduce" urlDisplayName="Using MapReduce" pageTitle="Using MapReduce with HDInsight - Windows Azure tutorial" metaKeywords="using mapreduce, mapreduce hdinsight, mapreduce azure" metaDescription="Learn how to use MapReduce with HDInsight" metaCanonical="" umbracoNaviHide="0" disqusComments="1" writer="jgao" editor="cgronlun" manager="paulettm" />


# Using MapReduce with HDInsight#

Hadoop MapReduce is a software framework for writing applications which process vast amounts of data. In this tutorial, you will create a Hadoop MapReduce job in Java, and execute the job on a Windows Azure HDInsight cluster to process a semi-structured Apache *log4j* log file stored in Windows Azure Blob storage. Windows Azure Blob storage provides a full featured HDFS file system over Windows Azure Blob storage.  

**Prerequisites:**

Note the following requirements before you begin this article:

* A Windows Azure HDInsight cluster. For instructions, see [Getting started with Windows Azure HDInsight service][hdinsight-getting-started] or [Provision HDInsight clusters][hdinsight-provision].
* Install and configure PowerShell for HDInsight. For instructions, see [Install and configure PowerShell for HDInsight][hdinsight-configure-powershell].

**Estimated time to complete:** 30 minutes

## In this Article
* [Big Data and Hadoop MapReduce](#mapreduce)
* [Upload data files to the blob storage](#uploaddata)
* [Connect to an HDInsight cluster](#connect)
* [Create a MapReduce job](#createjob)
* [Run the MapReduce job using PowerShell](#runjobpowershell)
* [Run the MapReduce job using Hadoop command line](#runjob)
* [Tutorial clean up](#cleanup)
* [Next steps](#nextsteps)

##<a id="mapreduce"></a>  Big Data and Hadoop MapReduce
Log files are a good example of big data. Most applications save errors, exceptions and other coded issues in log files. These log files contain a wealth of data that must be processed and mined, and they can get large in size. Working with big data is difficult using relational databases with statistics and visualization packages. Hadoop provides a MapReduce framework for writing applications that process large amounts of structured and semi-structured data in parallel across large clusters of machines in a very reliable and fault-tolerant manner.

[Apache Log4j](http://en.wikipedia.org/wiki/Log4j) is a logging utility. Each log inside a file contains a *log level* field to show the type and the severity. For example:

	2012-02-03 20:26:41 SampleClass3 [TRACE] verbose detail for id 1527353937

This MapReduce job takes a log4j log file as input, and generates an output file that contains the log level along with its frequency count.  The following is a sample output file:

	[TRACE] 8
	[DEBUG] 4
	[INFO]  1
	[WARN]  1
	[ERROR] 1
	[FATAL] 1

The following figure is the visual representation of what you will accomplish in this tutorial:

![HDI.VisualObjective](../media/HDI.VisualObject.gif "Visual Objective")

In the figure, the Map program performs filtering and sorting; and the Reduce program performs a summary operation. 
 
##<a id="uploaddata"></a>Upload data files to the Blob storage

HDInsight uses Windows Azure Blob storage container as the default file system.  For more information, see [Using Windows Azure Blob Storage with HDInsight][hdinsight-storage]. In this tutorial you will use a log4j sample file distributed with the HDInsight cluster stored in *\example\data\sample.log*. For information on uploading data, see [How to Upload Data to HDInsight][hdinsight-upload-data].

To access files, use the following syntax: 

		wasb[s]://[[<containername>@]<storageaccountname>.blob.core.windows.net]/<path>/<filename>

For example:

		wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

replace *mycontainer* with the container name, and *mystorage* with the Blob storage account name. 

Because the file is stored in the default file system, you can also access the file using the following:

		wasb:///example/data/sample.log
		/example/data/sample.log



##<a id="connect"></a>Connect to an HDInsight cluster
You must have an HDInsight cluster provisioned before you can work on this tutorial. For information on provision an HDInsight cluster see [How to Administer HDInsight Service](/en-us/manage/services/hdinsight/howto-administer-hdinsight/) or [Getting Started with Windows Azure HDInsight Service](/en-us/manage/services/hdinsight/get-started-hdinsight/).

1. Sign in to the [Management Portal](https://manage.windowsazure.com).
2. Click **HDINSIGHT** on the left. You shall see a list of deployed Hadoop clusters.
3. Click the name of the HDInsight cluster where you want to run the MapReduce job.
4. Click **CONFIGURATION** on the top.
5. Click **ENABLE REMOTE** if remote desktop hasn't been enabled. Otherwise skip to step 7.
6. Enter **USER NAME**, **PASSWORD**, and **EXPIRES ON**, and then click **Complete** (the check sign).
4. Click **Connect** on the bottom to connect to the remote desktop.
5. Click **Open**.
6. Click **Connect**.
7. Click **Yes**.
8. Enter your credentials, and then press **ENTER**.
9. From Desktop, click **Hadoop Command Line**. You will use Hadoop command line to execute all of the commands in this tutorial. 
10. Run the following command to list and verify the sample.log file you uploaded to Windows Azure Blob storage: 

		hadoop fs -ls wasb:///example/data/sample.log

	The wasb syntax is for listing the files in the default file system.  To access files in other containers, use the following syntax: 

		hadoop fs -ls wasb[s]://[[<containername>@]<storageaccountname>.blob.core.windows.net]/<path>

	For example, you can list the same file using the following command:

		hadoop fs -ls wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/data/sample.log

	Replace *&lt;containername&gt;* with the container name, and *&lt;storageaccountname&gt;* with the Blob Storage account name. 

	Because the file is located on the default file system, the same result can also be retrieved by using the following command:

		hadoop fs -ls /example/data/sample.log
	 
	To use wabs, you must provide the FQDN. For example to access sample.log on the default file system: 

		hadoop fs -ls wasbs://<containername>@<storageaccountname>.blob.core.windows.net/example/data/sample.log

	

##<a id="createjob"></a> Create the MapReduce job 
The Java programming language is used in this sample. Hadoop Streaming allows developers to use virtually any programming language to create MapReduces jobs.

1. From Hadoop command line, run the following commands to make a directory and change directory to the folder:

		c:
		mkdir \Tutorials
		cd \Tutorials

2. Run the following command to create a java file in the C:\Tutorials folder:

		notepad log4jMapReduce.java

	<div class="dev-callout"> 
	<b>Note</b> 
	<p>The class name is hard-coded in the program. If you want to change the file name,  you must update the java program accordingly.</p> 
	</div>

3. Click **Yes** to create a new file.

4. Copy and paste the following java program into the Notepad window.

		import java.io.IOException;
		import java.util.Iterator;
		import java.util.regex.Matcher;
		import java.util.regex.Pattern;
	
		import org.apache.hadoop.fs.Path;
		import org.apache.hadoop.io.IntWritable;
		import org.apache.hadoop.io.LongWritable;
		import org.apache.hadoop.io.Text;
		import org.apache.hadoop.mapred.FileInputFormat;
		import org.apache.hadoop.mapred.FileOutputFormat;
		import org.apache.hadoop.mapred.JobClient;
		import org.apache.hadoop.mapred.JobConf;
		import org.apache.hadoop.mapred.MapReduceBase;
		import org.apache.hadoop.mapred.Mapper;
		import org.apache.hadoop.mapred.OutputCollector;
		import org.apache.hadoop.mapred.Reducer;
		import org.apache.hadoop.mapred.Reporter;
		import org.apache.hadoop.mapred.TextInputFormat;
		import org.apache.hadoop.mapred.TextOutputFormat;
	
		public class log4jMapReduce
		{
	
			//The Mapper
	      	public static class Map extends MapReduceBase implements Mapper<LongWritable, Text, Text, IntWritable>
	      	{     
		        private static final Pattern pattern = Pattern.compile("(TRACE)|(DEBUG)|(INFO)|(WARN)|(ERROR)|(FATAL)"); 
	        	private static final IntWritable accumulator = new IntWritable(1); 
	        	private Text logLevel = new Text();
	
	        	public void map(LongWritable key, Text value, OutputCollector<Text, IntWritable> collector, Reporter reporter) throws IOException 
				{
		     		// split on space, '[', and ']'
		        	final String[] tokens = value.toString().split("[ \\[\\]]"); 
		
		        	if(tokens != null)
		        	{
			            //now find the log level token
		            	for(final String token : tokens) 
		            	{
			                final Matcher matcher = pattern.matcher(token);
		                	//log level found
		                	if(matcher.matches()) 
		                	{
			                    logLevel.set(token);
								//Create the key value pairs
								collector.collect(logLevel, accumulator);
		                	}                                                           
		            	}
		        	}                       
	        	}
	      	}
	 
	  		//The Reducer
	    	public static class Reduce extends MapReduceBase implements Reducer<Text, IntWritable, Text, IntWritable>
	    	{
			    public void reduce(Text key, Iterator<IntWritable> values, OutputCollector<Text, IntWritable> collector,Reporter reporter) throws IOException
		    	{
			        int count = 0;
					//code to aggregate the occurrence
		        	while(values.hasNext())
		        	{
		                    	count += values.next().get();
		        	}
			
		        	System.out.println(key +  "\t" + count);
		        	collector.collect(key, new IntWritable(count));
		    	}
	   		}
	
			//The java main method to execute the MapReduce job
			public static void main(String[] args) throws Exception
			{
				//Code to create a new Job specifying the MapReduce class
		    	final JobConf conf = new JobConf(log4jMapReduce.class);
		    	conf.setOutputKeyClass(Text.class);
		    	conf.setOutputValueClass(IntWritable.class);
		    	conf.setMapperClass(Map.class);
		
				// Combiner is commented out – to be used in bonus activity
		
			    //conf.setCombinerClass(Reduce.class);
		    	conf.setReducerClass(Reduce.class);
		    	conf.setInputFormat(TextInputFormat.class);
		    	conf.setOutputFormat(TextOutputFormat.class);
		
				//File Input argument passed as a command line argument
		    	FileInputFormat.setInputPaths(conf, new Path(args[0]));
		
				//File Output argument passed as a command line argument
			    FileOutputFormat.setOutputPath(conf, new Path(args[1]));
		
				//statement to execute the job 
			    JobClient.runJob(conf);
			}
		}

5. Press **CTRL+S** to save the file.
6. Close Notepad.
7. Verify your current folder is C:\Tutorials.

8. Compile the java file using the following command:

		C:\apps\dist\java\bin\javac -classpath %HADOOP_HOME%\hadoop-core-1.2.0.1.3.0.1-0302.jar log4jMapReduce.java
	
	You must to hadoop-core-&lt;YourVersion>.jar, and the version must match yours. To find out the version, you can run the following command from Hadoop command line:

		hadoop version

	

9. Create a log4jMapReduce.jar file containing the Hadoop class files:

		C:\apps\dist\java\bin\jar -cvf log4jMapReduce.jar *.class
 
	After executing the jar command, you will have the following files in the C:\Tutorials directory:

		log4jMapReduce$Map.class
		log4jMapReduce$Reduce.class
		log4jMapReduce.class
		log4jMapReduce.jar
		log4jMapReduce.java

10. Copy the file to HDFS so you can use PowerShell to submit a MapReduce job with the jar file:

		hadoop fs -copyFromLocal log4jMapReduce.jar \example\jars\

11. Verify the jar file has copied to the folder:

		hadoop fs -ls \example\jars\log4jMapReduce.jar

	You shall see the file listed there.

##<a id="runjobpowershell"></a> Run the MapReduce job using PowerShell
You can submit a MapReduce job using either HDinsight PowerShell cmdlets or Hadoop command line. This section covers using the PowerShell method, the next section cover using Hadoop command line.

1. Open a Windows Azure PowerShell console windows. The instructions can be found in [Install and configure PowerShell for HDInsight][hdinsight-configure-powershell].
2. Set the variables in the following script and run it:




##<a id="runjob"></a> Run the MapReduce job using Hadoop command line
You can submit a MapReduce job using Hadoop command line or using the HDInsight PowerShell.

1. From Hadoop command line, execute the following command to run the Hadoop MapReduce job:

		hadoop jar C:\Tutorials\log4jMapReduce.jar log4jMapReduce wasb:///sample.log wasb:///Tutorials/output

		hadoop jar wabs:///example/jars/log4jMapReduce.jar log4jMapReduce wasb:///example/data/sample.log wasb:///example/data/log4jMapReduceOutput 

		hadoop jar C:\Tutorials\log4jMapReduce.jar log4jMapReduce wasb:///example/data/sample.log wasb:///example/data/log4jMapReduceOutput 


	This command does a number of things: 
	
	- Calling the Hadoop program
	- Specifying the jar file (C:\Tutorials\log4jMapReduce.jar)
	- Indicating the class file (log4jMapReduce)
	- Specifying the input file (wasb:///sample.log), and output directory (wasb:///Tutorials/output). The command creates the output folder if the folder doesn't exist.
	- Running the MapReduce job 	

	![HDI.MapReduceResults](../media/HDI.MapReduceResults.png "MapReduce job results")
 
	The Reduce programs begin to process the data when the Map programs are 100% complete. Prior to that, the Reducer(s) queries the Mappers for intermediate data and gathers the data, but waits to process. 
	
	There are 6 Reduce input records (that correspond to the six log levels), and 1365 Map output records (that contain key value pairs). The Reduce program condensed the set of intermediate values that share the same key (DEBUG, ERROR, FATAL, and so on) to a smaller set of values.    

2. View the output of the MapReduce job in HDFS:

		hadoop fs -cat wasb:///Tutorials/output/part-00000

	By default, Hadoop creates files begin with the following naming convention: “part-00000”. Additional files created by the same job will have the number increased.The output look like:

	![HDI.MapReduceResults](../media/HDI.MapReduceOutput.png "MapReduce job output")
 
	Notice that after running MapReduce that the data types are now totaled and in a structured format.  

##<a id="cleanup"></a> Tutorial clean up ##

The clean up task applies to this tutorial only; it is not performed in the actual deployment. In this task, you will delete input and output directories so that if you like, you can run the tutorial again.  

1. Delete the sample.log file:

		hadoop fs -rm wasb:///sample.log
 
2. Delete the output directory and recursively delete files within the directory:

		hadoop fs –rmr wasb:///Tutorials/output/
 
Congratulations! You have successfully completed this tutorial.

##<a id="nextsteps"></a>Next steps

While MapReduce provides powerful diagnostic abilities, it can be a bit challenging to master. Other languages such as Pig and Hive provide an easier way to work with data stored in your HDInsight Service. To learn more, see the following articles:

* [Getting Started with Windows Azure HDInsight Service][hdinsight-getting-started]
* [Using Hive with HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
* [Using Pig with HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/) 
* [How to Run the HDInsight Samples](/en-us/manage/services/hdinsight/howto-run-samples/)


[hdinsight-getting-started]: /en-us/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-storage]: /en-us/manage/services/hdinsight/howto-blob-store/
[hdinsight-upload-data]: /en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/
[hdinsight-provision]: /en-us/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-configure-powershell]: /en-us/manage/services/hdinsight/configure-powershell-for-hdinsight/

[azure-create-storageaccount]: /en-us/manage/services/storage/how-to-create-a-storage-account/ 
[azure-storage-explorer]: http://azurestorageexplorer.codeplex.com/ 

[sample-log]: http://go.microsoft.com/fwlink/?LinkID=286223

