<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in Hadoop cluster versions of HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />


#What's new in the Hadoop cluster versions provided by HDInsight?

Azure HDInsight now supports Hadoop 2.4 with HDInsight cluster version 3.1 (using Hortonworks Data Platform 2.1) by default. It takes full advantage of the platform to provide a range of significant benefits to customers. These include, most notably:

- **HBase (Preview)**: HBase is a low-latency NoSQL database that allows online transactional processing of big data. HBase is offered as a managed cluster integrated into the Azure environment. The clusters are configured to store data directly in Azure Blob storage, which provides low latency and increased elasticity in performance/cost choices. This enables customers to build interactive websites that work with large datasets, to build services that store sensor and telemetry data from millions of end points, and to analyze this data with Hadoop jobs.

- **Cluster Dashboard**: A new web application that is deployed to your HDInsight cluster.  Use it to run Hive Queries, check job logs, and browse Azure Blob storage. The URL used to access the web application is <*ClusterName*>.azurehdinsight.net.

- **Microsoft Avro Library**: This library implements the Apache Avro data serialization system for the Microsoft.NET environment. Apache Avro provides a compact binary data interchange format for serialization. It uses JSON to define language agnostic schema that underwrites language interoperability. Data serialized in one language can be read in another. Currently C, C++, C#, Java, PHP, Python, and Ruby are supported. The Apache Avro serialization format is widely used in Azure HDInsight to represent complex data structures within a Hadoop MapReduce job.

- **YARN**: A new, general-purpose, distributed, application management framework that has replaced the classic Apache Hadoop MapReduce framework for processing data in Hadoop clusters. It effectively serves as the Hadoop operating system, and takes Hadoop from a single-use data platform for batch processing to a multi-use platform that enables batch, interactive, online and stream processing. This new management framework improves scalability and cluster utilization according to criteria such as capacity guarantees, fairness, and service-level agreements.

- **Tez (HDInsight 3.1 only)**: A general-purpose and customizable framework that creates simplified data-processing tasks across both small scale and large-scale workloads in Hadoop. It provides the ability to execute a complex directed acyclic graph (DAG) of tasks for a single job, so that projects in the Apache Hadoop ecosystem such as Apache Hive and Apache Pig can meet requirements for human-interactive response times and extreme throughput at petabyte scale. Note that Hive 0.13 allows Hive queries to run on top of Tez, rather than on MapReduce.

- **High Availability**: A second headnode has been added to the Hadoop clusters deployed by HDInsight to increase the availability of the service. Standard implementations of Hadoop clusters typically have a single headnode. HDInsight removes this single point of failure with the addition of a secondary headnode. The switch to new HA cluster configuration doesn’t change the price of the cluster, unless customers provision clusters with extra large head node instead of the default large size node.

- **Hive performance**: Order of magnitude improvements to Hive query response times (up to 40x) and to data compression (up to 80%) using the **Optimized Row Columnar** (ORC) format.

- **Pig, Sqoop, Qozie, Ambari**: Component version upgrades for HDInsight cluster version 3.0 (HDP 2.0/Hadoop 2.2) that provide parity with HDInsight cluster version 2.1 (HDP 1.3/Hadoop 1.2). See the version tables below for specifics. Note that HBase, Mahout, Flume are not included.


**Deployment**	
Creation of HDInsight 3.1 clusters on Hadoop 2.4 is supported by the Azure Portal, the HDInsight SDK, and by Azure PowerShell. Note that HDInsight 3.1 clusters are created by default on Hadoop 2.4, so the users must use the **Custom Create** option in the portal to specify other HDInsight cluster versions if they need to be created.

**Global Availability**		
With the release of Azure HDInsight on Hadoop 2.2, Microsoft has made HDInsight available in all major Azure geographies. Specifically, west Europe and southeast Asia data centers have been brought online. This enables customers to locate clusters in a data center that is close and potentially in a zone of similar compliance requirements. 

