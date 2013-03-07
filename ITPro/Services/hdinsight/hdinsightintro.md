<properties linkid="manage-hdinsight-introduction" urlDisplayName="Introducing HDInsight" pageTitle="Introducing Windows Azure HDInsight Service" metaKeywords="hdinsight, hdinsight service, hdinsight azure, what is hdinsight" metaDescription="Learn about the HDInsight Service for Windows Azure" metaCanonical="http://www.windowsazure.com/en-us/manage/hdinsight/hdinsightintro" umbracoNaviHide="0" disqusComments="1" writer="bradsev" editor="mollybos" manager="paulettm" />

# Introduction to Windows Azure HDInsight Service

**Overview**	
Windows Azure HDInsight Services is a service that deploys and provisions Apache™ Hadoop™ clusters in the cloud, providing a software framework designed to manage, analyze and report on big data.

**Big Data**  
Data is described as "big data" to indicate that it is being collected in ever escalating volumes, at increasingly high velocities, and for a widening variety of unstructured formats and variable semantic contexts. Big data collection does not provide value to an enterprise on its own. For big data to provide value in the form of actionable intelligence or insight, it must be accessible, cleaned, analyzed, and then presented in a useful way, often in combination with data from various sources.

**Apache Hadoop**  
Apache Hadoop is a software framework that facilitates big data management and analysis. Apache Hadoop core provides reliable data storage with the Hadoop Distributed File System (HDFS), and a simple MapReduce programming model to process and analyze in parallel the data stored in this distributed system. HDFS uses data replication to address hardware failure issues that arise when deploying such highly distributed systems.

**MapReduce**  
To simplify the complexities of analyzing unstructured data from various sources, the MapReduce programming model provides a core abstraction that provides closure for map and reduce operations. The MapReduce programming model views all of its jobs as computations over key-value pair datasets. So both input and output files must contain such key-value pair datasets. Other Hadoop-related projects such as Pig and Hive are built on top of HDFS and the MapReduce framework, providing higher abstraction levels such as data flow control and querying, as well as additional functionality such as warehousing and mining, required to integrate big data analysis and end-to-end management.

**HDInsight Services**  
HDInsight Services for Windows Azure makes Apache Hadoop avaliable as a service in the cloud. It makes the HDFS/MapReduce software framework and related projects available in a simpler, more scalable, and cost efficient environment. 

One of the efficiencies introduced by HDInsight is in how it manages and stores data. HDInsight uses Azure Storage Vault (ASV) as the default file system. ASV and HDFS are distinct file systems that are optimized,
respectively, for the storage of data and computations on that data.

- ASV providesa highly scalable and available, low cost, long term, and shareable storage option for data that is to be processed using HDInsight.
- The Hadoop clusters deployed by HDInsight on HDFS are optimized for running Map/Reduce (M/R) computational tasks on the data.
 
HDInsight clusters are deployed in Azure on compute nodes to execute MapReduce tasks and are dropped once these tasks have been completed. Keeping the data in the HDFS clusters after computations have been completed would be an expensive
way to store this data. ASV provides a full featured HDFS file system over Azure Blob storage (ABS). ABS is a robust, general purpose Azure storage solution, so storing data in ABS enables the clusters used for computation to be safely deleted without losing user data. ASV is not only low cost. It has been designed as an HDFS extension to provide a seamless experience to customers by enabling the full set of components in the Hadoop ecosystem to operate directly on the data it manages.

To simplify configuring and running Hadoop jobs and managing the deployed clusters, HDInsight Services provides JavaScript and Hive interactive consoles. This simplified JavaScript approach enables IT professionals and a wider group of developers to deal with big data management and analysis by providing a more accessible path for them into the Hadoop framework.
 
It addition to the available Apache Hadoop-related ecosystem projects, HDInsight Services for Windows Azure also provides Open Database Connectivity (ODBC) drivers to integrate Business Intelligence (BI) tools such as Excel, SQL Server Analysis Services, and Reporting Services, facilitating and simplifying end-to-end data analysis.

