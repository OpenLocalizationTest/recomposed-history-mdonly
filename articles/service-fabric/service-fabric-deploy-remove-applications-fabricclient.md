---
title: Service Fabric application deployment | Microsoft Docs
description: How to deploy and remove applications in Service Fabric
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''

ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/05/2017
ms.author: ryanwi

---
# Deploy and remove applications using FabricClient
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [C#](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Once an [application type has been packaged][10], it's ready for deployment into an Azure Service Fabric cluster. Deployment involves the following three steps:

1. Upload the application package to the image store
2. Register the application type
3. Create the application instance

After an app is deployed and an instance is running in the cluster, you can delete the app instance and its application type. To completely remove an app from the cluster involves the following steps:

1. Remove (or delete) the running application instance
2. Unregister the application type if you no longer need it
3. Remove the application package from the image store

If you use [Visual Studio for deploying and debugging applications](service-fabric-publish-app-remote-cluster.md) on your local development cluster, all the preceding steps are handled automatically through a PowerShell script.  This script is found in the *Scripts* folder of the application project. This article provides background on what that script is doing so that you can perform the same operations outside of Visual Studio. 
 
## Connect to the cluster
Connect to the cluster by creating a [FabricClient](/dotnet/api/system.fabric.fabricclient) instance before you run any of the code examples in this article. For examples of connecting to a local development cluster or a remote cluster or cluster secured using Azure Active Directory, X509 certificates, or Windows Active Directory see [Connect to a secure cluster](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis).  To connect to the local development cluster, run the following:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## Upload the application package
Suppose you build and package an app named *MyApplication* in Visual Studio. By default, the application type name listed in the ApplicationManifest.xml is "MyApplicationType".  The application package, which contains the necessary application manifest, service manifests, and code/config/data packages, is located in *C:\Users\username\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*.

Uploading the application package puts it in a location that's accessible by internal Service Fabric components.
If you want to verify the application package locally, use the [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) cmdlet.

The CopyApplicationPackage method uploads the application package to the cluster image store. 

```csharp
// Copy the application package to a location in the image store
try
{
    fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
    Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
}
catch (AggregateException ae)
{
    Console.WriteLine("Application package copy to Image Store failed: ");
    foreach (Exception ex in ae.InnerExceptions)
    {
        Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
    }                
}
```

If the application package is large and/or has many files, you can [compress it](service-fabric-package-apps.md#compress-a-package) and copy it to the imagestore using PowerShell. The compression reduces the size and the number of files.

See [Understand the image store connection string](service-fabric-image-store-connection-string.md) for supplementary information about the image store and image store connection string.

## Register the application package
The application type and version declared in the application manifest become available for use when the app package is registered. The system reads the package uploaded in the previous step, verifies the package, processes the package contents, and copies the processed package to an internal system location.  

The [Register-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_) method registers the application type in the cluster and make it available for deployment:

```csharp
// Provision the application.            
try

    fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

    Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
}
catch (AggregateException ae)
{
    Console.WriteLine("Provision Application Type failed:");

    foreach (Exception ex in ae.InnerExceptions)
    {
        Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
    }
}
```

"MyApplicationV1" is the folder in the image store where the app package is located. The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) is now registered in the cluster.


The [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) command lists all successfully registered application type versions and their registration status. You can use
this command to determine when the registration is done.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## Create the application
You can instantiate an application from any application type version that has been registered successfully by using the [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication) cmdlet. The name of each application must start with the *fabric:* scheme and be unique for each application instance. Any default services defined in the application manifest of the target application type are also created.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Multiple application instances can be created for any given version of a registered application type. Each application instance runs in isolation, with its own work directory and process.

To see which named apps and services are running in the cluster, run the [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) and [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) cmdlets:

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## Remove an application
When an application instance is no longer needed, you can permanently remove it by name using the [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) cmdlet. [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) automatically removes all services that belong to the application as well, permanently removing all service state. This operation cannot be reversed, and application state cannot be recovered.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## Unregister an application type
When a particular version of an application type is no longer needed, you should unregister the application type using the [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) cmdlet. Unregistering unused application types releases storage space used by the image store. An application type can be unregistered as long as no applications are instantiated against it and no pending application upgrades are referencing it.

Run [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) to see the application types currently registered in the cluster:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Run [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) to unregister a specific application type:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## Remove an application package from the image store
When an application package is no longer needed, you can delete it from the image store to free up system resources.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

## Troubleshooting
### Copy-ServiceFabricApplicationPackage asks for an ImageStoreConnectionString
The Service Fabric SDK environment should already have the correct defaults set up. But if needed, the ImageStoreConnectionString for all commands should match the value that the Service Fabric cluster is using. You can find the ImageStoreConnectionString in the cluster manifest, retrieved using the [Get-ServiceFabricClusterManifest](/powershell/servicefabric/vlatest/get-servicefabricclustermanifest) commanC:

```powershell
PS C:\> Get-ServiceFabricClusterManifest
```

The ImageStoreConnectionString is found in the cluster manifest:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

See [Understand the image store connection string](service-fabric-image-store-connection-string.md) for supplementary information about the image store and image store connection string.

### Deploy large application package
Issue: [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) times out for a large application package (order of GB).
Try:
- Specify a larger timeout for [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) command, with `TimeoutSec` parameter. By default, the timeout is 30 minutes.
- Check the network connection between your source machine and cluster. If the connection is slow, consider using a machine with a better network connection.
If the client machine is in another region than the cluster, consider using a client machine in a closer or same region as the cluster.
- Check if you are hitting external throttling. For example, when the image store is configured to use azure storage, upload may be throttled.

Issue: Upload package completed successfully, but [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) times out.
Try:
- [Compress the package](service-fabric-package-apps.md#compress-a-package) before copying to the image store.
The compression reduces the size and the number of files, which in turn reduces the amount of traffic and work that Service Fabric must perform. The upload operation may be slower (especially if you include the compression time), but register and un-register the application type are faster.
- Specify a larger timeout for [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) with `TimeoutSec` parameter.
- Specify `Async` switch for [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype). The command returns when the cluster accepts the command and the provision continues async.
For this reason, there is no need to specify a higher timeout in this case.

### Deploy application package with many files
Issue: [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) times out for an application package with many files (order of thousands).
Try:
- [Compress the package](service-fabric-package-apps.md#compress-a-package) before copying to the image store. The compression reduces the number of files.
- Specify a larger timeout for [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) with `TimeoutSec` parameter.
- Specify `Async` switch for [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype). The command returns when the cluster accepts the command and the provision continues async.
For this reason, there is no need to specify a higher timeout in this case. 

## Next steps
[Service Fabric application upgrade](service-fabric-application-upgrade.md)

[Service Fabric health introduction](service-fabric-health-introduction.md)

[Diagnose and troubleshoot a Service Fabric service](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Model an application in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
