---
title: Quickstart - Azure Kubernetes cluster for Windows | Microsoft Docs
description: Quickly learn to create a Kubernetes cluster for Windows containers in Azure Container Service with the Azure CLI.
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: acs, azure-container-service, kubernetes
keywords: ''

ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017

---

# Create a Kubernetes cluster for Windows containers with the Azure CLI

The Azure CLI is used to create and manage Azure resources from the command line or in scripts. This guide details using the Azure CLI to deploy a [Kubernetes](https://kubernetes.io/docs/home/) cluster in [Azure Container Service](container-service-intro.md). Once the cluster is deployed, you connect to it with the Kubernetes `kubectl` command-line tool, and you deploy your first Windows container.

To complete this quick start, make sure you have installed the latest [Azure CLI 2.0](/cli/azure/install-azure-cli). You can also use [Cloud Shell](/azure/cloud-shell/quickstart) from your browser.

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

> [!NOTE]
> Support for Windows containers on Kubernetes in Azure Container Service is in preview. 
>



## Log in to Azure 

Log in to your Azure subscription with the [az login](/cli/azure/#login) command and follow the on-screen directions.

```azurecli
az login
```

## Create a resource group

Create a resource group with the [az group create](/cli/azure/group#create) command. An Azure resource group is a logical group in which Azure resources are deployed and managed. 

The following example creates a resource group named *myResourceGroup* in the *eastus* location.

```azurecli
az group create --name myResourceGroup --location eastus
```

## Create Kubernetes cluster
Create a Kubernetes cluster in Azure Container Service with the [az acs create](/cli/azure/acs#create) command. 

The following example creates a cluster named *myK8sCluster* with one Linux master node and two Windows agent nodes. This example creates SSH keys and a [service principal](container-service-kubernetes-service-principal.md) if they don't already exist in the default locations. This example uses *azureuser* for an administrative user name and *myPassword12* as the password on the Windows nodes. Update these values to something appropriate to your environment. 



```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

After several minutes, the command completes, and shows you information about your deployment.

## Install kubectl

To connect to the Kubernetes cluster from your client computer, use [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), the Kubernetes command-line client. 

If you're using CloudShell, `kubectl` is already installed. If you want to install it locally, you can use the [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) command.

The following Azure CLI example installs `kubectl` to a full path you specify with the `--install-location` option. If you are running the Azure CLI on macOS or Linux, you might need to run the command with `sudo`.

```azurecli
sudo az acs kubernetes install-cli --install-location full-path-to-kubectl 
```

After `kubectl` is installed, add it to your system path. 

## Configure kubectl and connect

To configure `kubectl` to connect to your Kubernetes cluster, run the [az acs kubernetes get-credentials](cli/azure/acs/kubernetes#get-credentials) command. The following example
downloads the cluster configuration for your Kubernetes cluster.

```azurecli
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Now you are ready to connect to your cluster from your machine. Try running:

```bash
kubectl get nodes
```

`kubectl` shows output similar to the following.

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```



## Deploy your first Windows container

After creating the cluster and connecting with `kubectl`, try starting a containerized Windows app in a Kubernetes pod on the cluster. (A pod can contain one or more containers.) This basic example uses a JSON file to specify a Microsoft Internet Information Server (IIS) container, and then creates the pod using `kubctl apply`. 

Create a local file named `iis.json` and copy the following text. This file tells Kubernetes to run IIS on Windows Server 2016 Server Core, using a public container image from [Docker Hub](https://hub.docker.com/r/microsoft/iis/). The container uses port 80, but initially is only accessible within the cluster network.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

To start the pod, type:
  
```bash
kubectl apply -f iis.json
```  

To track the deployment, type:
  
```bash
kubectl get pods
```

While the pod is deploying, the status is `ContainerCreating`. Because of the size of the IIS image, it can take several minutes for the container to enter the `Running` state.

```output
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## View the IIS welcome page

To expose the pod to the world with a public IP address, type the following command:

```bash
kubectl expose pods iis --port=80 --type=LoadBalancer
```

With this command, Kubernetes creates an [Azure load balancer rule](container-service-kubernetes-load-balancing.md) with a public IP address. 

Run the following command to see the status of the service.

```bash
kubectl get svc
```

Initially the IP address appears as `pending`. After a few minutes, the external IP address of the `iis` pod is set:
  
```output
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       kubernetes   10.0.0.1       <none>          443/TCP        21h       iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

You can use a web browser of your choice to see the default IIS welcome page at the external IP address:

![Image of browsing to IIS](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## Delete cluster
When the cluster is no longer needed, you can use the [az group delete](/cli/azure/group#delete) command to remove the resource group, container service, and all related resources.

```azurecli
az group delete --name myResourceGroup
```


## Next steps

In this quickstart, you deployed a Kubernetes cluster, connected with `kubectl`, and deployed a pod with an IIS container. To learn more about Azure Container Service, continue to [TODO: Link to tutorial?]
