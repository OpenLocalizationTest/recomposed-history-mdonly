---
title: Data Lake Store MapReduce Performance Tuning Guidelines | Microsoft Docs
description: Data Lake Store MapReduce Performance Tuning Guidelines
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu

ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu

---
# Performance tuning guidance for MapReduce on HDInsight and Azure Data Lake Store


## Prerequisites

* **An Azure subscription**. See [Get Azure free trial](https://azure.microsoft.com/pricing/free-trial/).
* **An Azure Data Lake Store account**. For instructions on how to create one, see [Get started with Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** with access to a Data Lake Store account. See [Create an HDInsight cluster with Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Make sure you enable Remote Desktop for the cluster.
* **Using MapReduce on HDInsight**.  For more information, see [Use MapReduce in Hadoop on HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-use-mapreduce)  

## Parameters

When running MapReduce jobs, here are the most important parameters that you can configure to increase performance on ADLS:

* **Mapreduce.map.memory.mb** – The amount of memory to allocate to each mapper
* **Mapreduce.job.maps** – The number of map tasks per job
* **Mapreduce.reduce.memory.mb** – The amount of memory to allocate to each reducer
* **Mapreduce.job.reduces** – The number of reduce tasks per job

**Mapreduce.map.memory / Mapreduce.reduce.memory**
This number should be adjusted based on how much memory is needed for the map and/or reduce task.  The default values of mapreduce.map.memory and mapreduce.reduce.memory can be viewed in Ambari via the Yarn configuration.  In Ambari, navigate to YARN and view the Configs tab.  You can see the memory     

**Mapreduce.job.maps / Mapreduce.job.reduces**
This will determine the maximum number of mappers or reducers to be created.  The number of splits will determine how many mappers will be created for the MapReduce job.  Therefore, you may get less mappers than you requested if there are less splits than the number of mappers requested.       

## Guidance

**Step 1: Determine number of jobs running** - By default, MapReduce will use the entire cluster for your job.  You can use less of the cluster by using less mappers than there are available containers.  The guidance in this document assumes that your application is the only application running on your cluster.      

**Step 2: Set mapreduce.map.memory/mapreduce.reduce.memory** –  The size of the memory for map and reduce tasks will be dependent on your specific job.  You can reduce the memory size if you want to increase concurrency.  The number of concurrently running tasks depends on the number of containers.  By decreasing the amount of memory per mapper or reducer, more containers can be created, which enable more mappers or reducers to run concurrently.  Decreasing the amount of memory too much may cause some processes to run out of memory.  If you get a heap error when running your job, you should increase the memory per mapper or reducer.  You should consider that adding more containers will add extra overhead for each additional container, which can potentially degrade performance.  Another alternative is to get more memory by using a cluster that has higher amounts of memory or increasing the number of nodes in your cluster.  More memory will enable more containers to be used, which means more concurrency.  

**Step 3: Determine Total YARN memory** - To tune mapreduce.job.maps/mapreduce.job.reduces, you should consider the amount of total YARN memory available for use.  This information is available in Ambari.  Navigate to YARN and view the Configs tab.  The YARN memory is displayed in this window.  You should multiply the YARN memory with the number of nodes in your cluster to get the total YARN memory.

	Total YARN memory = nodes * YARN memory per node
If you are using an empty cluster, then memory can be the total YARN memory for your cluster.  If other applications are using memory, then you can choose to only use a portion of your cluster’s memory by reducing the number of mappers or reducers to the number of containers you want to use.  

**Step 4: Calculate mapreduce.job.maps/mapreduce.job.reduces** – mapreduce.job.maps is constrained either by memory or by CPU.  Take total YARN memory and divide that by mapreduce.map.memory.  

	Memory constraint = total YARN memory / mapreduce.map.memory
The CPU constraint is calculated as the total physical cores multiplied by the number of containers per core.  By default the number of containers per physical core is set to 2.

	CPU constraint = total physical cores * containers per core
Mapreduce.job.maps should be the minimum of these two numbers.

	Mapreduce.job.maps = Min (total cores * containers per core, available YARN memory / mapreduce.map.memory)   
This is a good starting point for mapreduce.job.maps and you can experiment with more or less mappers based on your data size to tune you performance.  The same calculation can be done for mapreduce.job.reduces.  

## Example Calculation

Let’s say you currently have a cluster composed of 8 D14 nodes and you want to run an I/O intensive job.  Here are the calculations you should do:

**Step 1: Determine number of jobs running** - for our example, we assume that our job is the only one running.  

**Step 2: Set mapreduce.map.memory/mapreduce.reduce.memory** – for our example, you are running an I/O intensive job and decide that 3GB of memory for map tasks will be sufficient.

	mapreduce.map.memory = 3GB
**Step 3: Determine Total YARN memory** 

	total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Step 4: Calculate mapreduce.job.maps/mapreduce.job.reduces**

	memory constraint = 768GB of available memory / 3 GB of memory =   256
	cores = nodes in cluster * # of cores in node = 8 nodes * 16 cores  = 128  
	CPU constraint = 128 cores * 2 containers per core = 256
	mapreduce.job.maps = Min (128 cores * 2 containers per core, 768GB of available YARN memory / 3GB of mapreduce.map.memory) = Min (256, 256) = 256

A good starting point is 256 map/reduce tasks.  You can experiment further by increasing or decreasing the number of mappers to see if you get better performance.   

## Limitations

**Using more mappers/reducers**

Using more mappers or reducers does not necessarily mean you will see better performance.  The number of mappers or reducers running concurrently depends on the available containers.  That is why we encourage you to tune the number of containers based on your available resources.   

**ADLS throttling** 

As a multi-tenant service, ADLS sets account level bandwidth limits.  If you hit these limits, you will start to see task failures. This can be identified by observing throttling errors in task logs.  If you need more bandwidth for your job, please contact us.   

To check if you are getting throttled, you need to enable the debug logging on the client side. Here’s how you can do that:

1. Put the following property in the log4j properties in Ambari > YARN > Config > Advanced yarn-log4j.
log4j.logger.com.microsoft.azure.datalake.store=DEBUG
Restart all the nodes/service for the config to take effect.

2. If you are getting throttled, you’ll see the HTTP 429 error code in the YARN log file. The YARN log file is in /tmp/&lt;user&gt;/yarn.log

## Examples to Run

To demonstrate how MapReduce runs on Azure Data Lake Store, below is some sample code that was run on a cluster with the following settings:

* 16 node D14v2
* Hadoop cluster running HDI 3.5

For a starting point, here are some example commands to run MapReduce Teragen, Terasort, and Teravalidate.  You can adjust these commands based on your resources.

**Teragen**

	yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=2048 -Dmapred.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

	yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=2048 -Dmapred.map.memory.mb=3072 -Dmapred.reduce.tasks=512 -Dmapred.reduce.memory.mb=3072 /example/data/1TB-sort-input /example/data/1TB-sort-output

**Teravalidate**

	yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=512 -Dmapred.map.memory.mb=3072 /example/data/1TB-sort-output /example/data/1TB-sort-validate
