<properties urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Hadoop in HDInsight: Big data analysis in the cloud | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Hadoop in HDInsight: Big data processing and analysis in the cloud" authors="cgronlun" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/17/2014" ms.author="cgronlun" />



# Introduction to Hadoop in HDInsight: Big data processing and analysis in the cloud

Azure HDInsight deploys and provisions Apache Hadoop clusters in the cloud, providing a software framework designed to manage, analyze, and report on big data. 

The Hadoop core provides reliable data storage with the Hadoop Distributed File System (HDFS), and a simple MapReduce programming model to process and analyze, in parallel, the data stored in this distributed system. 

### What is big data?
Big data refers to data being collected in ever-escalating volumes, at increasingly high velocities, and for a widening variety of unstructured formats and variable semantic contexts. For big data to provide actionable intelligence or insight, not only must the right questions be asked and data relevant to the issues be collected, the data must be accessible, cleaned, analyzed, and then presented in a useful way, often in combination with data from various other sources that establish perspective and context in what is now referred to as a mashup.


## In this article

This article provides an overview of Hadoop on HDInsight, including:

* **[Advantages of Hadoop in the cloud:](#advantage)** Why you should consider HDInsight's cloud implementation of Hadoop.
* **[Overview of the Hadoop ecosystem on HDInsight:](#overview)**  HDInsight is the Hadoop solution on Azure and provides implementations of Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari, and so on. HDInsight also integrates with business intelligence (BI) tools such as Excel, SQL Server Analysis Services, and SQL Server Reporting Services.
* **[HDInsight solutions for big data analysis:](#solutions)** Some practical ways you can you HDInsight to answer questions for your organization, from analyzing Twitter sentiment to analyzing HVAC system effectiveness.
* **[Resources for learning more about big data analysis, Hadoop, and HDInsight:](#resources)** Links to  additional information.

## <a name="advantage"></a>Advantages of Hadoop in the cloud

As part of the Azure cloud ecosystem, Hadoop in HDInsight offers a number of benefits, among them:

* High availability and reliability of clusters. See [Availability and reliability of Hadoop clusters in HDInsight](/hdinsight-high-availability/) for details.
* Efficient and economical data storage with Azure Blob storage, a Hadoop-compatible option. See [Use Azure Blob storage with Hadoop in HDInsight](/hdinsight-use-blob-storage/) for details.
* Integration with other Azure services, including [Websites](/documentation/services/websites/) and [SQL Database](/documentation/services/sql-database/).
* Low entry cost. Start a [free trial](/pricing/free-trial/), or consult [HDInsight pricing details](/pricing/details/hdinsight/).

To read more about the advantages on Hadoop in HDInsight, see the [Azure features page for HDInsight][marketing-page].

## <a name="overview"></a>Overview of the Hadoop ecosystem on HDInsight

Hadoop is the rapidly expanding technology stack that is the go-to solution for big data analysis. HDInsight is the framework for the Microsoft Azure cloud implementation of Hadoop.
 
Hadoop clusters in HDInsight use versions of the Hortonworks Data Platform (HDP) distribution and the set of Hadoop components within that distribution. For information on the components and versions in HDInsight clusters, see [What's new in the Hadoop cluster versions provided by HDInsight?][component-versioning]

Apache Hadoop is a software framework for big data management and analysis. Apache Hadoop core provides reliable data storage with the Hadoop Distributed File System (HDFS), and a simple MapReduce programming model to process and analyze, in parallel, the data stored in this distributed system. HDFS uses data replication to address hardware failure issues that arise when deploying such highly distributed systems.

Here are the Hadoop technologies in HDInsight:

* **Ambari:** Cluster provisioning, management, and monitoring 
* **Avro** (Microsoft .NET Library for Avro): Data serialization for the Microsoft .NET environment 
* **HBase:** Non-relational database for very large tables
* **HDFS:** Hadoop Distributed File System
* **Hive:** SQL-like querying
* **Mahout:** Machine learning
* **MapReduce and YARN:** Distributed processing
* **Oozie:** Workflow management
* **Pig:** Scripting
* **Sqoop:** Data import and export 
* **Storm:** Real-time processing of data streams
* **Zookeeper:** Management of distributed systems
 
Also, find out about **business intelligence tools** you can use with HDInsight.

### Ambari

Apache Ambari is for provisioning, managing and monitoring Apache Hadoop clusters. It includes an intuitive collection of operator tools and a robust set of APIs that hide the complexity of Hadoop, simplifying the operation of clusters. For more information about the APIs, see Ambari API reference. 

### Avro (Microsoft .NET Library for Avro)

The Microsoft .NET Library for Avro implements the Apache Avro compact binary data interchange format for serialization for the Microsoft .NET environment. It uses JSON to define a language-agnostic schema that underwrites language interoperability, meaning data serialized in one language can be read in another. Detailed information on the format can be found in the Apache Avro Specification. 
The format of Avro files supports the distributed MapReduce programming model. Files are “splittable”, meaning you can seek any point in a file and start reading from a particular block. For additional information, see Serialize data with the Microsoft .NET Library for Avro.

### HBase

Apache HBase is a non-relational database built on Hadoop and designed for large amounts of unstructured and semi-structured data - potentially billions of rows times millions of columns. HBase clusters on HDInsight are configured to store data directly in Azure Blob storage

### HDFS

Hadoop Distributed File System (HDFS) is a distributed file system that, with MapReduce and YARN, is the core of the Hadoop ecosystem. HDFS is the standard file system for Hadoop clusters on HDInsight.

### Hive

Apache Hive is a data warehouse software built on Hadoop that provides a way to project structure on data and query it using a SQL-like language call HiveQL. Hive, like Pig, is an abstraction on top of MapReduce and when run, Hive translates queries into a series of MapReduce jobs. Hive is conceptually closer to a relational database management system than Pig, and is therefore appropriate for use with more structured data. For unstructured data, Pig is better choice. For additional information, see Welcome to Apache Hive!

### Mahout

Apache Mahout is a scalable library of machine learning algorithms that run on Hadoop. Using principles of statistics, machine learning applications teach systems to learn from data and to use past outcomes to determine future behavior. 

### MapReduce and YARN
Hadoop MapReduce is a software framework for writing applications to process big data sets in parallel. A MapReduce job splits large data sets and organizes the data into key-value pairs for processing. 
Apache YARN is the next generation of MapReduce (MapReduce 2.0, or MRv2) that splits the two major tasks of JobTracker, resource management and job scheduling/monitoring, into separate entities.
For more information on MapReduce, see MapReduce in the Hadoop Wiki. To learn about YARN, see Apache Hadoop NextGen MapReduce (YARN).

### Oozie
Apache Oozie is a workflow/coordination system that manages Hadoop jobs. It is integrated with the Hadoop stack and supports Hadoop jobs for MapReduce, Pig, Hive, and Sqoop. It can also be used to schedule jobs specific to a system, like Java programs or shell scripts.

### Pig

Apache Pig is a high-level platform that allows you to perform complex MapReduce transformations on very large data sets using a simple scripting language called Pig Latin. Pig translates the Pig Latin scripts so they’ll run within Hadoop. You can create User Defined Functions (UDFs) to extend Pig Latin. For additional information, see Welcome to Apache Pig!

### Sqoop
Apache Sqoop is tool that transfers bulk data between Hadoop and relational databases such a SQL, or other structured data stores, as efficiently as possible. For additional information, see the Apache Sqoop Website.

### Storm
Apache Storm is a distributed, real-time computation system for processing large streams of data fast. Storm is offered as a managed cluster in HDInsight. 

### Zookeeper
Apache Zookeeper coordinates processes in large distributed systems by means of a shared hierarchical namespace of data registers (znodes). Znodes contain small amounts of meta information needed to coordinate the processes: status, location, configuration, and so on. For more information on Zookeeper, see the Apache Zookeeper Wiki.

### Business intelligence tools 
Familiar business intelligence (BI) tools - such as Excel, PowerPivot, SQL Server Analysis Services and Reporting Services - retrieve, analyze, and report data integrated with HDInsight using either the Power Query add-in or the Microsoft Hive ODBC Driver. 

These BI tools can help in your big data analysis:

* <a target="_blank" href="http://www.microsoft.com/en-us/download/details.aspx?id=39379">Download Microsoft Power Query for Excel</a> 
* <a target="_blank" href="http://www.microsoft.com/en-us/download/details.aspx?id=40886">Download Microsoft Hive ODBC Driver</a>
* <a target="_blank" href="http://www.microsoft.com/en-us/server-cloud/solutions/business-intelligence/analysis.aspx">Learn more about SQL Server Analysis Services</a>
* <a target="_blank" href="http://msdn.microsoft.com/en-us/library/ms159106.aspx">Learn about SQL Server Reporting Services</a>

## <a name="solutions"></a>Try HDInsight solutions for big data analysis

Analyze data from your organization to gain insights into your business. Here are some examples: 

* [Analyze HVAC sensor data](/hdinsight-hive-analyze-sensor-data/): Learn how to analyze sensor data using Hive with HDInsight (Hadoop), and then visualize the data in Microsoft Excel. In this sample, you'll use Hive to process historical data produced by HVAC systems to see which systems can't reliably maintain a set temperature.
* [Use Hive with HDInsight to analyze website logs](/hdinsight-hive-analyze-website-log/): Learn how to use HiveQL in HDInsight to analyze website logs to get insight into the frequency of visits in a day from external websites, and a summary of website errors that the users experience.
* [Analyze sensor data in real-time with Storm and HBase in HDInsight (Hadoop)](/hdinsight-storm-sensor-data-analysis/): Learn how to build a solution that uses a Storm cluster in HDInsight to process sensor data from Azure Event Hubs, and then displays the processed sensor data as near-real-time information on a web-based dashboard.

To try other examples, see the Analyze section on the [HDInsight documentation page](/documentation/services/hdinsight/).


## <a name="resources"></a>Resources for learning more about big data analysis, Hadoop, and HDInsight 

### Hadoop in HDInsight	

* [HDInsight documentation](/documentation/services/hdinsight/): The documentation page for Azure HDInsight with links to articles, videos, and more resources.

* [Learning map for Hadoop in HDInsight](/hdinsight-learn-map): A guided tour of Hadoop documentation for HDInsight.

* [Get started with Azure HDInsight](/hdinsight-get-started/): A quick-start tutorial for using Hadoop in HDInsight.

* [Run the HDInsight samples](/hdinsight-run-samples/): A tutorial on how the run the samples that ship with HDInsight.
	
* [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx): Reference documentation for the HDinsight SDK.


### SQL Database on Azure	
		
* [Azure SQL Database](http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx): MSDN documentation for SQL Database.
	
* [Management Portal for SQL Database](http://msdn.microsoft.com/en-us/library/windowsazure/gg442309.aspx): A lightweight and easy-to-use database management tool for managing SQL Database in the cloud.

* [Adventure Works for SQL Database](http://msftdbprodsamples.codeplex.com/releases/view/37304): Download page for SQL Database sample database.	

### Microsoft business intelligence		

* [Connect Excel to Hadoop with Power Query](/hdinsight-connect-excel-power-query/): Learn how to connect Excel to the Azure storage account that stores the data associated with your HDInsight cluster by using Microsoft Power Query for Excel. 

* [Connect Excel to Hadoop with the Microsoft Hive ODBC Driver](/hdinsight-connect-excel-hive-ODBC-driver/): Learn how to import data from HDInsight with the Microsoft Hive ODBC Driver.

* [Microsoft Business Intelligence (BI)](http://www.microsoft.com/en-us/server-cloud/solutions/business-intelligence/default.aspx): Learn about Power BI for Office 365, download the SQL Server trial, and set up SharePoint Server 2013 and SQL Server BI.
			
	
### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: Learn more about the Apache Hadoop software library, a framework that allows for the distributed processing of large data sets across clusters of computers.	

*  <a target="_blank" href="http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html">HDFS</a>: Learn more about the architecture and design of the Hadoop Distributed File System (HDFS), the primary storage system used by Hadoop applications.		

* <a target="_blank" href="http://mapreduce.org/">MapReduce</a>: Learn more about the programming framework for writing Hadoop applications that rapidly process vast amounts of data in parallel on large clusters of compute nodes.	


[marketing-page]: ../services/hdinsight/
[component-versioning]: ../hdinsight-component-versioning/

[zookeeper]: http://zookeeper.apache.org/ 
