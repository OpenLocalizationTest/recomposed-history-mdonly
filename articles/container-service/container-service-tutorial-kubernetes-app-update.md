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
> * Update application code
> * Create update container images
> * Push container images to ACR
> * Deploy updated application

## Prerequisites

This tutorial is one part of a series. While you do not need to complete the full series to work through this tutorial, the following items are required.

**Azure Container Service Kubernetes cluster** – see, [Create a Kubernetes cluster]( container-service-tutorial-kubernetes-deploy-cluster.md) for information on creating the cluster.

**Azure Container Registry** – if you would like to integrate Azure Container registry (optional), an ACR instance is needed. See, [Deploy container registry]() for information on the deployment steps.

## Update application

To complete the steps in this tutorial, you must have cloned a copy of the Azure Vote application. If needed, do so with the following command:

```bash
git clone https://github.com/neilpeterson/azure-kubernetes-samples.git
```

Change directories such that you are working from the root of the cloned repository.

```bash
cd azure-kubernetes-samples
```

Open the `config_file.cfg` file with any code or text editor.

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

Tag the new image with `v2` to indicate a new version. Replace `<acrLoginServer>` with your ACR login server name.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Push the image to your registry.


```bash
docker push mycontainerregistry007.azurecr.io/azure-vote-front:v2
```

## Deploy updated application

Ensure that you have multiple running instances of the azure-vote-front container.


```bash
kubectl get pod
```

If needed, scale the front-end pod out so that multiple instances are running.

```bash
kubectl scale --replicas=4 deployment/azure-vote-front
```

Make sure that they are running before updating the application.

```bash
kubectl get pod
```

If using Azure Container Registry to store container images, you need the ACR login server name. This name can be retrieved using the [az acr list](/cli/azure/acr#list) command.

```bash
az acr list --resource-group myResourceGroup --query [0].loginServer -o tsv
```

Run the following command to update the application. Update `<acrLoginServer>` with your ACR loging server name.

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2 --record
```

To monitor the deployment, run the following command. Exit the wait loop by pressing `control + c`.

```bash
kubectl get pod -w
```

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-wfgqt   1/1       Running   0         1m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
azure-vote-front-2870584546-htssh   1/1       Running   0         6s
azure-vote-front-2870584546-qj3tf   1/1       Running   0         6s
azure-vote-front-1297194256-tpjlg   1/1       Terminating   0         1m
azure-vote-front-1297194256-wfgqt   1/1       Terminating   0         1m
azure-vote-front-2870584546-p9ldj   0/1       Pending   0         0s
azure-vote-front-2870584546-nk8px   0/1       Pending   0         0s
```

Get the external IP address of the service.

```bash
kubectl get service
```

Browse to the IP address to see the updated application.

![Image of Kubernetes cluster on Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## Next steps

In this tutorial, an application was updated and this update rolled out to a Kubernetes cluster. Tasks completed include:  

> [!div class="checklist"]
> * Update application code
> * Create update container images
> * Push container images to ACR
> * Deploy updated application

Advance to the next tutorial to learn about monitoring Kubernetes with Operations Management Suite.

> [!div class="nextstepaction"]
> [Monitor Kubernetes with OMS](./container-service-tutorial-kubernetes-scale.md)