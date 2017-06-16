---
title: Azure Container Service tutorial - Update Application | Microsoft Docs
description: Azure Container Service tutorial - Update Application
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
ms.date: 06/15/2017
ms.author: nepeters
---

# Update an application in Kubernetes

Tasks completed in this tutorial include:

> [!div class="checklist"]
> * <complete>

## Prerequisites

This tutorial is one part of a series. While you do not need to complete the full series to work through this tutorial, the following items are required.

**Azure Container Service Kubernetes cluster** – see, [Create a Kubernetes cluster]( container-service-tutorial-kubernetes-deploy-cluster.md) for information on creating the cluster.

**Azure Container Registry** – if you would like to integrate Azure Container registry (optional), an ACR instance is needed. See, [Deploy container registry]() for information on the deployment steps.

## Update application

To complete the steps in this tutorial, you must have cloned a copy of the Azure Vote application. If needed, do so with the following command:

```bash
git clone https://github.com/neilpeterson/azure-kubernetes-samples.git
```

Open up the file with any code or text editor.

```bash
code /azure-kubernetes-samples/azure-vote/azure-vote/config_file.cfg
```

The file will look like the following:

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Cats'
VOTE2VALUE = 'Dogs'
SHOWHOST = 'false'
```

Change the values for `VOTE1VALUE` and `VOTE2VALUES`, and save the file.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Half Full'
VOTE2VALUE = 'Half Empty'
SHOWHOST = 'false'
```

Run `docker-compose build` to re-create the container images.

```bash
docker-compose build
```

Run `docker-compose up -d` to run the container images.

```bash
docker-compose up -d
```

Browse to `http://localhost:8080` to see the updated application.

![Image of Kubernetes cluster on Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

```bash
docker tag azure-vote-front mycontainerregistry007.azurecr.io/azure-vote-front:v2
```

```bash
docker push mycontainerregistry007.azurecr.io/azure-vote-front:v2
```

## Deploy updated application

Ensure multiple instances of the front-end pod are running.

```bash
kubectl get pod
```

If needed, scale the front-end pod out so that multiple instances are running. This will ensure no down time as the updated application is deployed.

```bash
kubectl scale --replicas=4 deployment/azure-vote-front
```

Update the application.


```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2 --record
```

![Image of Kubernetes cluster on Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## Next steps

<complete> Tasks completed include:  

> [!div class="checklist"]
> * <complete>

Advance to the next tutorial to learn about monitoring Kubernetes with Operations Managemetn Suite.

> [!div class="nextstepaction"]
> [Monitor Kubernetes with OMS](./container-service-tutorial-kubernetes-scale.md)