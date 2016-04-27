<properties
   	pageTitle="Install Hadoop applications on HDInsight | Microsoft Azure"
   	description="Learn how to install HDInsight applications on HDInsight applications."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="04/27/2016"
   	ms.author="jgao"/>

# Install HDInsight applications

An HDInsight application is an application that users can install to a Linux-based HDInsight cluster. These applications can be developed by Microsoft, independent software vendors (ISV) or by yourself. In this article, you will learn how to install a published application. For installing your own application, see [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md). For defining custom HDInsight applications, see [Deine HDInsight applications](hdinsight-apps-define-applications.md).

The following are the published HDInsight applications: [jgao: list and description to be provided by Travis]

- **Data-Centric Security for HDInsight(Bluetalon)**: [jgao: description to be provided] 
- **Datameer powered by Azure(Datameer)**: 
- **DataTorrent RTS(DataTorrent)**: 
- **R Server for HDInsight (Microsoft)**: 

The instructions provided in this article use Azure Portal. You can also export the ARM template from the portal or obtain a copy of the ARM template from vendors, and use Azure PowerShell and Azure CLI to deploy the template.  See [Create Linux-based Hadoop clusters in HDInsight using ARM templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

### Prerequisites

If you want to install HDInsight applications on an existing HDInsight cluster, you must have an HDInsight cluster. To create one, see [Create clusters](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). You can also install HDInsight applications when you create an HDInsight cluster.

## Install applications to existing clusters

The following procedure shows you how to install HDInsight applications to an existing HDInsight cluster.

**To install an HDInsight application**

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Click **HDInsight Clusters** in the left menu.  If you don't see it, click **Browse**, and then click **HDInsight Clusters**.
3. Click an HDInsight cluster.  If you don't have one, you must create one first.  see [Create clusters](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).
4. From the **Settings** blade, click **Applications** under the **General** category. The **Installed Apps** blade lists all the installed applications. 

    ![hdinsight applications portal menu](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Click **Submit New** from the blade menu. You shall see a list of existing HDInsight applications.

    ![hdinsight applications installed apps](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)
6. Click one of the applications, and then click **Select**.

You can see the installation status from the portal notifications (click the bell icon on the top of the portal). After the application is installed, the application will appear on the Installed Apps blade.

## Install applications during cluster creation

You have the option to install HDInsight applications when you create a cluster. During the process, HDInsight applications are installed after the cluster is created and is in the running state. The following procedure shows you how to install HDInsight applications when you create a cluster.

**To install an HDInsight application**

1. Sign in to the [Azure  portal](https://portal.azure.com).
2. Click **NEW**, Click **Data + Analytics**, and then click **HDInsight**.
3. Enter **Cluster Name**: This name must be globally unique.
4. Click **Subscription** to select the Azure subscription that will be used for the cluster.
5. Click **Select cluster Type**, and then select:

    - **Cluster Type**: If you don't know what to choose, select **Hadoop**. It is the most popular cluster type.
    - **Operating System**: Select **Linux**.
    - **Version**: Use the default version if you don't know what to choose. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
    - **Cluster Tier**: Azure HDInsight provides the big data cloud offerings in two categories: Standard tier and Premium tier. For more information, see [Cluster tiers](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
6. Click **Applications**, click one of the published applications, and then click **Select**.
6. Click **Credentials** and then enter a password for the admin user. You must also enter an **SSH Username** and either a **PASSWORD** or **PUBLIC KEY**, which will be used to authenticate the SSH user. Using a public key is the recommended approach. Click **Select** at the bottom to save the credentials configuration.
8. Click **Data Source**, select one of the existing storage account or create a new storage account to be used as the default storage account for the cluster.
9. Click **Resource Group** to select an existing resource group, or click **New** to create a new resource group

10. On the **New HDInsight Cluster** blade, ensure that **Pin to Startboard** is selected, and then click **Create**. 

## List installed HDInsight apps and properties

The portal shows a list of the installed HDInsight applications for a cluster, and the properties of each installed application.

**To list HDInsight application and display properties**

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Click **HDInsight Clusters** in the left menu.  If you don't see it, click **Browse**, and then click **HDInsight Clusters**.
3. Click an HDInsight cluster.
4. From the **Settings** blade, click **Applications** under the **General** category. The Installed Apps blade lists all the installed applications. 
5. Click one of the installed applications to show the property. The property blade lists:

    - App name: application name.
    - Status: application status. 
    - Webpage: The URL of the web application that you have deployed to the edge node if there is any. The credential is the same as the HTTP user credentials that you have configured for the cluster.
    - HTTP endpoint: The credential is the same as the HTTP user credentials that you have configured for the cluster. 
    - SSH endpoint: You can use [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) to connect to the edge node. The SSH credentials are the same as the SSH user credentials that you have configured for the cluster.

## Connect to the edge node

You can connect to the edge node using HTTP and SSH. The endpoint information can be found from the [portal](#list-installed-hdinsight-apps-and-properties). For more information on using SSH, see [Use SSH with Linux-based Hadoop on HDInsight from Linux, Unix, or OS X](hdinsight-hadoop-linux-use-ssh-unix.md). 

The HTTP endpoint credentials are the HTTP user credentials that you have configured for the HDInsight cluster; the SSH endpoint credentials are the SSH credentials that you have configured for the HDInsight cluster.

## Troubleshoot

You can check the application installation status from the portal notification (Click the bell icon on the top of the portal). 

If an application installation failed, you can see the error messages and debug information from 3 places:

- HDInsight Applications: general error information.

    Open the cluster from the portal, and click Applications from the Settings blade:

    ![hdinsight applications application installation error](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)

- HDInsight script action: If the HDInsight Applications' error message indicates a script action failure, more details about the script failure will be presented in the script actions pane.

    Click Script Action from the Settings blade. Script action history shows the error messages

    ![hdinsight applications script action error](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
    
- Ambari Web UI: If the install script was the cause of the failure, use Ambari Web UI to check full logs about the install scripts.

    For more information, see [Troubleshooting](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).


## Next steps

- [Define HDInsight applications](hdinsight-apps-define-applications.md): learn how to develop ARM templates for deploying HDInsight applications.
- [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md): learn how to deploy an un-published HDInsight application to HDInsight.
