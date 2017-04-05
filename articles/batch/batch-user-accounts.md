---
title: Run tasks under user accounts in Batch .NET | Microsoft Docs
description: Run tasks under user accounts in Batch .NET
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: ''
tags: 

ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/04/2017
ms.author: tamram
---

# Run tasks under user accounts in Batch

A task running in Azure Batch always runs under a user account. By default, that user account is a standard user account without administrator permissions. Also by default, each task runs under a separate user account. These default user account settings are sufficient for most cases. For certain scenarios, however, it's useful to be able to configure the user account under which you want a task to run. 

## Types of user accounts

Azure Batch provides two types of user accounts for running tasks:

- **The AutoUser account.** The AutoUser is a built-in user account that's created automatically by the Batch service. You can specify the elevation level for the AutoUser, as well as its scope. The AutoUser is the default user account for running tasks. 

    Configure the AutoUser account to run tasks in scenarios where you need to run a task with elevated access, or where you want a set of tasks on a node to run under the same user account. 

- **A named user account.** You can specify one or more named user accounts for a pool when you create the pool. Each user account is created on each node of the pool. In addition to the account name, you can specify the user account password, elevation level, and, for Linux pools, the SSH private key. When you add a task, you can specify the named user account under which that task should run.

    Use named user accounts in scenarios where you require password-less SSH between Linux nodes. 