**Breaking Changes**	
Only the "wasb://" syntax is supported in HDInsight 3.0  and 3.1 clusters. The older "asv://" syntax is supported in HDInsight 2.1 and 1.6 clusters, but it is not supported in HDInsight 3.0 clusters or later versions. This means that any jobs submitted to an HDInsight 3.0  or 3.1 cluster that explicitly use the “asv://” syntax will fail. The wasb:// syntax should be used instead. Also, jobs submitted to any HDInsight 3.0 or 3.1 clusters that are created with an existing metastore that contains explicit references to resources using the asv:// syntax will fail. These metastores will need to be recreated using the wasb:// to address resources. 

**Release Notes**	
See [HDInsight Release Notes](http://azure.microsoft.com/en-us/documentation/articles/hdinsight-release-notes/) for additional release notes on the latest versions of HDInsight.

##HDInsight versions
HDInsight supports multiple Hadoop cluster versions that can be deployed at any time. Each version choice provisions a specific version of the Hortonworks Data Platform (HDP) distribution and a set of components that are contained within that distribution. The component version associated with each HDInsight cluster version are itemized in the following table. Note that the default cluster version used by [Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) is currently 3.1 based on HDP 2.1.


<table border="1">
<tr><th>Component</th><th>Version 3.1 (Default)</th><th>Version 3.0</th><th>Version 2.1</th><th>Version 1.6</th></tr>
<tr><td>Hortonworks Data Platform (HDP)</td><td>2.1</td><td>2.0</td><td>1.3</td><td>1.1</td></tr>
<tr><td>Apache Hadoop & YARN</td><td>2.4.0</td><td>2.2.0</td><td>1.2.0</td><td>1.0.3</td></tr>
<tr><td>Tez</td><td>0.4.0</td><td></td><td></td><td></td></tr>
<tr><td>Apache Pig</td><td>0.12.1</td><td>0.12.0</td><td>0.11.0</td><td>0.9.3</td></tr>
<tr><td>Apache Hive & HCatalog</td><td>0.13.0</td><td>0.12.0</td><td>0.11.0</td><td>0.9.0</td></tr>
<tr><td>HBase (Preview)</td><td></td><td>0.96.0</td><td></td><td></td></tr>
<tr><td>Apache Sqoop</td><td>1.4.4</td><td>1.4.4</td><td>1.4.3</td><td>1.4.2</td></tr>
<tr><td>Apache Oozie</td><td>4.0.0</td><td>4.0.0</td><td>3.3.2</td><td>3.2.0</td></tr>
<tr><td>Apache HCatalog</td><td>Merged with Hive</td><td>Merged with Hive</td><td>Merged with Hive</td><td>0.4.1</td></tr>
<tr><td>Apache Templeton</td><td>Merged with Hive</td><td>Merged with Hive</td><td>Merged with Hive</td><td>0.1.4</td></tr>
<tr><td>Ambari</td><td>1.5.1</td><td>1.4.1</td><td>API v1.0</td><td></td></tr>
<tr><td>Zookeeper</td><td>3.4.5</td><td>3.4.5</td><td></td><td></td></tr>
</table>


### Select a version when provisioning an HDInsight cluster

When creating a cluster through the HDInsight PowerShell Cmdlets or the HDInsight .NET SDK, you can choose the version for the HDInsight Hadoop cluster using the "Version" parameter.

If you use the **Quick Create** option, you will get the version 3.1 of HDInsight that creates Hadoop cluster by default. If you use the **Custom Create** option from the Azure Portal, you can choose the version of the cluster you will deploy from the **HDInsight Version** drop-down on the **Cluster Details** page. 


## Supported versions
The following table lists the versions of HDInsight currently available, the corresponding Hortonworks Data Platform (HDP) versions that they use, and their release dates. When known, their support expiration and deprecation dates will also be provided. Note that highly available clusters with two head nodes are deployed by default for HDInsight 2.1, 3.0, and 3.1 clusters. They are not available for HDInsight 1.6 clusters.

<table border="1">
<tr><th>HDInsight Version</th><th>HDP Version</a><th>High Availability</th></th><th>Release Date</th><th>Support Expiration Date</th><th>Deprecation Date</th></tr>
<tr><td>HDI 3.1</td><td>HDP 2.1</td><td>Yes</td><td></td><td></td><td></td></tr>
<tr><td>HDI 3.0</td><td>HDP 2.0</td><td>Yes</td><td>02/11/2014</td><td>09/17/2014</td><td>06/30/2015</td></tr>
<tr><td>HDI 2.1</td><td>HDP 1.3</td><td>Yes</td><td>10/28/2013</td><td>05/12/2014</td><td>05/31/2015</td></tr>
<tr><td>HDI 1.6</td><td>HDP 1.1</td><td>No</td><td>10/28/2013</td><td>04/26/2014</td><td>05/31/2015</td></tr>
</table><br>

### The Service-Level Agreement (SLA) for HDInsight cluster versions

The SLA is defined in terms of a "Support Window". A Support Window refers to the period of time that an HDInsight cluster version is supported by Microsoft Customer Support.  An HDInsight cluster is outside the Support Window if its version has a **Support Expiration Date** past the current date.  A list of supported HDInsight cluster versions may be found in the table above.  The support expiration date for a given HDInsight version X (once a newer X+1 version is available) is calculated as the later of:  

- Formula 1:  Add 180 days to the date HDInsight cluster version X was released
- Formula 2: Add 90 days to the date HDInsight cluster version X+1 (the subsequent version after X) is made available in the Azure Management Portal.

The **Deprecation Date** is the date after which the cluster version can not be created on HDInsight.

> [WACOM.NOTE] Both HDInsight 2.1 and 3.0 cluster run on Azure Guest OS [Family 4](http://msdn.microsoft.com/en-us/library/azure/ee924680.aspx#explanation) which uses the 64-bit version of Windows Server 2012 R2 and supports .NET Framework 4.0, 4.5. and 4.5.1. 

### Additional notes and information on versioning

- The SQL Server JDBC Driver is used internally by HDInsight and is not used for external operations. If you wish to connect to HDInsight using ODBC, please use the Microsoft Hive ODBC driver. For more information on using Hive ODBC, [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver][connect-excel-with-hive-ODBC].

- The ports used by the HDInsight service have been changed. The port numbers which were being used were within the Windows OS ephemeral port range. Ports are allocated automatically from a predefined ephemeral range for short-lived internet protocol-based communications. The new set of allowed Hortonworks Data Platform (HDP) service port numbers are now outside of this range to avoid encountering conflicts that could arise with the ports used by services running on the headnode. The new port numbers should not cause any breaking changes. The numbers used now are as follows:

 **HDInsight 1.6 (HDP 1.1)**
<table border="1">
<tr><th>Name</th><th>Value</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.0 and 3.1 (HDP 2.0 and 2.1)**
<table border="1">
<tr><th>Name</th><th>Value</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

* HDInsight cluster version 3.1 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 2.1][hdp-2-1-1].This is the default Hadoop cluster created when using the Azure HDInsight portal.

* HDInsight cluster version 3.0 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 2.0][hdp-2-0-8].

* HDInsight cluster version 2.1 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 1.3][hdp-1-3-0]. 

* HDInsight cluster version 1.6 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 1.1][hdp-1-1-0]. 

* The component versions associated with HDInsight cluster versions may change in future updates to HDInsight. One way to determine the available components and to verify which versions are being used for a cluster to use the Ambari REST API. The **GetComponentInformation** command can be used to retrieve information about a service component. For details, see the [Ambari documentation][ambari-docs]. Another way to obtain this information is to login to a cluster using remote desktop and examine the contents of the "C:\apps\dist\" directory directly.

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/en-us/support/forums/

[connect-excel-with-hive-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/ 
