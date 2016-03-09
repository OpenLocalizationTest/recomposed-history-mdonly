<properties
	pageTitle="Easy application installation and management in Azure Batch | Microsoft Azure"
	description="Use the Applications feature of Azure Batch to easily manage multiple applications and versions for installation on Batch compute nodes."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="03/04/2016"
	ms.author="marsma" />

# Install and manage task applications with Azure Batch application packages

The application packages feature of Azure Batch provides easy management and deployment of applications for your jobs' tasks. With application packages, you can easily upload and manage multiple versions of the applications run by your tasks, including binaries and support files, then automatically deploy one or more of these applications to the compute nodes in your pool.

In this article, you will learn how to upload and manage application packages using the Azure Portal, then install them on a pool's compute nodes using the [Batch .NET][api_net] library.

> [AZURE.IMPORTANT] The application packages feature discussed in this article is compatible *only* with Batch pools created *after* 10 March 2016. Application packages will not be deployed to compute nodes in pools created before this date.

## Benefits of application packages

Application packages can simplify the code in your Batch solution, as well as lower the overhead required in managing the applications your tasks run.

With application packages, your pool's start task doesn't have to specify a long list of individual resource files to install on the nodes. You don't have to manually manage multiple versions of these files in Azure Storage, or on your nodes. And, you don't need to worry about generating [SAS URLs](../storage/storage-dotnet-shared-access-signature-part-1.md) to provide secure access to the files in Azure Storage.

Batch handles the details of working with Azure Storage in the background to securely store and deploy your application packages to compute nodes, so both your code and your management overhead can be simplified.

> [AZURE.NOTE] The application packages feature supersedes the "Batch Apps" feature available in previous versions of the service. We recommend that you always use the latest API version when working with Batch.

## Applications and application packages

Within Azure Batch, an **application** refers to a set of versioned binaries that can be automatically downloaded to the compute nodes in your pool. An **application package** refers to a *specific set* of those binaries, and represents a given *version* of the application.

![Application packages][1]

### Applications

An application in Batch contains one or more application packages. It specifies configuration options for the application such as the default application package to install on compute nodes, and whether its packages may be updated or deleted.

### Application packages

An application package is a ZIP file containing the application binaries and supporting files required for execution by your tasks. Each application package represents a specific version of the application. When you create a pool in the Batch service, you can specify one or more of these applications and (optionally) a version, and those application packages will be downloaded automatically and extracted onto each node as the node joins the pool.

> [AZURE.IMPORTANT] There are restrictions on the number of applications and application packages within a Batch account, as well as the maximum application package size. See [Quotas and limits for the Azure Batch service](batch-quota-limit.md) for details on these limits.

## Upload and manage applications

Using the Azure portal, you can add, update, and delete application packages, and configure default versions for each application. While application management is supported only in the Azure portal at the time of this writing, additional methods including programmatic management using the [Batch Management .NET](batch-management-dotnet.md) library will be supported in the future.

In the next few sections, we'll first cover associating a Storage account with your Batch account, then review the package management features available in the Azure portal.

### Associate a Storage account

In order to use application packages, you must first associate an Azure Storage account with your Batch account. If have not yet configured a Storage account for your Batch account, the Azure portal will notify you the first time you click the *Applications* tile in the Batch account blade.

![Associate Storage account][9]

The Batch service uses the associated Storage account for the storage and retrieval of application packages. Once you've associated the two accounts, Batch can automatically--and securely--deploy application packages to your compute nodes.

If you do not have a storage account, see the "Create a storage account" section of [About Azure storage accounts](../storage/storage-create-storage-account.md) for step-by-step instructions. Once you've created a storage account, you may then link it to your Batch account using the *Storage Account* blade.

> [AZURE.WARNING] Because Batch stores your application packages using Azure Storage, you are [charged as normal][storage_pricing] for the block blob data. Be sure to consider the size and number of your application packages, and periodically remove deprecated packages to minimize cost.

### View current applications

To view the applications in your Batch account, click the **Applications** tile in the Batch account blade.

![Applications tile][2]

This opens the Applications blade:

![List applications][3]

The Applications blade displays the ID of each application in your account, as well as the following properties:

* **Packages** - The number of versions associated with this application.
* **Default version** – If you do not specify a version when setting the application for a pool, this version will be installed.
* **Allow Updates** – If this is set to *No*, package updates and deletions are disabled for that application--only new application packages can be added.

### View application details

Clicking on an application in the *Applications* blade displays the details blade for that application.

![Application details][4]

In the application details blade, you can configure the following settings for your application.

* **Allow updates** - Specify whether its application packages can be updated or deleted (see "Update or Delete an application package" below).
* **Default version** - Specify a default application package to deploy to compute nodes.
* **Display name** - This is a "friendly" name that your Batch solution can use when displaying information about the application, such as in the UI of a service you provide your customers through Batch.

### Add a new application

To create a new application, add an application package using a new, unique application ID. The first application package that you add using the new application ID will also create the new application.

Click the **Add** button on the *Applications* blade to open the *New application* blade.

![New application][5]

The *New application* blade provides the following fields for specifying the settings for your new application and application package.

**Metadata**

This provides an optional method for supplying the application ID and package version. You can either supply the application metadata manually, or upload a JSON file that contains the metadata. When you use the Metadata method to specify the ID and version, the "Application" and "Version" fields described below are populated automatically.

Use the following JSON format to specify the application package metadata:

```
{
    "id": "litware",
    "version": "1.1001.2b"
}
```


**Application**

This specifies the ID of your new application. Application IDs are subject to the standard Azure Batch ID validation rules:

* Can contain any combination of alphanumeric characters, including hyphens and underscores.
* Cannot contain more than 64 characters.
* Must be unique within the Batch account.
* Case preserving, and case insensitive.

**Version**

Specifies the version of the application package you are uploading. Version strings are subject to the following validation rules:

* Can contain any combination of alphanumeric characters, including hyphens, underscores, and periods.
* Cannot contain more than 64 characters.
* Must be unique within the application.
* Case preserving, and case insensitive.

**Application Package**

This specifies the path to a ZIP file containing the application binaries and any support files required to execute the application. You may enter the path to the ZIP file manually, or browse to the file by clicking the folder icon next to the "Select a file" text box.

Once you've entered the required information, click the "OK" button at the bottom of the *New application* blade and the file will begin uploading to Azure Storage. When the upload operation completes, you will be notified and the blade will close. Note that depending on the size of the file that you are uploading and the speed of your network connection, this operation may take some time.

> [AZURE.WARNING] Do not close the *New application* blade before the upload operation is complete. Doing so will abort the upload process.

### Add a new application package

To add a new application package to an existing application, select an application in the *Applications* blade, then click the "Add" button to display the *New application package* blade.

![New application package][8]

As you can see, the fields in the *New application package* blade match those of the *New application* blade, except for the disabled *Application* text box that displays the ID of the selected application. As above, specify the version for your new package, supply a path to the ZIP file containing the application files, and click "OK" to upload the package.

### Update or Delete an application package

To update or delete an existing application package, open the details blade for the application, click the ellipses on the application package row you wish to modify, and select the action you wish to perform.

![Update or delete packages][7]

**Update**

When you click "Update", the *Update application package* blade is displayed. This blade is similar to the *New application package* blade, however only the package selection field is enabled, allowing you to specify a new ZIP file to upload.

**Delete**

When you click "Delete", you are asked to confirm the deletion of the package, and Batch deletes the package from Storage. If you delete the default version of an application, the default version setting is removed for the application.

## Install applications on compute nodes

Now that we've covered uploading and managing the application packages in the Azure portal, we are ready to discuss actually using those applications for your Batch tasks.

To install an application package on the compute nodes in a pool, you specify one or more application package *references* for the pool. In Batch .NET, you do so by adding one or more `CloudPool.ApplicationPackageReferences` when you create the pool, or to an existing pool.

The `ApplicationPackageReference` class specifies an application ID and version to install on a pool's compute nodes.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(poolId: "myPool",
                                          osFamily: "4",
                                          virtualMachineSize: "small",
                                          targetDedicated: "1");

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "10.7" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

## Execute the installed applications

As each compute node joins a pool (or is rebooted or reimaged) the packages you've specified are downloaded and extracted to a named directory within `AZ_BATCH_ROOT_DIR` on the node. Batch also creates an environment variable for your task command lines to use when calling the application binaries--this variable adheres to the following naming scheme:

`AZ_BATCH_APP_PACKAGE_appid#version`

For example, if you specify that version 2.7 of application *blender* be installed, your tasks can access its binaries by referencing the following environment variable in their command lines:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

If your application specifies a default version, you can reference the environment variable without the version string suffix. For example, if you had specified default version 2.7 for the *blender* application within the Azure portal, your tasks can reference the following environment variable:

`AZ_BATCH_APP_PACKAGE_BLENDER`

The following code snippet shows how a task might be configured when a default version has been specified for the *blender* application.

```csharp
string taskId = "blendertask01";
string commandLine = @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -my -awesome -args";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

## Update a pool's application packages

If you've already specified an application package for a pool, you can specify a new package for the existing pool. All new nodes that join the pool will install the newly specified package, as will any existing node that is rebooted or reimaged. Compute nodes that are already in the pool when you update the package references do not automatically install the new application package.

In this example, the existing pool has version 2.7 of the *blender* application configured as one of its `CloudPool.ApplicationPackageReferences`. To update the pool's nodes with version 2.76b, specify a new `ApplicationPackageReference` with the new version, and commit the change.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Now that the new version has been configured, any *new* node joining the pool will have version 2.76b deployed to it. To install 2.76b on the nodes that are *already* in the pool, reboot (or reimage) them. Note that rebooted nodes will retain files from previous package deployments.

## List the applications in a Batch account

You can list the applications and their packages in a Batch account by using the `ApplicationOperations.ListApplicationSummaries` method.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## Next steps

- Next step one goes here
- Next step two goes here
- Maybe even a number three

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/

[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-app-pkg/app_pkg_01.png "Applications and application packages"
[2]: ./media/batch-app-pkg/app_pkg_02.png "Applications tile"
[3]: ./media/batch-app-pkg/app_pkg_03.png "List applications"
[4]: ./media/batch-app-pkg/app_pkg_04.png "Application details"
[5]: ./media/batch-app-pkg/app_pkg_05.png "New application"
[6]: ./media/batch-app-pkg/app_pkg_06.png "List applications"
[7]: ./media/batch-app-pkg/app_pkg_07.png "Update or delete packages"
[8]: ./media/batch-app-pkg/app_pkg_08.png "New application package"
[9]: ./media/batch-app-pkg/app_pkg_09.png "Associate Storage account"