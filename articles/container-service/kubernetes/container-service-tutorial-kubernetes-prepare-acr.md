---
title: Azure Container Service tutorial - Prepare ACR | Microsoft Docs
description: Azure Container Service tutorial - Prepare ACR
services: container-service
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure

ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
---

# Deploy and use Azure Container Registry

Azure Container Registry (ACR) is an Azure-based, private registry, for Docker container images. This tutorial, part 2 of 7, walks through deploying an Azure Container Registry instance, and pushing a container image to it. Steps completed include:

> [!div class="checklist"]
> * Deploying an Azure Container Registry (ACR) instance
> * Tagging a container image for ACR
> * Uploading the image to ACR

In subsequent tutorials, this ACR instance is integrated with an Azure Container Service Kubernetes cluster, for securely running container images. 

## Before you begin

In the [previous tutorial](./container-service-tutorial-kubernetes-prepare-app.md), a container image was created for a simple Azure Voting application. In this tutorial, this image is pushed to an Azure Container Registry. If you have not created the Azure Voting app image, return to [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md). Alternatively, the steps detailed here work with any container image.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this tutorial requires that you are running the Azure CLI version 2.0.4 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## Deploy Azure Container Registry

When deploying an Azure Container Registry, you first need a resource group. An Azure resource group is a logical container into which Azure resources are deployed and managed.

Create a resource group with the [az group create](/cli/azure/group#create) command. In this example, a resource group named *myResourceGroup* is created in the *eastus* region.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Create an Azure Container registry with the [az acr create](/cli/azure/acr#create) command. The name of a Container Registry **must be unique**. Using the following example, update the name with some random characters.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic --admin-enabled true
```

## Get registry information 

Once the ACR instance has been created, the name, login server name, and authentication password are needed. The following code returns each of these values. Note each value down, they are referenced throughout this tutorial.  

ACR Name and Login Server:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrName:name,acrLoginServer:loginServer}" --output table
```

ACR Password - update with the ACR name.

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value -o tsv
```

## Container registry login

You must log in to your ACR instance before pushing images to it. Use the [docker login](https://docs.docker.com/engine/reference/commandline/login/) command to complete the operation. When running docker login, you need to provide th ACR login server name and ACR credentials.

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

The command returns a 'Login Succeeded’ message once completed.

## Tag container images

Each container image needs to be tagged with the loginServer name of the registry. This tag is used for routing when pushing container images to an image registry.

To see a list of current images, use the [docker images](https://docs.docker.com/engine/reference/commandline/images/) command.

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Tag the *azure-vote-front* image with the loginServer of the container registry. Also, add `:redis-v1` to the end of the image name. This tag indicates the image version.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

Once tagged, run [docker images] (https://docs.docker.com/engine/reference/commandline/images/) to verify the operation.

```bash
docker images
```

Output:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## Push images to registry

Push the *azure-vote-front* image to the registry. 

Using the following example, replace the ACR loginServer name with the loginServer from your environment.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

This takes a couple of minutes to complete.

## List images in registry

To return a list of images that have been pushed to your Azure Container registry, user the [az acr repository list](/cli/azure/acr/repository#list) command. Update the command with the ACR instance name.

```azurecli-interactive
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

Output:

```azurecli
Result
----------------
azure-vote-front
```

And then to see the tags for a specific image, use the [az acr repository show-tags](/cli/azure/acr/repository#show-tags) command.

```azurecli-interactive
az acr repository show-tags --name <acrName> --username <acrName> --password <acrPassword> --repository azure-vote-front --output table
```

Output:

```azurecli
Result
--------
redis-v1
```

At tutorial completion, the container image has been stored in a private Azure Container Registry instance. This image is deployed from ACR to a Kubernetes cluster in subsequent tutorials.

## Next steps

In this tutorial, an Azure Container Registry was prepared for use in an ACS Kubernetes cluster. The following steps were completed:

> [!div class="checklist"]
> * Deployed an Azure Container Registry instance
> * Tagged a container image for ACR
> * Uploaded the image to ACR

Advance to the next tutorial to learn about deploying a Kubernetes cluster in Azure.

> [!div class="nextstepaction"]
> [Deploy Kubernetes cluster](./container-service-tutorial-kubernetes-deploy-cluster.md)