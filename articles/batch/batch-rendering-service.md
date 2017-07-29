---
title: Use the Azure Batch Rendering service to render in the cloud | Microsoft Docs
description: Render jobs on Azure virtual machines directly from Maya and on a pay-per-use basis.
services: batch
author: tamram
manager: timlt

ms.service: batch
ms.topic: article
ms.date: 07/25/2017
ms.author: tamram
---

# Get started with the Batch Rendering service

The Azure Batch Rendering service offers cloud-scale rendering capabilities on a pay-per-use basis. The Batch Rendering service handles job scheduling and queueing, managing failures and retries, and auto-scaling according to the needs of your job. The Batch Rendering service supports the Autodesk Maya and Autodesk Arnold rendering engines. You can use Maya and Arnold without having to manage licenses or infrastructure. The Batch plug-in for Maya 2017 makes it easy to start a rendering job on Azure right from your desktop. 

## Supported rendering engines

The Batch Rendering service currently supports the following rendering engines:

- Autodesk Maya
- Autodesk Arnold
- 3DS Max (???is this supported in preview - it's not in the portal?)

## Prerequisites

To use the Batch Rendering service, you need:

- An [Azure account](https://azure.microsoft.com/en-us/free/). 
- **An Azure Batch account.** For guidance on creating a Batch account in the Azure portal, see [Create a Batch account with the Azure portal](batch-account-create-portal.md). Create your Batch account with its pool allocation mode set to Batch Service.
- **An Azure Storage account.** You can create a storage account automatically when you set up your Batch account. You can also use an existing storage account. To learn more about Storage accounts, see [How to create, manage, or delete a storage account in the Azure portal](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

To use the Batch plug-in for Maya, you need:

- **Maya 2017**
- **Arnold for Maya**

You can also use the [Azure portal](https://portal.azure.com) to create pools of virtual machines that are pre-configured with Maya and Arnold. You can use the portal to monitor jobs and diagnose failed tasks by downloading application logs and by remotely connecting to individual VMs using RDP or SSH.

## Basic Batch concepts

Before you begin using the Batch Rendering service, it's helpful to be familiar with a few Batch concepts, including compute nodes, pools, and jobs. To learn more about Azure Batch in general, see [Run intrinsically parallel workloads with Batch](batch-technical-overview.md).

### Pools

Batch is a platform service for running compute-intensive work, like rendering, on a managed collection, or **pool**, of **compute nodes**. Each compute node in a pool is an Azure virtual machine (VM) running either Linux or Windows. 

For more information about Batch pools and compute nodes, see the [Pool](batch-api-basics.md#pool) and [Compute node](batch-api-basics.md#compute-node) sections in [Develop large-scale parallel compute solutions with Batch](batch-api-basics.md).

### Jobs

A Batch **job** is a collection of tasks that run on the compute nodes in a pool. When you submit a rendering job, Batch divides the job into tasks and distributes the tasks to the compute nodes in the pool to run (???is this an accurate description?).

For more information about Batch jobs, see the [Job](batch-api-basics.md#job) section in [Develop large-scale parallel compute solutions with Batch](batch-api-basics.md).

## Use the Batch plug-in for Maya to submit a render job

With the Batch plug-in for Maya, you can submit a job to the Batch Rendering service right from Maya. The following sections describe how to configure the job from the plug-in and then submit it. 

### Load the Batch plug-in in Maya

The Batch plug-in is available on [GitHub](https://github.com/Azure/azure-batch-maya). Clone the repository to a directory of your choice. You can load the plug-in directly from the *azure_batch_maya* directory.

To load the plug-in in Maya:

1. Run Maya.
2. Open **Window** > **Settings/Preferences** > **Plug-in Manager**.
3. Click **Browse**.
4. Navigate to and select *azure_batch_maya/plug-in/AzureBatch.py*.

### Authenticate access to your Batch and Storage accounts

To use the plug-in, you need to authenticate using your Azure Batch and Azure Storage account keys. To retrieve your account keys:

1. Display the plug-in in Maya, and select the **Config** tab.
2. Navigate to the [Azure portal](https://portal.azure.com).
3. Select **Batch Accounts** from the left-hand menu. If necessary, click **More Services** and filter on _Batch_.
4. Locate the desired Batch account in the list.
5. Select the **Keys** menu item to display your account name, account URL, and access keys:
    - Paste the Batch account URL into the **Service** field in the Batch plug-in.
    - Paste the account name into the **Batch Account** field.
    - Paste the primary account key into the **Batch Key** field.
7. Select Storage Accounts from the left-hand menu. If necessary, click **More Services** and filter on _Storage_.
8. Locate the desired Storage account in the list.
9. Select the **Access Keys** menu item to display the storage account name and keys.
    - Paste the Storage account name into the **Storage Account** field in the Batch plug-in.
    - Paste the primary account key into the **Storage Key** field.
10. Click **Authenticate** to ensure that the plug-in can access both accounts.

Once you have successfully authenticated, the plug-in sets the status field to **Authenticated**: 

![Authenticate your Batch and Storage accounts](./media/batch-rendering-service/authentication.png)

### Configure a pool for a render job

After you have authenticated your Batch and Storage accounts, set up a pool for your rendering job. The following sections walk you through the available options:

#### Specify a new or existing pool

To specify a pool on which to run the render job, select the **Submit** tab. This tab offers options for creating a pool or selecting an existing pool:

- You can **auto provision a pool for this job** (the default option). When you choose this option, Batch creates the pool exclusively for the current job, and automatically deletes the pool when the render job is complete. This option is best when you have a single render job to complete.
- You can **reuse an existing persistent pool**. If you have an existing pool that is idle, you can specify that pool for running the render job by selecting it from the dropdown. Reusing an existing persistent pool saves the time required to provision the pool. Use this option when you have a continuous need to run render jobs. 
- You can **create a new persistent pool**. Choosing this option creates a new pool for running the job. It does not delete the pool when the job is complete, so that you can reuse it for future jobs.

You can also create a pool using the Azure portal. 

#### Specify the OS image to provision

You can specify the type of OS image to use to provision compute nodes in the pool on the **Env** (Environment) tab. Batch currently supports the following image options for rendering jobs:

|Operating System  |Image  |
|---------|---------|
|Linux     |Batch CentOS Preview ???Names are different in portal Offer field vs plugin - does this matter?         |
|Windows     |???What text will appear in plug-in for Windows? I only have the screenshot....         |

#### Choose a VM size

You can specify the VM size on the **Env** tab. Azure now supports [GPU](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) VMs for both Linux and Windows, which may be a good choice for rendering jobs. For more information about all available VM sizes, see [Linux VM sizes in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) and [Windows VM sizes in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes). 


???What is default OS and size selected by plug-in?
???Are all sizes supported, or only a subset? From portal, it looks like A, Av2, D, Dv2 series?
???Any other guidance for choosing the right one? Cost/performance tradeoffs?

![Specify the VM OS image and size on the Env tab](./media/batch-rendering-service/environment.png)

#### Specify licensing options

You can specify the license servers you wish to use on the **Env** tab. Options include:

- **Maya**, which is enabled by default.
- **Arnold**, which is enabled if Arnold is detected as the active render engine in Maya.

 If you wish to render using your own license server, you can deselect these license options and configure your license end point by adding the appropriate environment variables to the table. 

> [!IMPORTANT]
> You are billed for use of the license servers as long as VMs are running in the pool, regardless of whether they are being used for rendering.
>
>

#### Manage persistent pools

You can manage an existing persistent pool on the **Pools** tab. Selecting a pool from the list displays the current state of the pool, including how many nodes are running, the selected VM image, the VM type, and the licenses that are deployed to that pool.

From the **Pools** tab, you can also delete the pool and resize the number of VMs in the pool. You can resize a pool to 0 nodes to avoid incurring costs in between workloads.

![View, resize, and delete pools](./media/batch-rendering-service/pools.png)

### Configure a render job for submission

Once you have specified the parameters for the pool that will run the render job, configure the job itself. 

#### Specify scene parameters

The Batch plug-in detects which rendering engine you're currently using in Maya, and displays the appropriate render settings on the **Submit** tab based on the settings found in the scene file. These settings include the start frame, end frame, output prefix, and frame step. You can override the scene file render settings by specifying different settings in the plug-in. Changes you make to the plug-in settings are not persisted back to the scene file render settings, so you can make changes on a job-by-job basis without needing to reupload the scene file.

The plug-in warns you if the render engine that you selected in Maya is not supported.

If you load a new scene while the plug-in is open, click the **Refresh** button to make sure the settings are updated.

#### Resolve asset paths

When you load the plug-in, it scans the scene file for any external file references. These references are displayed in the **Assets** tab. If a referenced path cannot be resolved, the plug-in attempts to locate the file in a few default locations, including the scene file location, the current project's _sourceimages_ directory, and the current working directory. If the asset still cannot be located, it is listed with a warning icon:

![Missing assets are displayed with a warning icon](./media/batch-rendering-service/missing_assets.png)

If you know the location of an unresolved file reference, you can click the warning icon to be prompted to add a search path. The plug-in then uses this search path to attempt to resolve any missing assets. You can add any number of additional search paths.

When a reference is resolved, it is listed with a green light icon:

![Resolved assets show a green light icon](./media/batch-rendering-service/found_assets.png)

If the plug-in has not detected other references, you can add additional files or directories with the **Add Files** and **Add Directory** buttons. If you load a new scene while the plug-in is open, be sure to click **Refresh** to update the scene's references.

#### Upload assets to a container in Azure Storage

When you submit a render job, the referenced files displayed in the **Assets** tab are automatically uploaded to a container in Azure Storage. You can also upload the asset files independently of a render job, using the **Upload** button on the **Assets** tab. The destination container name is specified in the **Project** field. It is named after the current Maya project by default. When asset files are uploaded to the container, the file structure of the project is preserved. 

Once uploaded, assets can be referenced by any number of render jobs. All uploaded assets are available to any job that references the container, whether or not they are included in the scene. To change the destination container referenced by your next job, change the name in the **Project** field in the **Assets** tab. If there are referenced files that you wish to exclude from uploading, unselect them using the green button beside the listing.

#### Submit and monitor the render job

After you have configured the render job in the plug-in, click the **Submit Job** button on the **Submit** tab to submit the job to Batch. 

You can monitor a job that is in progress from the **Jobs** tab on the plug-in. Select a job from the list to display the current state of the job. You can also use this tab to cancel and delete jobs, as well as to download the outputs and rendering logs. 

To download outputs, modify the **Outputs** field to set the desired destination directory. Click the gear icon to start a background process that watches the job and downloads outputs as it progresses: 

![View job status and download outputs](./media/batch-rendering-service/jobs.png)

You can close Maya without disrupting the download process.

## Use the Azure portal to manage and monitor the Batch Rendering service

You can use the [Azure portal](https://portal.azure.com) to:

- Create pools of virtual machines that are pre-configured with Maya and Arnold.
- Monitor jobs and diagnose failed tasks by downloading application logs.
- Remotely connecting to individual VMs using RDP or SSH.

### Create pools of VMs for rendering




## Troubleshooting

???If we want to include this section, can someone provide a few tips?

TBD

## Rendering service FAQ

TBD

## Next steps

TBD