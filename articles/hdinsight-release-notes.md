<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />


#Microsoft HDInsight release notes

## Notes for 11/14/2014 release of HDInsight ##

The full version numbers for HDInsight clusters deployed with this release are unchanged from the 11/7/2014 release.

This release contains the following new features, component updates and bug fixes.
<table border="1">
<tr><th>Title</th><th>Description</a><th>Component</th></th><th>Cluster Type</th><th>JIRA (if applicable)</th></tr>

<tr><td>Script Action (Preview)</td><td>Preview of the cluster customization feature that enables modification of Hadoop clusters in arbitrary ways using custom scripts. With this new feature, users can experiment with and deploy projects available from the Apache Hadoop ecosystem to Azure HDInsight clusters. This customization feature is available on all types of HDInsight clusters including Hadoop, HBase and Storm. 
</td><td>New feature</td><td>All</td><td>N/A</td></tr>

<tr><td>Pre-built jobs for Azure websites & Storage logs analysis</td><td>The HDInsight Query Console has a getting started gallery that supports solutions that work on either your data or on sample data.

<p>Solutions that work on your data:<br>
We’ve created jobs for some of the most common data analysis scenarios, to provide a starting point for creating your own solutions. You can use your own data with the job to see how it works. Then, when you are ready, use what you have learned to create your own solution modeled after the pre-built job.</p>

<p>Solutions that work on sample data:<br>
Learn how to work with HDInsight by walking through some basic scenarios such as analyzing web logs and sensor data. You will learn not only how to use HDInsight to analyze such data, but how you can connect other applications and services to this data. Visualizing data by connecting to Microsoft Excel provides an example of the power of this approach.</p></td><td>Query console</td><td>Hadoop</td><td>N/A</td></tr>

<tr><td>Memory leak fix in Templeton</td><td>A memory leak in Templeton has been addressed that affected customers who had long running cluster or were submitting 100s of job requests a second. The issue manifested as Templeton 5xx errors and the workaround was to restart the service. The workaround is no longer needed.</td><td>Templeton</td><td>All</td><td>https://issues.apache.org/jira/browse/HADOOP-11248</td></tr>
</table><br>

Note: To demonstrate the new capabilities made available by cluster customization, the procedures using Script Actions to install Spark and R modules on a cluster have been documented. For further information, see:

* [Install and use Spark on HDInsight clusters][hdinsight-hadoop-spark-install] 
* [Install and use R on HDInsight clusters][hdinsight-hadoop-r-script]  

## Notes for 11/07/2014 release of HDInsight ##

The full version numbers for HDInsight clusters deployed with this release are:

* HDInsight 2.1	2.1.9.374.1153876
* HDInsight 3.0	3.0.5.374.1153876
* HDInsight 3.1	3.1.1.374.1153876

This release contains the following component updates.
<table border="1">
<tr><th>Title</th><th>Description</a><th>Component</th></th><th>Cluster Type</th><th>JIRA (if applicable)</th></tr>

<tr><td>HDP 2.1.7</td><td>This release is based on Hortonworks Data Platform (HDP) 2.1.7. The release notes for HDP 2.1.7 are available on the Hortonworks site at http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html.</td><td>HDP</td><td>All</td><td>N/A</td></tr>

<tr><td>YARN Timeline Server</td><td>The YARN Timeline Server (also known as the Generic Application History Server) has been enabled by default. The Timeline Server provides generic information about completed applications such as application ID, application name, application status, application submission time, and application completion time. <p>This application information can be retrieved from the head node either by accessing the URI http://headnodehost:8188 or by running the YARN command: yarn application –list –appStates ALL.</p> 
<p>This information can also be retrieved remotely though REST API at https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/.</p> 
<p>More detailed information on the Timeline Server is available at http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html.</p></td><td>Service, YARN</td><td>Hadoop, HBase</td><td>N/A</td></tr>

<tr><td>Cluster Deployment ID</td><td>Starting with latest SDK version 1.3.3.1.5426.29232, users can access HDInsight issued unique id for each cluster. This will enable customers to figure out unique instances of clusters when a dnsname is being reused across create/drop scenarios.</td><td>SDK</td><td>All</td><td>N/A</td></tr>
</table><br>

