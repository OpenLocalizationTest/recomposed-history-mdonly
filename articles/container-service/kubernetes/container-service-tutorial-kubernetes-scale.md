---
title: Azure Container Service tutorial - Scale Application | Microsoft Docs
description: Azure Container Service tutorial - Scale Application
services: container-service
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, Azure

ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: danlep
---

# Scale Kubernetes pods and Kubernetes infrastructure

If you've been following the tutorials, you have a working Kubernetes cluster in Azure Container Service and you deployed the Azure Voting app. 

In this tutorial, you scale out the pods in the app and try pod autoscaling. You also learn how to scale the number of agent nodes to change the cluster's capacity for hosting workloads. Tasks completed include:

> [!div class="checklist"]
> * Manually scaling Kubernetes pods
> * Configuring Autoscale pods running the app front end
> * Scale the Kubernetes Azure agent nodes

## Before you begin

In previous tutorials, an application was packaged into container images, these images uploaded to Azure Container Registry, and a Kubernetes cluster created. The application was then run on the Kubernetes cluster. If you have not done these steps, and would like to follow along, return to the [Tutorial 1 – Create container images](container-service-tutorial-kubernetes-prepare-app.md). 

At minimum, this tutorial requires a Kubernetes cluster with a running application.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this tutorial requires that you are running the Azure CLI version 2.0.4 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## Manually scale pods

The previous tutorial deployed the Azure Vote front-end and back-end each in a single pod. To verify, run the [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) command.

```bash
kubectl get pods
```

Output is similar to the following:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Manually change the number of pods in the `azure-vote-front` deployment using the [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale) command. This example increases the number to 5:

```bash
kubectl scale --replicas=5 deployment/azure-vote-front
```

Run [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) to verify that Kubernetes is creating the pods. After a minute or so, the additional pods are running:

```bash
kubectl get pods
```

Output:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## Autoscale pods

Kubernetes supports [horizontal pod autoscaling](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) to adjust the number of pods in a deployment depending on CPU utilization or other metrics. 

To use the autoscaler, your pods must have CPU requests and limits defined. In the `azure-vote-front` deployment, each container requests 0.25 CPU, with a limit of 0.5 CPU. The settings look like:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

The following example uses the [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) command to autoscale the number of pods in the `azure-vote-front` deployment. Here, if CPU utilization exceeds 50%, the autoscaler increases the pods to a maximum of 10.


```bash
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

To see the status of the autoscaler, run the following command:

```bash
kubectl get hpa
```

Output:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

After a few minutes with minimal load on the Azure Vote app, the number of pod replicas decreases automatically to 3.

## Scale the agents

If you created your Kubernetes cluster using default commands in the previous tutorial, it has three agent nodes. You can adjust the number of agents manually if you plan more or fewer container workloads on your cluster. Use the [az acs scale](/cli/azure/acs#scale) command, and specify the number of agents with the `--new-agent-count` parameter.

The following example increases the number of agent nodes to 4 in the Kubernetes cluster named *myK8sCluster*. The command takes a couple of minutes to complete.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

The command output shows the number of agent nodes in the value of `agentPoolProfiles:count`:

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## Next steps

In this tutorial, you used different scaling features in your Kubernetes cluster. Tasks covered included:

> [!div class="checklist"]
> * Manually scaling Kubernetes pods
> * Configuring Autoscale pods running the app front end
> * Scale the Kubernetes Azure agent nodes

Advance to the next tutorial to learn about updating application in Kubernetes.

> [!div class="nextstepaction"]
> [Update an application in Kubernetes](container-service-tutorial-kubernetes-app-update.md)

