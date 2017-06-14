---
title: Persist job and task output to Azure Storage with the Azure Batch service API | Microsoft Docs
description: Learn how to use Batch service API to persist Batch task and job output to Azure Storage.
services: batch
author: tamram
manager: timlt
editor: ''

ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/13/2017
ms.author: tamram

---


# Use the Batch service API to persist task data to Azure Storage

A task running in Azure Batch may produce output data when it runs. Task output data often needs to be stored for retrieval by other tasks in the job, the client application that executed the job, or both. Tasks write output data to the file system of a Batch compute node, but all data on the node is lost when it is reimaged. Therefore it's important to persist task output that you'll need later to a data store such as Azure Storage.

Starting with version 2017-05-01, the Batch service API supports persisting output data to Azure Storage for tasks and job manager tasks that run on pools with the virtual machine configuration. When you add a task, you can specify a container in Azure Storage as the destination for the task's output. The Batch service then writes any output data to that container when the task is complete.

An advantage to using the Batch service API to persist task output is that you do not need to modify the application that the task is running. Instead, with a few simple modifications to your client application, you can persist the task's output from within the code that creates the task.   

## When do I use the Batch service API to persist task output?

Azure Batch provides more than one way to persist task output. Using the Batch service API is a convenient approach that's best suited to these scenarios:

