---
title: Azure Container Registry introduction | Microsoft Docs
description: Introduction to the Azure Container Registry service, providing cloud-based, managed, private Docker registries.
services: container-registry
documentationcenter: ''
author: stevelas
manager: balans
editor: dlepow
tags: ''
keywords: ''

ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: stevelas
---
# What is Azure Container Registry?
> [!NOTE]
> Container Registry is currently in preview.


Azure Container Registry is a managed Docker registry service to store and manage your private [Docker container](https://www.docker.com/what-docker) images. Container Registry gives you the ability to create and maintain private registry instances similar to the public [Docker Hub](http://hub.docker.com). Use your existing container development and deployment pipelines, or draw on the body of Docker community expertise, to work with container registries in Azure.

By creating a container registry in your subscription, you have full control over access and image names for all of your container deployments. Create a registry in the same Azure location as your deployments to take advantage of local, network-close storage of your container images. This can help reduce network latency in your container workflows as well as reduce charges for storage ingress and egress.

For background about Docker and containers, see:

* [Docker user guide](https://docs.docker.com/engine/userguide/)
* [Azure Container Registry preview announcement](https://azure.microsoft.com/blog/azure-container-registry-preview/) 

## Key concepts
* **Registry** - You can create one or more container registries in your Azure subscription. Each registry is backed by a standard Azure [storage account](https://azure.microsoft.com/en-us/documentation/articles/storage-introduction/) in the same location. 

  Configure your development workflows and deployment targets to access container images in your registries. Registries are created in a root domain based on the [Azure Active Directory tenant](active-directory-howto-tenant.md) for your subscription. For example, if you have an organizational account in the Contoso domain, your registry URL is of the form `myregistry.contoso.azurecr.io`.
* **Repository** - A registry consists of one or more repositories, which are collections of related images. Azure Container Registry supports multilevel repository namespaces. This enables you to group collections of images related to a specific app, or a collection of apps to specific development or operational groups. For example:
  
  * `myregistry.contoso.azurecr.io/aspnetcore:1.0.1` represents a corporate wide image
  * `myregistry.contoso.azurecr.io/warrantydept/dotnet-build` represents an image used to build dotnet apps, shared across the warranty department
  * `myregistry.contoso.azrecr.io/warrantydept/customersubmissions/web` represents a web image, grouped in the constomersubmissions app, owned by the warranty department
* **Image** - Stored in a repository, an image is a read-only snapshot of a Docker container. Azure container registries can manage both Windows and Linux images. Use standard [Docker commands](https://docs.docker.com/engine/reference/commandline/) to push images into a repository, or pull an image from a repository. 
* **Container** - A container defines a software application and its dependencies wrapped in a complete filesystem including code, runtime, system tools, and libraries. Run Docker containers based on Windows or Linux images that you pull from a container registry. Containers running on a single machines share the same operating system kernel. Docker containers are fully portable to all major Linux distros, Mac, and Windows.

## Deployment targets
Deploy containers from an Azure container registry to a variety of deployment targets:

* **Scalable orchestration systems** that manage containerized applications across clusters of hosts, including [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/), and [Kubernetes](http://kubernetes.io/docs/).
* **Azure services** that support building and running applications at scale, including [Container Service](https://azure.microsoft.com/en-us/documentation/services/container-service/), [App Service](https://azure.microsoft.com/en-us/documentation/services/app-service/), and [Batch](https://azure.microsoft.com/en-us/documentation/services/batch/). 

## Securing registry access
For private preview, securing a container registry is limited to basic authentication flows using usernames and passwords, managed with Azure Active Directory service principals. 

See [Authenticate with a container registry](container-registry-authentication.md) for more information.



## Next steps
* [Create a container registry using the Azure portal](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI](container-registry-get-started-docker-cli.md)
* [Push your first image using the Docker CLI](container-registry-get-started-docker-cli.md)

