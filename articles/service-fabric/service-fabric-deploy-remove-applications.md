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
ms.date: 02/23/2017
ms.author: ryanwi

---
# Deploy and remove applications using PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

Once an [application type has been packaged][10], it's ready for deployment into an Azure Service Fabric cluster. Deployment involves the following three steps:

1. Upload the application package to the image store
2. Register the application type
3. Create the application instance

After an app is deployed and running in the cluster, you can also remove the app. App removal involves the following steps:

1. Remove (or delete) the running application
2. Unregister the application type if you no longer need it
3. Remove the application package from the image store

If you use [Visual Studio for deploying and debugging applications](service-fabric-publish-app-remote-cluster.md) on your local development cluster, all the preceding steps are handled automatically through a PowerShell script.  This script is found in the *Scripts* folder of the application project. This article provides background on what those scripts are doing so that you can perform the same operations outside of Visual Studio. 
 
## Connect to the cluster
Before you run any PowerShell commands in this article, always start by using [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster) to connect to the Service Fabric cluster. To connect to the local development cluster, run the following:

```powershell
Connect-ServiceFabricCluster
```

For examples of connecting to a remote cluster or cluster secured using Azure Active Directory, X509 certificates, or Windows Active Directory see [Connect to a secure cluster](service-fabric-connect-to-secure-cluster.md).

## Upload the application package
Uploading the application package puts it in a location that's accessible by internal Service Fabric components.  

Suppose you have a folder named *MyApplicationType* that contains the necessary application manifest, service manifests, and code/config/data packages. The [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) command uploads the package to the cluster Image Store. The **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, which is part of the Service Fabric SDK PowerShell module, is used to get the image store connection string.  To import the SDK module, run:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

You can copy an application package from *C:\users\username\Documents\Visual Studio 2015\Projects\MyApplication\myapplication\pkg\debug* to *c:\temp\MyApplicationType* (rename the "debug" directory to "MyApplicationType"). The following command lists the contents of the application package:

```powershell
PS C:\temp> tree /f .\MyApplicationType
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\TEMP\MYAPPLICATIONTYPE
³   ApplicationManifest.xml
³   
----Stateless1Pkg
    ³   ServiceManifest.xml
    ³   
    ----Code
    ³       Microsoft.ServiceFabric.Data.dll
    ³       Microsoft.ServiceFabric.Data.Interfaces.dll
    ³       Microsoft.ServiceFabric.Internal.dll
    ³       Microsoft.ServiceFabric.Internal.Strings.dll
    ³       Microsoft.ServiceFabric.Services.dll
    ³       ServiceFabricServiceModel.dll
    ³       Stateless1.exe
    ³       Stateless1.exe.config
    ³       Stateless1.pdb
    ³       System.Fabric.dll
    ³       System.Fabric.Strings.dll
    ³       
    ----Config
            Settings.xml
```

The following example uploads the package:

```powershell
PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded
```

See [Understand the image store connection string](service-fabric-image-store-connection-string.md) for supplementary information about the Image Store and ImageStoreConnectionString.

## Register the application package
The application type and version declared in the application manifest becomes available for use when the app package is registered. The system reads the package uploaded in the previous step, verifies the package, processes the package contents, and copies the processed package to an internal system location.  If you want to verify the app package locally, use the [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/servicefabric/test-servicefabricapplicationpackage) cmdlet.

Run the [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) cmdlet to register the application type in the cluster and make it available for deployment:

```powershell
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded
```

To see what application types have been registered, run the [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/servicefabric/get-servicefabricapplicationtype) cmdlet:
```powershell
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}
```

The [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) command returns only after the system has successfully copied the application package. How long registration takes depends on the size and contents of the application package. If needed, the **-TimeoutSec** parameter can be used to supply a longer timeout (the default timeout is 60 seconds).  If it is a large app package and you are experiencing timeouts, use the **-Async** parameter.

The [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) command lists all successfully registered application type versions.

## Create the application
You can instantiate an application by using any application type version that has been registered successfully through the [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication) command. The name of each application must start with the *fabric:* scheme and be unique for each application instance. Any default services defined in the application manifest of the target application type are also created.

```powershell
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
```

The [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) command lists all application instances that were successfully created, along with their overall status.

The [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) command lists all service instances that were successfully created within a given application instance. Default services (if any) are listed here.

Multiple application instances can be created for any given version of a registered application type. Each application instance runs in isolation, with its own work directory and process.

## Remove an application
When an application instance is no longer needed, you can permanently remove it by using the [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) command. This command automatically removes all services that belong to the application as well, permanently removing all service state. This operation cannot be reversed, and application state cannot be recovered.

```powershell
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
```

When a particular version of an application type is no longer needed, you should unregister it by using the [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) command. Unregistering unused types releases storage space used by the application package contents of that type on the image store. An application type can be unregistered as long as no applications are instantiated against it and no pending application upgrades are referencing it.

```powershell
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
```

## Troubleshooting
### Copy-ServiceFabricApplicationPackage asks for an ImageStoreConnectionString
The Service Fabric SDK environment should already have the correct defaults set up. But if needed, the ImageStoreConnectionString for all commands should match the value that the Service Fabric cluster is using. You can find the ImageStoreConnectionString in the cluster manifest, retrieved using the [Get-ServiceFabricClusterManifest](/powershell/servicefabric/vlatest/get-servicefabricclustermanifest) command:

```powershell
PS D:\temp> Get-ServiceFabricClusterManifest
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

## Next steps
[Service Fabric application upgrade](service-fabric-application-upgrade.md)

[Service Fabric health introduction](service-fabric-health-introduction.md)

[Diagnose and troubleshoot a Service Fabric service](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Model an application in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