- You want to persist output from your client application, without modifying the application that your task is running.
- You want to persist output from Batch tasks and job manager tasks in pools created with the virtual machine configuration.
- You want to persist output to an Azure Storage container with an arbitrary name.
- You want to persist output to an Azure Storage container named according to the [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

If your scenario differs from those listed above, you may need to consider a different approach. For example, the Batch service API does not currently support streaming output to Azure Storage while the task is running. To stream output, consider using the Batch File Conventions library, available for .NET. For other languages, you'll need to implement your own solution. For more information on other options for persisting task output, see [Persist job and task output to Azure Storage](batch-task-output.md). 

## Link an Azure Storage account to your Batch account

To persist task output to Azure Storage using the Batch service API, you must first [link an Azure Storage account](batch-application-packages.md#link-a-storage-account) to your Batch account. If you haven't done so already, link a Storage account to your Batch account by using the [Azure portal](https://portal.azure.com):

1. Navigate to your Batch account in the Azure portal. 
2. Under **Settings**, select **Storage Account**.
3. If you do not already have a Storage account associated with your Batch account, click **Storage Account** (None).
4. Select a Storage account from the list for your subscription. For best performance, use an Azure Storage account that is in the same region as the Batch account where your tasks are running.

## Create a container in Azure Storage

All task output is persisted to blobs in a container in Azure Storage. The container that is the destination for your output files needs to be created before the task runs, preferably before you submit your job. To create the container, use the appropriate Azure Storage client library or SDK. For more information about Azure Storage APIs, see the [Azure Storage documentation](https://docs.microsoft.com/azure/storage/).

For example, if you are writing your application in C#, use the [Azure Storage client library for .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). The following example shows how to create a container:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await conainer.CreateIfNotExists();
```

## Get a shared access signature for the container

After you create the container, get a shared access signature (SAS) with write access to the container. A SAS provides delegated access to the container. The SAS grants access with a specified set of permissions and over a specified time interval. The Batch service needs a SAS with write permissions to write task output to the container. For more information about SAS, see [Using shared access signatures \(SAS\) in Azure Storage](../storage/storage-dotnet-shared-access-signature-part-1.md).

When you get a SAS using the Azure Storage APIs, the API returns a SAS token string. This token string includes all parameters of the SAS, including the permissions and the interval over which the SAS is valid. To use the SAS to access a container in Azure Storage, you need to append the SAS token string to the resource URI. The resource URI, together with the appended SAS token, provides authenticated access to Azure Storage.

The following example shows how to get a write-only SAS token string for the container, then appends the SAS to the container URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken; 
```

## Specify output files for task output

To specify output files for a task, create a collection of [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile?view=azure-dotnet) objects and assign it to the [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles?view=azure-dotnet#Microsoft_Azure_Batch_CloudTask_OutputFiles) property when you create the task. 

The following .NET code example creates a task that writes random numbers to a file named `output.txt`. The example creates an output file for `output.txt` to be written to the container. The example also creates output files for any log files that match the file pattern `std*.txt` (_e.g._, `stdout` and `stderr`). The container URL requires the SAS that was created previously for the container. The Batch service uses the SAS to authenticate access to the container: 

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### Specify a file pattern for matching

When you specify an output file, you can use the [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern?view=azure-dotnet#Microsoft_Azure_Batch_OutputFile_FilePattern) property to specify a file pattern for matching. The file pattern may match zero files, a single file, or a set of files that are created by the task.

The **FilePattern** property supports standard filesystem wildcards such as \* (for non-recursive matches) and \*\* (for recursive matches). For example, the code sample above specifies the file pattern to match `std*.txt` non-recursively: 

`filePattern: @"..\std*.txt"`

To upload a single file, specify a file pattern with no wildcards. For example, the code sample above specifies the file pattern to match `output.txt`:

`filePattern: @"output.txt"`

### Specify an upload condition

The [Output​File​Upload​Options.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition?view=azure-dotnet#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) property permits conditional uploading of output files. A common scenario is to upload one set of files if the task succeeds, and a different set of files if it fails. For example, you may want to upload verbose log files only when the task fails and exits with a nonzero exit code. Similarly, you may want to upload result files only if the task succeeds, as those files may be missing or incomplete if the task fails.

The code sample above sets the **UploadCondition** property to **TaskCompletion**. This setting specifies that the file is to be uploaded after the tasks completes, regardless of the value of the exit code. 

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

For other settings, see the [Output​File​Upload​Condition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition?view=azure-dotnet) enum.

### Disambiguate files with the same name

The tasks in a job may produce files that have the same name. For example, `stdout.txt` and `stderr.txt` are created for every task that runs in a job. Because each task runs in its own context, these files don't conflict on the node's file system. However, when you upload files from multiple tasks to a shared container, you'll need to disambiguate files with the same name.

The [Output​File​Blob​Container​Destination.​Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path?view=azure-dotnet#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) property specifies the destination blob or virtual directory for output files. You can use the **Path** property to name the blob or virtual directory in such a way that output files with the same name are uniquely named in Azure Storage. Using the task ID in the path is a good way to ensure unique names and easily identify files.

If the **FilePattern** property is set to a wildcard expression, then all files that match the pattern are uploaded to the virtual directory specified by the **Path** property. For example, if the container is `mycontainer`, the task ID is `mytask`, and the file pattern is `..\std*.txt`, then the absolute URIs to the output files in Azure Storage will be similar to:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

If the **FilePattern** property is set to match a single file name, then the value of the **Path** property specifies the fully qualified blob name. If you anticipate naming conflicts with a single file from multiple tasks, then include the name of the virtual directory as part of the file name to disambiguate those files. For example, set the **Path** property to include the task ID, the delimiter character (typically a forward slash), and the file name:

`path: taskId + @"/output.txt"`

The absolute URIs to the output files for a set of tasks will be similar to:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

For more information about virtual directories in Azure Storage, see [List the blobs in a container](../storage/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container).


## Diagnose file upload errors

If uploading output files to Azure Storage fails, then the task moves to the **Completed** state and the [Task​Execution​Information.​Failure​Information](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation?view=azure-dotnet#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) property is set. Examine the **FailureInformation** property to determine what error occurred. For example, here is an error that occurs on file upload if the container cannot be found: 

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

On every file upload, Batch writes two log files to the compute node, `fileuploadout.txt` and `fileuploaderr.txt`. You can examine these log files to learn more about a specific failure. In cases where the file upload was never attempted, for example because the task itself couldn’t run, then these log files will not exist.

## Diagnose file upload performance

The `fileuploadout.txt` file logs upload progress. You can examine this file to learn more about how long your file uploads are taking. Keep in mind that
there are many contributing factors to upload performance, including the size of the node, other activity on the node at the time of the upload, whether the target container is in the same region as the Batch pool, how many nodes are uploading to the storage account at the same time, and so on.

## Use the Batch service API with the Batch File Conventions standard

When you persist task output with the Batch service API, you can name your destination container and blobs however you like. You can also choose to name them according to the [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). The File Conventions standard determines the names of the destination container and blob in Azure Storage for a given output file based on the names of the job and task. If you do use the File Conventions standard for naming output files, then your output files are available for viewing in the [Azure portal](https://portal.azure.com).

If you are developing in C#, you can use the methods built into the [Batch File Conventions library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). This library creates the properly named containers and blob paths for you. For example, you can call the API to get the correct name for the container, based on the job name:

```csharp
string containerName = job.OutputStorageContainerName();
```

If you are developing in a language other than C#, you will need to implement the File Conventions standard yourself.

## Next steps

- See the XXX code example to view and run code that shows how to write task output to Azure Storage.
- For more information on persisting task output with the File Conventions library for .NET, see [Use the Batch File Conventions standard to persist job and task data](batch-task-output-file-conventions.md).
- For information on other approaches for persisting output data in Azure Batch, see [Persist job and task output to Azure Storage](batch-task-output.md).