* Note that the bug that prevented the full version # from showing up in the portal or from being returned by the SDK or by PowerShell has been fixed in this release. 

## Notes for 10/15/2014 release ##

This hotfix release fixed a memory leak in Templeton that affected the heavy users of Templeton. In some cases users who exercised Templeton heavily would see errors manifested as 500 error codes because the requests would not have enough memory to execute. The workaround for this problem was to restart the Templeton service. This issue has now been fixed.


## Notes for 10/7/2014 release ##

* When using Ambari endpoint, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", the *host_name* field now returns the fully qualified domain name (FQDN) of the node instead of just the host name. For example, instead of returning "**headnode0**", you get the FQDN “**headnode0.{ClusterDNS}.azurehdinsight.net**”. This change was required to facilitate scenarios where multiple cluster types such as HBase and Hadoop can be deployed in one Virtual Network (VNET). This happens, for example, when using HBase as a back-end platform for Hadoop.

* We have provided new memory settings for the default deployment of the HDInsight cluster. Previous default memory settings did not take adequate account of the guidance for the number of CPU cores being deployed. These new memory settings should provide better defaults, as per Hortonworks recommendations. For changing these, please refer to SDK reference documentation on changing cluster configuration. The new memory settings used by the default 4 CPU core (8 container) HDInsight cluster are itemized in the following table. (The values used prior to this release are also provided parenthetically). 
 
