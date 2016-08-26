<properties
pageTitle="Use the Microsoft Azure Data Lake Tools for Visual Studio with the Hortonworks Sandbox"
description="Learn how to use the Azure Data Lake Tools for VIsual Studio with the Hortonworks sandbox (running in a local VM.) With these tools, you can create and run Hive and Pig jobs on the sandbox and view job output and history."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang=""
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/24/2016"
ms.author="larryfr"/>

# Use the Azure Data Lake Tools for Visual Studio with the Hortonworks Sandbox

The Azure Data Lake tools for Visual Studio include tools for working with generic Hadoop clusters, in addition to tools for working with Azure Data Lake and HDInsight. This document provides the steps needed to use the Azure Data Lake tools with the Hortonworks Sandbox running in a local virtual machine.

## Prerequisites

* The Hortonworks Sandbox running in a virtual machine on your development environment. This document was written and tested with the sandbox running in Oracle VirtualBox, which was configured using the information in the [Get started in the Hadoop ecosystem](hdinsight-hadoop-emulator-get-started.md) document.

* Visual Studio 2013 or 2015

* The [Azure SDK for .NET](https://azure.microsoft.com/en-us/downloads/) 2.7.1 or higher

* [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## Configure passwords for the sandbox

Make sure that the Hortonworks Sandbox is running, then follow the steps in [Get started in the Hadoop ecosystem](hdinsight-hadoop-emulator-get-started.md#set-passwords) to configure the password for the SSH `root` account, and the Ambari `admin` account. These passwords will be used when connecting to the sandbox from Visual Studio.

## Connect the tools to the sandbox

1. Open Visual Studio, and select __View__, then __Server Explorer__.

2. From __Server Explorer__, right click the __HDInsight__ entry, and then select __Connect to HDInsight Emulator__.

    ![Connect to HDInsight Emulator](./media/hdinsight-hadoop-emulator-visual-studio/connect.png)

3. From the __Connect to HDInsight Emulator__ dialog, enter the password that you configured for Ambari.

    ![Enter Ambari password](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Select __Next__ to continue.

4. Enter a __Friendly Name__ for the emulator, then use the __Password__ field to enter the password you configured for the `root` account. Leave the other fields at the default value.

    ![Enter root password](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Select __Next__ to continue.

5. Wait for validation of the services to complete. In some cases, validation may fail and prompt you to update the configuration. When this happens, select the __update__ button and wait for the configuration and verification for the service to complete.

    ![Errors and update button](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [AZURE.NOTE] The update process uses Ambari to modify the Hortonworks Sandbox configuration to what is expected by the Azure Data Lake tools for Visual Studio.

    Once validation has completed, select __Finish__ to complete configuration.

    ![Finish connecting](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [AZURE.NOTE] Depending on the speed of your development environment, and the amount of memory allocated to the virtual machine, it can take several minutes to configure and validate the services.

## Write a Hive query

1. In __Server Explorer__, right-click on the entry for the local cluster that you added previously, and then select __Write a Hive query__.

    ![Write a hive query](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    This opens a new query window that allows you to quickly type up and submit a query to the sandbox.

2. In the new query window, enter the following:

        select count(*) from sample_08;
    
    From the top of the query window, make sure that configuration for the local cluster is selected, and then select __Submit__. Leave the other values (__Batch__ and server name,) at the default values.

    ![query window and submit button](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

3. Once you submit the query, the job status will appear. This provides information on the job as it is processed by Hadoop. The __Job State__ entry provides the current status of the job. The state will be updated periodically, or you can use the refresh icon to manually refresh the state.

    ![Job state](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Once the __Job Status__ changes to __Finished__, a Directed Acyclic Graph (DAG) is displayed. This describes the execution path that was determined by Tez (the default execution engine for Hive on the Hortonworks Sandbox.) 
    
    > [AZURE.NOTE] Tez is also the default when using Linux-based HDInsight clusters. It is not the default on Windows-based HDInsight; to use it there, you must add the line `set hive.execution.engine = tez;` to the beginning of your Hive query. 

    Use the __Job Output__ link to view the output. In this case, it is __823__; the number of rows in the sample_08 table. You can view diagnostics information about the job by using the __Job Log__ and __Download YARN Log__ links.

4. You can also run Hive jobs interactively by changing the __Batch__ field to __Interactive__, and then select __Execute__. 

    ![Interactive query](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    This streams the output log generated during processing to the __HiveServer2 Output__ window.
    
    > [AZURE.NOTE] This is the same information that is available from the __Job Log__ link after a job has completed.

    ![HiveServer2 output](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## Create a Hive project

You can also create a project that contains multiple Hive scripts. This is useful when you have related scripts that you need to keep together, or maintain using version control systems.

To create a Hive project, open Visual Studio and select __File__, __New__, and then__Project__. From the list of projects, expand __Templates__, __Azure Data Lake__ and then select __HIVE (HDInsight)__. From the list of templates, select __Hive Sample__. Enter a name, location, and then select __OK__.

The __Hive Sample__ project contains two scripts, __WebLogAnalysis.hql__ and __SensorDataAnalysis.hql__. You can submit these using the same __Submit__ button at the top of the window.

## Create a Pig project

You can also create a project that contains Pig Latin scripts, which can be ran with Pig on the sandbox.

1. Open Visual Studio and select __File__, __New__, and then __Project__. From the list of projects, expand __Templates__, __Azure Data Lake__, and then select __Pig (HDInsight)__. From the list of templates, select __Pig Application__. Enter a name, location, and then select __OK__.

2. [TBD] when I have working bits

## View jobs

From __Server Explorer__, right-click on the local sandbox entry, and then select __View Jobs__. This will display a list of jobs that have been submitted to the sandbox. You can select each job and view information about it.

## View Hive databases

1. From __Server Explorer__, expand the __HDInsight local cluster__ entry, and then expand __Hive Databases__.

2. Expand the __Default__ database to display the tables stored in this database.

3. Right-click on __Default__ and select __Create Table__. [TBD]

3. [TBD] Delete tables

5. [TBD] Properties