**Outline**  
This topic describes the Hadoop ecosystem supported by HDInsight Services, the main use scenarios for HDInsight Services, and a guide to further resources. It contains the following sections:


 * <a href="#Ecosystem">The Hadoop Ecosystem on HDInsight Services </a> - HDInsight Services provides Pig, Hive, Mahout, Pegasus, Sqoop, and Flume implementations, and supports other BI tools such as Excel, SQL Server Analysis Services and Reporting Services that are integrated with ASV/HDFS and the MapReduce framework.  

 * <a href="#Scenarios">Big Data Scenarios for HDInsight Services</a> - The types of jobs appropriate for using HDInsight Services.

 * <a href="#Resources">Resources for HDInsight Services</a> - Where to find relevant resources with additional information.

(ToDo: Confirm which technologies are supported in HDInsight.)
<h2 id="ecosystem"> <a name="Ecosystem">The Hadoop Ecosystem on Windows Azure </a></h2>

**Introduction** 	
HDInsight Services offers a framework implementing Microsoft's cloud-based solution for handling big data. This federated ecosystem manages and analyses large data amounts while exploiting parallel processing capabilities the MapReduce programming model. Technologies such as Sqoop and Flume integrate HDFS with relational data stores and log files. Hive and Pig integrate data processing and warehousing capabilities. Pegasus provides graph-mining capabilities. Microsoft’s Big Data solution  integrates with Microsoft's BI tools, including SQL Server Analysis Services, Reporting Services, PowerPivot and Excel. This enables you to perform a straightforward BI on data stored and managed by the HDInsight Services ecosystem. The Apache-compatible technologies and sister technologies are part of this ecosystem built to run on top of Hadoop clusters are itemized and briefly described in this section.

 **Pig**	
Pig is a high-level platform for processing big data on Hadoop clusters. Pig consists of a data flow language, called Pig Latin, supporting writing queries on large datasets and an execution environment running programs from a console. The Pig Latin programs consist of dataset transformation series converted under the covers, to a MapReduce program series. Pig Latin abstractions provide richer data structures than MapReduce, and perform for Hadoop what SQL performs for RDBMS systems. Pig Latin is fully extensible. User Defined Functions (UDFs), written in Java, Python, C#, or JavaScript, can be called to customize each processing path stage when composing the analysis. For additional information, see [Welcome to Apache Pig!](http://pig.apache.org/)

 **Hive**	