> [!IMPORTANT] 
> Batch .NET version 6.x replaces the **RunElevated** property with the new **UserIdentity** property of a task. This is a breaking change that requires that you update your code to use the new version. A call made using version 6.x that includes the **RunElevated** property will fail. See the section titled [Update your code for version 6.x](#update-your-code-for-version-6-x) for quick guidelines for updating your Batch .NET code for 6.x.
>
>

> [!NOTE] 
> The user accounts discussed in this article do not support Remote Desktop Protocol (RDP) or Secure Shell (SSH), for security reasons. 
>
> To connect to a node running the Linux virtual machine configuration via SSH, see [Use Remote Desktop to a Linux VM in Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). To connect to nodes running Windows via RDP, see [Connect to a Windows Server VM](../virtual-machines/windows/connect-logon.md). 
> 
> To connect to a node running the cloud service configuration via RDP, see [Enable Remote Desktop Connection for a Role in Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## User account access to files and directories

Both the AutoUser account and a named user account have read/write access to the task’s working directory and shared directory, and read access to the startup and job prep directories (???what is the job prep directory - we don't appear to doc this?). (???Question from Ivan - What about the multi-instance tasks common directory path?) 

If a task runs under the same account that was used for the start task, the task has read-write access to the start task directory. Otherwise the task has only read access.  (Correct?)  Similarly for the job prep directory ???need more info on this.

For more information on accessing files and directories from a task, see [Develop large-scale parallel compute solutions with Batch](https://docs.microsoft.com/en-us/azure/batch/batch-api-basics#files-and-directories).

## Elevated access for tasks 

The user account's elevation level indicates whether a task runs with elevated access. Both the AutoUser account and a named user account can run with elevated access. The two options for elevation level are:

- **NonAdmin (Default):** The task runs as a standard user without elevated access. The default elevation level for a Batch user account is always **NonAdmin**.
- **Admin:** The taks runs as a user with elevated access and operates with full Administrator permissions. 

## The AutoUser account

By default, tasks run in Batch under the AutoUser account, as a standard user without elevated access, and with task scope. 

The AutoUser's scope specifies whether the AutoUser's configuration applies to a single task (task scope), or applies to all tasks running on all nodes in a pool (pool scope). The default scope is different on Windows and Linux nodes:

- On Windows nodes, tasks run under task scope by default. With task scope, each task running on the same node runs under a different AutoUser account. The behavior is the same for nodes running the cloud service configuration or the virtual machine configuration. You can also run tasks on Windows nodes under pool scope.
- Linux nodes always run under pool scope. With pool scope, all tasks running on a given node run under the same AutoUser account.

> [!NOTE] 
> Specifying task scope for the AutoUser on a Linux node results in an error. ???true? if so, what is the error?   
>
>

> [!IMPORTANT] 
> Tasks running under task scope do not have de facto access to other tasks on a node. However, a malicious user with access to the account could work around the task scope restriction, by submitting a task that runs with administrator privileges and grants itself access to other task directories. A malicious user could also use RDP or SSH to connect to a node. It's important to protect access to your Batch account keys to prevent such a scenario, and be sure to regenerate your keys if you suspect your account may have been compromised.
>
>

### Run a task as the AutoUser with elevated access

You can configure the AutoUser for administrator privileges when you need to run a task with elevated access. For example, elevated access is useful for a start task when the start task needs to install software on the node and that software installation requires elevated access.

> [!NOTE] 
> In general, it's best to use elevated access only when necessary. Best practices recommend granting the minimum privilege necessary to achieve the desired outcome. If you install software for the current user, instead of for all users, you may be able to avoid granting elevated access to tasks other than the start task (???correct).
>
>

To set the AutoUser's elevation level in Batch .NET, use the task's **UserIdentity** property. The **AutoUserSpecification** class configures the AutoUser account under which to run the task.

This code snippet sets the elevation level for the AutoUser to `Admin`:

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));
```

### Run a task as the AutoUser with pool scope

When a node is provisioned, two pool-wide AutoUser accounts are created on each node in the pool, one with elevated access, and one without elevated access (???is this an accurate way to describe it?). Setting the AutoUser's scope to pool scope for a given task runs the task under one of these two pool-wide AutoUser accounts. Each task that runs as the AutoUser with its scope set to pool scope and with the same elevation level runs under the same pool-wide AutoUser account. The advantage to running under the same AutoUser account is that tasks are able to share data with other tasks.

> [!NOTE] 
> The two pool-wide AutoUser accounts are separate accounts. Tasks running under the pool-wide AutoUser account with elevated access are not able to share data with tasks running under the account without elevated access, and vice versa. 
>
>

Sharing secrets between tasks is one scenario where running tasks under one of the two pool-wide AutoUser accounts is useful. For example, suppose a start task needs to provision a secret onto the node that other tasks can use. You could use the Windows Data Protection API (DPAPI), but it requires administrator privileges. Instead, you can protect the secret at the user level. Tasks running under the same user account can access the secret without elevated access.

Another scenario where you may want to run tasks under an AutoUser account with pool scope is a Message Passing Interface (MPI) file share. An MPI file share is useful when the nodes in the MPI task need to work on the same file data. The head node creates a file share that the child nodes can access if they are running under the same AutoUser account. 

To set the AutoUser's scope in Batch .NET, use the task's **UserIdentity** property, as shown in this code snippet. If the elevation level is omitted, the task runs under the standard pool-wide AutoUser account.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## Named user accounts

The named user account enables password-less SSH between Linux nodes. You can use a named user account with Linux nodes that need to run multi-instance tasks. Each node in the pool can run tasks under a user account defined on the whole pool. See [Use multi\-instance tasks to run MPI applications](batch-mpi.md) for more information about multi-instance tasks.

???what is the purpose of the user account password - this is not an SSH password, but SSH is the scenario it enables?
???Is the Linux scenario currently the only useful one, or are there Windows scenarios for the named user as well?

???From Ivan's notes: Useful when… what?  You want some tasks to be able to run as the same user, but not others?  You want predictable naming of the user account?  (What is the use case for that – connecting to on-premise machines or VMs outside Batch?)

### Create named user accounts

A named user is always created on a pool, and is available to all nodes in the pool and all tasks running on those nodes. You may define any number of named users for a pool (???is there a limit to be concerned with?). Then when you add a task to a job running on a pool, you can specify that the task runs under a user account that you previously defined on the pool.

When the pool is resized, the named user account is available to any new node in the pool. 

To create named user accounts in Batch .NET, specify a list of user accounts for the [CloudPool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool).[UserAccounts](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.useraccounts) property.

This code snippet shows how to create both admin and non-admin named accounts on a pool:

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicated: 3,                                                         
    virtualMachineSize: "small",                                                
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

pool.UserAccounts = new List<UserAccount>()
{
    new UserAccount(AdminUserAccountName, password, ElevationLevel.Admin),
    new UserAccount(NonAdminUserAccountName, password, ElevationLevel.NonAdmin),
};
 
currentPool.Commit();
```

### Run a task under a named user account with elevated access

To run a task as an elevated user, set the task's **UserIdentity** property to a named user account that was created with its **ElevationLevel** property set to `Admin`.

This code snippet specifies that the task should run under a named user account with admin permissions. This named user account is the same one that was defined on the pool when it was created:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## Update your code for version 6.x



