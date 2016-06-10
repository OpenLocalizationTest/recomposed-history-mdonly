<properties
   pageTitle="Compute context options for R Server on HDInsight (preview) | Microsoft Azure"
   description="Learn about the different compute context options available to users with R Server on HDInsight (preview)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="05/31/2016"
   ms.author="jeffstok"
/>

# Compute context options for R Server on HDInsight (preview)

R Server on HDInsight (preview) provides the latest capabilities for R-based analytics by using data stored in HDFS in a container on your [Azure Blob](../storage/storage-introduction.md "Azure Blob storage") storage account or the local Linux file system. Since R Server is built on open source R, the R-based applications you build can leverage any of the 8000+ open source R packages, as well as the routines in [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR"), Microsoft’s big data analytics package that's included with R Server.  

The edge node of Premium clusters provides a convenient landing zone for connecting to the cluster and running your R scripts. With an edge node, you have the option of running ScaleR’s parallelized distributed functions across the cores of the edge node server, or across the nodes of the cluster through use of ScaleR’s Hadoop Map Reduce or Spark compute contexts.

## Compute contexts for an edge node

In general, an R script that's run in R Server on the edge node runs within the R interpreter on that node except for those steps that call a ScaleR function. The ScaleR calls run in a compute environment that's determined by how you set the ScaleR compute context. The possible values of the compute context when you run your R script from an edge node are local sequential (‘local’), local parallel (‘localpar’), Map Reduce, and Spark, where:

| Compute context  | How to set                      | Execution context                                                                     |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Local Sequential | rxSetComputeContext(‘local’)    | Sequential (non-parallelized) execution on the edge node server                       |
| Local Parallel   | rxSetComputeContext(‘localpar’) | Parallelized across the cores of the edge node server                                 |
| Spark            | RxSpark()                       | Parallelized distributed execution via Spark across the nodes of the HDI cluster      |
| Map Reduce       | RxHadoopMR()                    | Parallelized distributed execution via Map Reduce across the nodes of the HDI cluster |


Assuming that you’d like parallelized execution for the purposes of performance, then there are three options. Which option you choose depends on the nature of your analytics work, and the size and location of your data.

## Deciding on a compute context

Currently, there is no formula that tells you which compute context to use. There are, however, some guiding principles that can point you to the right choice, or at least help you narrow it down before you to run a benchmark. These guiding principles include:

1.	The local Linux file system is faster than HDFS.
2.	Repeated analyses are faster if the data is local, and if it's in XDF.
3.	Stream from a text data source for small data; otherwise convert to XDF prior to analysis.
4.	The overhead of copying/streaming the data to the edge node for analysis becomes unmanageable for very large amounts of data.
5.	Spark is faster than Map Reduce for analysis in Hadoop until the amount of data gets very large and is no longer able to fit in distributed memory.

Given these principles, some general rules of thumb for selecting a compute context are:

### Local parallel

- If the amount of data to analyze is small and does not require repeated analysis, then stream it directly into the analysis routine and use ‘localpar’.
- If the amoung of data to analyze is small or medium-sized and requires repeated analysis, then copy it to the local file system, import to XDF, and analyze via ‘localpar’.

### Hadoop spark

- If the amount of data to analyze is large, then import it to XDF in HDFS unless storage is an issue, and analyze it via ‘Spark‘.

### Hadoop Map Reduce

- If the amount of data to analyze is very large and Spark performance begins to deteriorate, then try an analysis via Map Reduce.

## Inline help on rxSetComputeContext

For more information and examples of ScaleR compute contexts, see the inline help in R on the rxSetComputeContext method, for example:

    > ?rxSetComputeContext

Or, see the “ScaleR Distributed Computing Guide” that's available from the [R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "R Server on MSDN") library.


## Next steps

Now that you understand how to create a new HDInsight cluster that includes R Server, and the basics of using the R console from an SSH session, use the following to discover other ways of working with R Server on HDInsight:

- [Overview of R Server on Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Get started with R server on Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Add RStudio Server to HDInsight premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Azure Storage options for R Server on HDInsight premium](hdinsight-hadoop-r-server-storage.md)
