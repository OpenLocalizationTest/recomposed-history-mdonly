---
title: Azure Container Service tutorial - Deploy Application | Microsoft Docs
description: Azure Container Service tutorial - Deploy Application
services: container-service
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure

ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
---

# Run applications in Kubernetes

In this tutorial, part 4 of 7, a sample application is deployed into a Kubernetes cluster. Steps completed include:

> [!div class="checklist"]
> * Download Kubernetes manifest files
> * Run application in Kubernetes
> * Test the application

In subsequent tutorials, this application is scaled out, updated, and Operations Management Suite configured to monitor the Kubernetes cluster.

This tutorial assumes a basic understanding of Kubernetes concepts, for detailed information on Kubernetes see the [Kubernetes documentation](https://kubernetes.io/docs/home/).

## Before you begin

In previous tutorials, an application was packaged into a container image, this image was uploaded to Azure Container Registry, and a Kubernetes cluster was created. If you have not done these steps, and would like to follow along, return to [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md). 

At minimum, this tutorial requires a Kubernetes cluster.

## Get manifest file

For this tutorial, [Kubernetes objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) are deployed using a Kubernetes manifest. A Kubernetes manifest is a YAML or JSON formatted file containing Kubernetes object deployment and configuration instructions.

The application manifest file for this tutorial is available in the Azure Vote application repo, which was cloned in a previous tutorial. If you have not already done so, clone the repo with the following command: 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

The manifest file is found in the following directory of the cloned repo.

```bash
/azure-voting-app-redis/kubernetes-manifests/
```

## Update manifest file

If using Azure Container Registry to store the container images, the manifest needs to be updated with the ACR loginServer name.

Get the ACR login server name with the [az acr list](/cli/azure/acr#list) command.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

The sample manifest has been pre-created with a repository name of *microsoft*. Open the file with any text editor, and replace the *microsoft* value with the login server name of your ACR instance.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

## Deploy application

Use the [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) command to run the application. This command parses the manifest file and create the defined Kubernetes objects.

```azurecli-interactive
kubectl create -f ./azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yaml
```

Output:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## Test application

A [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service/) is created which exposes the application to the internet. This process can take a few minutes. 

To monitor progress, use the [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) command with the `--watch` argument.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Initially, the **EXTERNAL-IP** for the *azure-vote-front* service appears as *pending*. Once the EXTERNAL-IP address has changed from *pending* to an *IP address*, use `CTRL-C` to stop the kubectl watch process.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

To see the application, browse to the external IP address.

![Image of Kubernetes cluster on Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## Next steps

In this tutorial, the Azure vote application was deployed to an Azure Container Service Kubernetes cluster. Tasks completed include:  

> [!div class="checklist"]
> * Download Kubernetes manifest files
> * Run the application in Kubernetes
> * Tested the application

Advance to the next tutorial to learn about scaling both a Kubernetes application and the underlying Kubernetes infrastructure. 

> [!div class="nextstepaction"]
> [Scale Kubernetes application and infrastructure](./container-service-tutorial-kubernetes-scale.md)