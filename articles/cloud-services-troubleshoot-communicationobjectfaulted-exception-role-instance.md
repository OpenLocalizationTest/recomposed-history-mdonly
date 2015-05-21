<properties 
   pageTitle="System.ServiceModel.CommunicationObjectFaultedException During Role Instance Start-up"
   description=""
   services="cloud-services"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""/>
<tags 
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="05/12/2015"
   ms.author="adegeo" />

## System.ServiceModel.CommunicationObjectFaultedException During Role Instance Start-up

**Applies to**: Azure SDK 1.3 and later

**Symptom**:  This topic covers the possible causes and resolutions for the System.ServiceModel.CommunicationObjectFaultedException error message.

You may encounter a **This communication object, System.ServiceModel.Channels.ServiceChannel, cannot be used for communication because it is in the Faulted state** error when you start your role in the local development environment, which uses the compute emulator.

![Communication Object Faulted Exception](./media/communicationobjectfaultedexception-role-instance/IC457000.png)

**Cause 1**: The Web.config file is marked read-only in compute emulator.

In the SDK 1.3 release, this dialog box appears in the Azure compute emulator when the web.config file is marked as read-only. The error does not occur when deploying to Azure in the cloud, because file attributes are reset in any cloud deployment, thus making the web.config file writable. Note that the compute emulator does not copy the site’s content during execution.

**Resolution**: Clear the read-only attribute from the web.config file.  If you are using a source control system, you may need to check-out the file.

In SDK 1.3 to simplify the use of ASP.NET, the Azure environment automatically configures the ASP.NET machine key on a per-site basis using the site’s web.config file.  The automatically-supplied machine key is identical for all instances of a given site, but is different in all other cases (across deployments, etc.).

In prior releases, the machine key was set at the machine level.

**Cause 2**: Multiple role instances are writing to same configuration file in compute emulator.

This error can occur in the SDK 1.3 release when launching multiple instances of a given IIS-based web role.  Since all instances refer to the same on-disk location, they attempt to write to the same web configuration file causing intermittent crashes during role startup.

**Resolution**: Limit the instance count to one for any given role when using the compute emulator. Before deploying to Azure, reset the instance count to a higher value as desired.

**Cause 3**: The project has a very large number of files.

This error can occur in the SDK 1.3 release when a role starts that has a very large number of files. You may encounter this error starting when there are 2000 to 5000 files present. It is not possible to specify a precise number of files since the error is timing dependent and other processes running on the VM may affect the performance.During startup, all files in the project must have the correct ACLs applied. This process can take an extended period of time and can cause intermittent crashes during role startup.

**Resolution**: Limit the number of files included in the project.

**Cause 4**: Errors in the Web.config file.

These errors can occur in the SDK 1.3 release when the web.config file has configuration errors. This is a partial list of possible errors:

- Invalid XML formatting: missing closing tags, unmatched quotes, etc.

- Duplicate **<configSections>**

- Duplicate **machineKey** elements

- The **machineKey** validation set to something other than: AES, SHA1, 3DES, or MD5

**Resolution**: Correct the errors in the Web.config file.

### See Also

[Known Issues in Azure Cloud Services](https://msdn.microsoft.com/library/gg508668)

