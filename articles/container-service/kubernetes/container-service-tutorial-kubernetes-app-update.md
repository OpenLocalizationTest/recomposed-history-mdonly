---
title: Azure Container Service tutorial - Update application | Microsoft Docs
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
ms.date: 07/26/2017
ms.author: nepeters
---

# Update an application in Kubernetes

After you deploy an application in Kubernetes, it can be updated by specifying a new container image or image version. When you update an application, the update rollout is staged so that only a portion of the deployment is concurrently updated. This staged update enables the application to keep running during the update, and provides a rollback mechanism if a deployment failure occurs. 

In this tutorial, part six of seven, the sample Azure Vote app is updated. Tasks that you complete include:

> [!div class="checklist"]
> * Updating the front-end application code
> * Creating an updated container image
> * Pushing the container image to Azure Container Registry
> * Deploying the updated container image

In subsequent tutorials, Operations Management Suite is configured to monitor the Kubernetes cluster.

## Before you begin

In previous tutorials, an application was packaged into a container image, the image uploaded to Azure Container Registry, and a Kubernetes cluster created. The application was then run on the Kubernetes cluster. 

If you haven't completed these steps, and want to follow along, return to [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md). 

## Update application

To complete the steps in this tutorial, you must have cloned a copy of the Azure Vote application. If necessary, create this cloned copy with the following command:

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Open the `config_file.cfg` file with any code or text editor. You can find this file under the following directory of the cloned repo.

```bash
 /azure-voting-app-redis/azure-vote/azure-vote/config_file.cfg
```

Change the values for `VOTE1VALUE` and `VOTE2VALUE`, and then save the file.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Use [docker-compose](https://docs.docker.com/compose/) to re-create the front-end image and run the updated application.

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yaml up --build -d
```

## Test application locally

Browse to `http://localhost:8080` to see the updated application.

![Image of Kubernetes cluster on Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## Tag and push images

Tag the *azure-vote-front* image with the loginServer of the container registry.

If you're using Azure Container Registry, get the login server name with the [az acr list](/cli/azure/acr#list) command.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Use [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) to tag the image. Replace `<acrLoginServer>` with your Azure Container Registry login server name or public registry hostname.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Use [docker push](https://docs.docker.com/engine/reference/commandline/push/) to upload the image to your registry. Replace `<acrLoginServer>` with your Azure Container Registry login server name or public registry hostname.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## Deploy update application

To ensure maximum uptime, multiple instances of the application pod must be running. Verify this configuration with the [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) command.

```bash
kubectl get pod
```

Output:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

If you don't have multiple pods running the azure-vote-front image, scale the *azure-vote-front* deployment.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

To update the application, use the [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set) command. Update `<acrLoginServer>` with the login server or host name of your container registry.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

To monitor the deployment, use the [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) command. As the updated application is deployed, your pods are terminated and re-created with the new container image.

```azurecli-interactive
kubectl get pod
```

Output:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## Test updated application

Get the external IP address of the *azure-vote-front* service.

```azurecli-interactive
kubectl get service azure-vote-front
```

Browse to the IP address to see the updated application.

![Image of Kubernetes cluster on Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## Next steps

In this tutorial, you updated an application and rolled out this update to a Kubernetes cluster. The following tasks were completed:

> [!div class="checklist"]
> * Updated the front-end application code
> * Created an updated container image
> * Pushed the container image to Azure Container Registry
> * Deployed the updated application

Advance to the next tutorial to learn about how to monitor Kubernetes with Operations Management Suite.

> [!div class="nextstepaction"]
> [Monitor Kubernetes with OMS](./container-service-tutorial-kubernetes-monitor.md)