<table border="1">
<tr><th>Component</th><th>Memory Allocation</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768MB (previously 512MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144MB (unchanged)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144MB (unchanged)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768MB (previously 512MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (previously -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536MB (previously 1024MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (previously -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768MB (previously 1024MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (previously -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256MB (previously 200MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536MB (unchanged)</td></tr>

</table><br>

For more information on the memory configuration settings used by YARN and MapReduce on the Hortonworks Data Platform used by HDInsight, see [Determine HDP Memory Configuration Settings](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks has also provide a tool to calculate proper memory settings.

HDInsight PowerShell/SDK Error: "*Cluster is not configured for Http Services access*":

* This error is a known [compatibility issue](https://social.msdn.microsoft.com/Forums/azure/en-US/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) that may occur due to a difference in the version of SDK/PowerShell and the version of the cluster. Clusters created on 8/15 or later have support for new provisioning capability into Virtual Networks. But this capability is not correctly interpreted by older versions of the SDK/PowerShell. The result is a failure in some job submission operations. If you use SDK APIs or PowerShell cmdlets to submit jobs (**Use-AzureHDInsightCluster**, **Invoke-Hive**), those operations may fail with the error message “*Cluster <clustername> is not configured for Http Services access*” or, depending on the operation, other error messages such as “*Cannot connect to cluster*”.

* Those compatibility issues are resolved in the latest versions of the HDInsight SDK and Azure PowerShell. We recommend updating the HDInsight SDK to the version 1.3.1.6 or later and Azure PowerShell Tools to the version 0.8.8 or later. You can get access to the latest HDInsight SDK from [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) and the Azure PowerShell Tools at [How to install and configure Azure PowerShell](http://azure.microsoft.com/en-us/documentation/articles/install-configure-powershell/).

* You can expect that SDK and PowerShell will continue to work with new updates to clusters as long as the version of the cluster remains the same. For example, clusters version 3.1 will always be compatible with current version of the SDK/PowerShell 1.3.1.6 and 0.8.8.


## Notes for 9/12/2014 release of HDInsight 3.1##

* This release is based on Hortonworks Data Platform (HDP) 2.1.5. For a list of the bugs fixed in this release, see the [Fixed in this Release](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) page on the Hortonworks site.
* In the pig libraries folder, the file “avro-mapred-1.7.4.jar” has been changed to avro-mapred-1.7.4-hadoop2.jar. The contents of these file contain a minor bug fix that is non-breaking. It is recommended that customers do not take a direct dependency on the name of the JAR file itself to avoid breaks when files are renamed.


## Notes for 8/21/2014 release ##

* We are adding the following new WebHCat configuration (HIVE-7155) that sets the default memory limit for a Templeton controller job to 1GB (the previous default value was 512MB):
	
	* templeton.mapper.memory.mb (=1024)
	* This change addresses the following error which certain Hive queries had run into due to lower memory limits: “Container is running beyond physical memory limits”.
	* To revert back to old defaults, you can set this configuration value to 512 through PowerShell SDK at cluster creation time using the following command:
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* The host name of zookeeper role was changed to zookeeper. This affects name resolution within the cluster, but doesn't affect external REST APIs. If you have components that use zookeepernode host name you will need to update them to use new name instead. The new names for the three zookeeper nodes are: 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* HBase version support matrix is updated. Only version HDInsight 3.1 (HBase version 0.98) is supported for production HBase workloads. Version 3.0 that was available for preview will not be supported moving forward. During transition period customers can still create clusters of version 3.0. 

## Notes on clusters created prior to 8/15/2014 ##

An HDInsight PowerShell/SDK error with the message "Cluster <clustername> is not configured for Http Services access" (or, depending on the operation other error messages like: "Cannot connect to cluster") may be encountered due to a version difference between SDK/PowerShell and a cluster. Clusters created on 8/15 or later have support for new provisioning capability into Virtual Networks. This capability isn’t correctly interpreted by older versions of the SDK/PowerShell, which results in failures of job submission operations. If you use SDK APIs or PowerShell cmdlets to submit jobs, such as Use-AzureHDInsightCluster or Invoke-AzureHDInsightHiveJob, those operations may fail with one of the error message described above.

These compatibility issues are resolved in the latest versions of SDK and Azure PowerShell. We recommend updating HDInsight SDK to the version 1.3.1.6 or later and Azure PowerShell Tools to the version 0.8.8 or later. You can get access to the latest HDInsight SDK from [nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/) and the Azure PowerShell Tools using [Microsoft Web PI](http://go.microsoft.com/?linkid=9811175&clcid=0x409).

You can expect that SDK and PowerShell will continue to work with new updates to clusters as long as the version of the cluster remains the same. For example, clusters version 3.1 will always be compatible with current version of the SDK/PowerShell 1.3.1.6 and 0.8.8.


## Notes for 7/28/2014 release ##

* **HDInsight Available in New Regions**: With this release, we have expanded HDInsight geographical presence to three new regions. HDInsight customers can now create clusters in these regions. 
	* East Asia, 
	* North Central US and 
	* South Central US. 
* With this release, HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) and HDInsight v2.1 (HDP1.3, Hadoop 1.2) are being removed from the Azure Management Portal. You may continue to create Hadoop clusters for these versions using HDInsight PowerShell cmdlets ([New-AzureHDInsightCluster](http://msdn.microsoft.com/en-us/library/dn593744.aspx)) or by using the [HDInsight SDK](http://msdn.microsoft.com/en-us/library/azure/dn469975.aspx). Please refer to the [HDInsight component versioning](http://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/) page for more information.
* Hortonworks Data Platform (HDP) changes in this release: 

<table border="1">
<tr><th>HDP</th><th>Changes</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>No changes</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>No changes</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## Notes for 6/24/2014 release ##

This release contains several new enhancements to HDInsight service: 

* **HDP 2.1 Availability**: HDInsight 3.1 which contains HDP 2.1 is now generally available and is the default version for new clusters.
* **HBase – Azure Management Portal Improvements**: We are making HBase clusters available in Preview. You can now create HBase clusters from the portal with 3 clicks.

![](http://i.imgur.com/cmOl5fM.png)

With HBase, you can build a variety of real-time workloads on HDInsight, from interactive websites that work with large datasets to services storing sensor and telemetry data from millions of end points. The next step would be to analyze the data in these workloads with Hadoop jobs and this is immediately possible in HDInsight through the experiences provided like PowerShell and Hive cluster dashboard.

### Apache Mahout now pre-installed on HDInsight 3.1 ###

 [Mahout](http://hortonworks.com/hadoop/mahout/) is preinstalled on HDInsight 3.1 Hadoop clusters. So you can run Mahout jobs without the need for any additional cluster configuration. For example, you can remote into an Hadoop cluster using the Remote Desktop Protocol (RDP) and without additional steps execute the Hello world Mahout command:

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

For a more complete explanation of this procedure, see the documentation of the [Breiman Example](https://mahout.apache.org/users/classification/breiman-example.html) on the Apache Mahout website. 


### Hive Queries can use Tez in HDInsight 3.1 ###

Hive 0.13 is now available in HDInsight 3.1 and is capable of running queries using Tez, which can be leveraged for substantial performance improvements. 
Tez is not enable by default for Hive queries. To use it, you must opt in. You can enable Tez by running the following code snippet:

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks has published a detailed breakdown of Hive query performance enhancements with Tez as delivered in standard benchmarks. For details, see [Benchmarking Apache Hive 13 for Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/). 

For more details on using Hive with Tez, check out the [Hive on Tez wiki page](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###Global Availability
With the release of Azure HDInsight on Hadoop 2.2, Microsoft has made HDInsight available in all major Azure geographies. Specifically, west Europe and southeast Asia data centers have been brought online. This enables customers to locate clusters in a data center that is close and potentially in a zone of similar compliance requirements. 


###Dos & Dont's between Cluster Versions

**Oozie metastores used with an HDInsight 3.1 cluster are not backward compatible with HDInsight 2.1 clusters and cannot be used with this previous version**

A custom Oozie metastore database deployed with an HDInsight 3.1 cluster cannot be reused with an HDInsight 2.1 cluster. This is the case even if the metastore originated with a 2.1 cluster. This scenario is not supported as the metastore schema gets upgraded when used with a 3.1 cluster and so is no longer compatible with the metastore required by the 2.1 clusters. Any attempt to reuse an Oozie metastore that has been used with an HDInsight 3.1 cluster will render the 2.1 cluster useless. 

**Oozie metastores cannot be shared across clusters**
On a more general and somewhat orthogonal note, Oozie metastores are attached to specific clusters and cannot be shared across clusters.

###Breaking Changes

**Prefix syntax**:
Only the "wasb://" syntax is supported in HDInsight 3.0  and 3.1 clusters. The older "asv://" syntax is supported in HDInsight 2.1 and 1.6 clusters, but it is not supported in HDInsight 3.0 clusters or later versions. This means that any jobs submitted to an HDInsight 3.0  or 3.1 cluster that explicitly use the “asv://” syntax will fail. The wasb:// syntax should be used instead. Also, jobs submitted to any HDInsight 3.0 or 3.1 clusters that are created with an existing metastore that contains explicit references to resources using the asv:// syntax will fail. These metastores will need to be recreated using the wasb:// to address resources. 


**Ports**: The ports used by the HDInsight service have been changed. The port numbers which were being used were within the Windows OS ephemeral port range. Ports are allocated automatically from a predefined ephemeral range for short-lived internet protocol-based communications. The new set of allowed Hortonworks Data Platform (HDP) service port numbers are now outside of this range to avoid encountering conflicts that could arise with the ports used by services running on the headnode. The new port numbers should not cause any breaking changes. The numbers used now are as follows:

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

###Dependencies 

The following dependencies were add in HDInsight 3.x (HDP2.x):

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

The following dependencies no longer exist in HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###Version changes 

The following version changes were made between HDInsight 2.x (HDP1.x) and HDInsight 3.x (HDP2.x):

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###Drivers
The SQL Server JDBC Driver is used internally by HDInsight and is not used for external operations. If you wish to connect to HDInsight using ODBC, please use the Microsoft Hive ODBC driver. For more information on using Hive ODBC, [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver][connect-excel-with-hive-ODBC].


### Bug fixes ###

With this release, we have refreshed the following HDInsight  (Hortonworks Data Platform - HDP) versions with several bug fixes:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## Hortonworks Release Notes ##

Release notes for the HDPs that are used by the versions of HDInsight cluster are available at the following locations.

* HDInsight cluster version 3.1 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 2.1.7][hdp-2-1-7].This is the default Hadoop cluster created when using the Azure HDInsight portal after 11/7/2014. HDInsight 3.1 clusters created before 11/7/2014 were based on the [Hortonworks Data Platform 2.1.1][hdp-2-1-1] 

* HDInsight cluster version 3.0 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 2.0][hdp-2-0-8].

* HDInsight cluster version 2.1 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 1.3][hdp-1-3-0]. 

* HDInsight cluster version 1.6 uses an Hadoop distribution that is based on the [Hortonworks Data Platform 1.1][hdp-1-1-0]. 

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[hdinsight-hadoop-spark-install]: ../hdinsight-hadoop-spark-install/
[hdinsight-hadoop-r-script]: ../hdinsight-hadoop-r-script/



