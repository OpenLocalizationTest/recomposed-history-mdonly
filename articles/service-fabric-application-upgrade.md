<properties
   pageTitle="Service Fabric Application Upgrade"
   description="This article provides an introduction to upgrading a Service Fabric application."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/15/2015"
   ms.author="subramar"/>


# Service Fabric Application Upgrade


A Service Fabric application is a collection of services. During an upgrade, Service Fabric compares the new application manifest (TBA link) with the previous version and determines which services in the application require updates. This is determined by comparing the version numbers in the service manifests to the previous version. If a service has not changed, that service is not upgraded.

In a rolling application upgrade, the upgrade is performed in stages. At each stage, the upgrade is applied to a subset of nodes in the cluster, called an upgrade domain. As a result, the application remains available throughout the upgrade. During the upgrade, the cluster may contain a mix of the old and new versions. For that reason, the two versions must be forward and backward compatible. If they are not compatible, the application administrator is responsible for staging a multiple-phase upgrade to maintain availability. This is done by doing an upgrade with an intermediate version of the application that is compatible with the previous version before upgrading to the final version.

Upgrade domains are specified in the cluster manifest when you configure the cluster. You should not assume that upgrade domains will receive updates in a particular order. For more information on upgrade domains, see <TBA>.

Non-rolling upgrades are possible, if the upgrade is applied to all nodes in the cluster, which is the case when the application has only one upgrade domain. This is not recommended since the service will be down and not available at the time of upgrade. Additionally, Azure doesn't provide any guarantees when a cluster is set up with only one upgrade domain.

For an upgrade, health policies have to be set (or default values may be used). An upgrade is termed successful when all upgrade domains are upgraded within the timeouts specified and all upgrade domains are deemed healthy.  A healthy upgrade domain means that the upgrade domain passes all the health checks specified in the health policy - for example, a health policy may mandate that all services within an application instance must be <em>healthy</em>, as health is defined by Service Fabric.

Health policies and checks during upgrade by Service Fabric are Service and Application agnostic. That is, no service specific tests are checked.  For example, your service have a minimal throughput requirement. However, Service Fabric does not have the information to test for that, and hence will not check throughput as defined for your application.   Please refer to the Health Section (TBA link) for the checks that are performed - the checks during upgrade include if the application package was copied correctly, and if the instance was started and so on.
The application health is an aggregation of the child entities of the application. In short, Service Fabric evaluates the health of the application through the health reported on the application as well as all the health of the services for the application. The health of the application services are further evaluated by aggregating the health of their children such as the service replica. Once the application health policy is satisfied, the upgrade can proceed or if the health policy is violated the application upgrade fails.

The common mode (and recommended) for upgrade is MonitoredAuto.  MonitoredAuto performs the upgrade on one upgrade domain, and if all health checks pass (per the policy specified), moves on to the next upgrade domain automatically, and so on.  If the health checks fail and/or timeouts reached, the upgrade is either rolled back for the upgrade domain, or the mode changed to UnmonitoredManual if that is the option selected at the time of upgrade.

UnmonitoredManual would need manual intervention after every upgrade on an upgrade domain to kick off the upgrade on the next upgrade domain. There are no Service Fabric health checks that are performed, and is reliant on the intervener to perform the health or status checks before starting the upgrade in the next upgrade domain.


## Next Steps


[Upgrade Tutorial](service-fabric-application-upgrade-tutorial.md)

[Upgrade Parameters](service-fabric-application-upgrade-parameters.md)

[Advanced Topics](service-fabric-application-upgrade-advanced.md)

[Troubleshooting Application Upgrade ](service-fabric-application-troubleshooting.md)

[Upgrade Flowchart](service-fabric-application-upgrade-flowchart.md)