Hive is a distributed data warehouse managing data stored in an HDFS. It is the Hadoop query engine. Hive is for analysts with strong SQL skills providing an SQL-like interface and a relational data model. Hive uses a language called HiveQL; a dialect of SQL. Hive, like Pig, is an abstraction on top of MapReduce and when run, Hive translates queries into a series of MapReduce jobs. Scenarios for Hive are closer in concept to those for RDBMS, and so are appropriate for use with more structured data. For unstructured data, Pig is better choice. HDInsight Services includes an ODBC driver for Hive, which provides direct real-time querying from business intelligence tools such as Excel into Hadoop. For additional information, see [Welcome to Apache Hive!](http://hive.apache.org/)

 **Mahout**		
Mahout is an open source machine-learning library facilitating building scalable matching learning libraries. Using the map/reduce paradigm, algorithms for clustering, classification and batch-based collaborative filtering developed for Mahout are implemented on top of Apache Hadoop. For additional information, see [What is Apache Mahout](http://mahout.apache.org/).

 **Pagasus**		
Pegasus is a peta-scale graph mining system running on Hadoop. Graph mining is data mining used to find the patterns, rules, and anomalies characterizing graphs. A graph in this context is a set of objects with links existing between any two objects in the set. This structure type characterizes networks everywhere, including pages linked on the Web, computer and social networks (FaceBook, Twitter), and many biological and physical systems. Prior to Pegasus, the maximum graph size that could be mined incorporated millions of objects. By developing algorithms that run in parallel on top of a Hadoop cluster, Pegasus develops algorithms to mine graphs containing billions of objects. For additional information, see the [Project Pegasus](http://www.cs.cmu.edu/~pegasus/) Web site.

 **Sqoop**		
Sqoop is tool that transfers bulk data between Hadoop and relational databases such a SQL, or other structured data stores, as efficiently as possible. Use Sqoop to import data from external structured data stores into the HDFS or related systems like Hive. Sqoop can also extract data from Hadoop and export the extracted data to external relational databases, enterprise data warehouses, or any other structured data store type. For additional information, see the  [Apache Sqoop](http://sqoop.apache.org/) Web site.

**Flume**
Flume is a distributed, reliable, and available service for efficiently collecting, aggregating, and moving large log data amounts to HDFS. Flume's architecture is streaming data flow based. It is robust and fault tolerant with tunable and reliability mechanisms with many failover and recovery mechanisms. It has a simple extensible data model enabling online analytical applications. For additional information, see the  [Flume](http://incubator.apache.org/flume/) incubation site.

 **Business Intelligence Tools**		
Familiar Business Intelligence (BI) tools such as Excel, PowerPivot, SQL Server Analysis Services and Reporting Services retrieves, analyzes and reports data integrated with HDInsight Services using ODBC drivers. The Hive ODBC driver and Hive Add-in for Excel are available for download on the _HDInsight Services_ portal (**ToDo**: link? And update the following links.) 	
 * For information Analysis Services, see [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx).	
 * For information Reporting Services, see [SQL Server 2012 Reporting](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-reporting-services.aspx).	


<h2 id="senarios"> <a name="Scenarios">Big Data Scenarios for HDInsight Services</a></h2>
A exemplary scenario providing a case for an HDInsight Service is an ad hoc analysis, in batch fashion, on an entire unstructured dataset stored on Windows Azure nodes, which do not require frequent updates.

These conditions apply to a wide variety of activities in business, science and governance. These might include, for example, monitoring supply chains in retail, suspicious trading patterns in finance, demand patterns for public utilities and services, air and water quality from arrays of environmental sensors, or crime patterns in metropolitan areas.

HDInsight Services (and Hadoop technologies in general) are most suitable for handling a large amount of logged or archived data that does not require frequent updating once it is written, and that is read often, typically to do a full analysis. This scenario is complementary to data more suitably handled by a RDBMS that require lesser amounts of data (Gigabytes instead of Petabytes), and that must be continually updated or queried for specific data points within the full dataset. RDBMS work best with structured data organized and stored according to a fixed schema. MapReduce works well with unstructured data with no predefined schema because it interprets data when being processed.

(**ToDo**: Do we want to get some more concrete examples listed or outlined?)

(**ToDo**: update any of the links that are stale in the following section)
<h2 id="Resources"> <a name="Resources">Resources  for HDInsight Services</a> </h2>
**Microsoft: HDInsight Services**	

* [Welcome to HDInsight Services](https://www.hadooponazure.com/) - the welcome page for the Developer Preview for the Apache Hadoop-based Services for Windows Azure.	
* [Apache Hadoop-based Services for Windows Azure How To Guide](http://social.technet.microsoft.com/wiki/contents/articles/6206.hadoop-based-services-on-windows-azure-how-to-guide.aspx) - TechNet wiki with links HDInsight Services documentation.	
* [Big Data and Windows Azure](http://www.windowsazure.com/en-us/home/scenarios/big-data/) - Big Data scenarios that explore what you can build with Windows Azure.	

**Microsoft: Windows and SQL Database**	

* [Windows Azure home page](https://www.windowsazure.com/en-us/) - scenarios, free trial sign up, development tools and documentation that you need get started building applications.		
* [MSDN SQL Database](http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx)	- MSDN documentation for SQL Database	
* [Management Portal for SQL Database](http://msdn.microsoft.com/en-us/library/windowsazure/gg442309.aspx) - a lightweight and easy-to-use database management tool for managing SQL Database in the cloud.
* [Adventure Works for SQL Database](http://msftdbprodsamples.codeplex.com/releases/view/37304) - Download page for SQL Databse sample database.	

**Microsoft: Business Intelligence**		

* [Microsoft BI PowerPivot](http://www.microsoft.com/en-us/bi/PowerPivot.aspx) - a powerful data mashup and data exploration tool. 				
* [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx) - build comprehensive, enterprise-scale analytic solutions that deliver actionable insights.	
* [SQL Server 2012 Reporting](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-reporting-services.aspx) - a comprehensive, highly scalable solution that enables real-time decision making across the enterprise. 
	
**Apache Hadoop**:			

* [Apache Hadoop](http://hadoop.apache.org/) - software library providing a framework that allows for the distributed processing of large data sets across clusters of computers.	
* [HDFS](http://hadoop.apache.org/hdfs/) - Hadoop Distributed File System (HDFS) is the primary storage system used by Hadoop applications.		
* [Map Reduce](http://hadoop.apache.org/mapreduce/) - a programming model and software framework for writing applications that rapidly process vast amounts of data in parallel on large clusters of compute nodes.		
