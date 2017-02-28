---
title: Set up your eclipse development environment | Microsoft Docs
description: Set up your eclipse development environment.
services: service-fabric
documentationcenter: java
author: saysa
manager: raunakp
editor: ''

ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa

---

# Set up Eclipse development environment for Service Fabric Java application
Eclipse is one of the most used IDEs for Java Developers. In this article we discuss how you can set up your eclipse development environment to work with Service Fabric. Here we will show you - how you can install the plugin, create Service fabric applications and deploy your service-fabric application to local or remote Service Fabric cluster.

## Install Service Fabric Plugin Eclipse Neon
Service Fabric provides a plugin for the **Eclipse Neon IDE for Java Developers** that can simplify the process of building and deploying Java services.

1. In Eclipse, ensure that you have latest eclipse Neon and latest Buildship version (1.0.17 or later) installed. You can check the versions of installed components by choosing ``Help => Installation Details``. You can update Buildship using the instructions [here][buildship-update]. To check and update if your eclipse neon is on latest version, you can go to ``Help => Check for Updates``.
2. To install the Service Fabric plugin, choose ``Help => Install New Software...``.
  * In the "Work with" textbox, enter: http://dl.windowsazure.com/eclipse/servicefabric.
  * Click Add.

  ![Eclipse Neon plugin for Service Fabric][sf-eclipse-plugin-install]

  * Choose the Service Fabric plugin and click next.
  * Proceed through the installation and accept the end-user license agreement.
3. If you already have the Service Fabric eclipse plugin installed, make sure you are on the latest version. You can check if it can be updated any further be following - ``Help => Installation Details``. Then search for Service fabric in the list of installed plugin and click on update. If there is any pending update, it will be fetched and installed.

## Create Service Fabric application using Eclipse

1. Go to ``File => New => Other``. Select  ``Service Fabric Project``. Click ``Next``.

    ![Service Fabric New Project Page 1][create-application/p1]

2. Give a name to your project. Click ``Next``.

    ![Service Fabric New Project Page 2][create-application/p2]

3. Select Service Template from the available set of templates (Actor, Stateless, Container or Guest-executable). Click ``Next``.

    ![Service Fabric New Project Page 3][create-application/p3]

4. Enter the Service Name and/or the relevant Service details on this page and click ``Finish``.

    ![Service Fabric New Project Page 4][create-application/p4]

5. When you create your first Service fabric project, it will as if it would set the Service-fabric perspective, please select ``yes`` to continue.

    ![Service Fabric New Project Page 5][create-application/p5]

6. After successful creation the project looks like -

    ![Service Fabric New Project Page 6][create-application/p6]

## Build and deploy the Service Fabric Application using Eclipse

* Right click on the Service Fabric Application you just created above. It will show you a context-menu, there find ``Service Fabric`` and hover around there. It will popup a submenu on the right which would look like -

    ![Service Fabric Right Click Menu][publish/RightClick]

  Once you click on the options for build, rebuild, and clean, it will perform the intended actions.
  - ``Build Application`` will build the application without cleaning
  - ``Rebuild Application`` will do a clean-build of the application
  - ``Clean Application`` will clean the application of the built artefacts


* You can choose to deploy, undeploy and publish your application too from this menu.
  - ``Deploy Application`` will deploy to your local cluster
  - ``Publish Application..`` will ask for which publish-profile you want to select between ``Local.json`` and ``Cloud.json``

  ![Service Fabric Right Click Menu][publish/Publish]

* There is an alternate way in which you can deploy your Service Fabric application using eclipse Run-configurations.

  1. Choose ``Run => Run Configurations``. Select the ``ServiceFabricDeployer`` run-configuration.
  2. Under the ``Arguments`` tab, Specify **local** or **cloud** as the ``publishProfile``. The default setup is **local**. For deploying to a remote cluster, select **cloud**.
  3. Ensure the proper information is populated in the publish profiles, by editing the `Local.json` or `Cloud.json` as appropriate.
  4. Click **Run**.

Your app builds and deploys within a few moments. You can monitor its status from Service Fabric Explorer.  

## Add new Service Fabric Service to your Service Fabric Application

Once you have created a Service-fabric application using Eclipse, you can add new Service Fabric Service to the existing project. You can start by following the steps below -

1. Right click on the Project, you want to add a Service to open the context menu. Look for 'Service Fabric'. Once your mouse is there, a sub-menu appears.

    ![Service Fabric Add Service page 1][add-service/p1]

2. Select the 'Add ServiceFabric Service' Option and it will guide you through the next set of steps to add a service to the project.
3. Select the Service template you want to add to your project. And click 'Next'.

    ![Service Fabric Add Service page 2][add-service/p2]

4. Enter the Service Name (and other necessary details as and when required) and click "Add Service" button below.  

    ![Service Fabric Add Service page 3][add-service/p3]

5. After the Service gets added successfully, the entire project structure now looks like something a as below -

    ![Service Fabric Add Service page 4][add-service/p4]

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png
