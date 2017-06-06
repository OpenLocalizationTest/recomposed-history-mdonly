---
title: Using ACR with an Azure DC/OS cluster | Microsoft Docs
description: Use an Azure Container Registry with a DC/OS cluster in Azure Container Service
services: container-service
documentationcenter: ''
author: julienstroheker
manager: dcaro
editor: ''
tags: acs, azure-container-service, acr, azure-container-registry
keywords: Docker, Containers, Micro-services, Mesos, Azure, FileShare, cifs

ms.assetid:
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens

---
# Use ACR with a DC/OS cluster to deploy your application

In this article, we'll explore how to use a private container register such as ACR (Azure Container Registry) with a DC/OS cluster. Using ACR allows you to privatley store and manage container images. This tutorial will cover the following:

> [!div class="checklist"]
> * Deploy ACS cluster (if needed)
> * Deploy Azure Container Registry (if needed)
> * Configure ACR authentication on a DC/OS cluster
> * Uploaded an image to the Azure Container Registry
> * Run a container image from the Azure Container Registry

You will need an ACS DC/OS cluster to complete the steps in this tutorial. If needed, [this script sample](./scripts/container-service-cli-deploy-dcos.md) can create one for you.

This tutorial requires the Azure CLI version 2.0.4 or later. Run `az --version` to find the version. If you need to upgrade, see [Install Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Deploy Azure Container Registry

If needed, create an Azure Container registry with the [az acr create](/cli/azure/acr#create) command. The following example creates a registry with a randomly generate name *myContainerRegistry* and using the *Basic* sku. The registry is also configured with an admin account using the `--admin-enabled` argument.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic --admin-enabled true
```

Once the registry has been created, the Azure CLI will output data similar to the following. Take not of the `name` and `loginServer`, these are used in later steps.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Get the container registry credentials using the [az acr credential show](/cli/azure/acr/credential) command. Substitute the name with the one noted in the last step. Take note of the credentials, they are needed in a later step.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

For more information on Azure Container Registry, see [Introduction to private Docker container registries](../container-registry/container-registry-intro.md). 

## Manage ACR authentication

The conventional way to push and pull image from a private registry is to first authenticate with the registry. To do so, you use the `docker login` command any docker client that will access the private registry. Because a DC/OS cluster can be comprised of multiple nodes, it is helpful to automate this process across all of them. 

### Create shared storage

This process will use an Azure file share that has been mounted on each node in the cluster. If you have not already set up shared storage, see the [Setup a file share inside a DC/OS cluster](container-service-dcos-fileshare.md) 

### Configure ACR authentication

First, get the FQDN of the DC/OS master and store it in a variable.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Create an SSH connection with the master (or the first master) of your DC/OS-based cluster. Note, update the user name if a non-default value was used when creating the cluster.

```azurecli-interactive
ssh azureuser@$FQDN
```

Run the following command to log into the Azure Container Registry. Replace the name with the `loginServer` name, the `--username` with the name of the container registry, and the `--password` with one of the provided passwords. This command will store the authentication values locally under the `~/.docker` path.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Create a compressed file that containers the container registry authentication values.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Copy this file to the cluster shred storge. This will make the fiel avaliable on all nodes of the DC/OS cluster.

```azurecli-interactive
cp docker.tar.gz /mtn/share/dcos
```

## Upload image to ACR

Now from a development machine, or any other system with Docker installed. Create an image and upload it to the Azure Container Registry. This tutorial assumes that you have an existing image to upload.

First, login into the Azure Container Registry. In the following command, replace the name with the `loginServer` name, the `--username` with the name of the container registry, and the `--password` with one of the provided passwords.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Next, upload the image to the ACR registry. This example uploads an image named dcos-demo.

```azurecli-interactive
docker push dcos-demo
```

## Run an image from ACR

To use an image from the ACR registry, create a file names acrDemo.json and copy the following text into it. Replace the image name with the ACR `loginServer/imageName`. Also, replace the HAPROXY+ Take note of the `uris` property. This property holds the location of the container registry authentication file, which in this case if the Azure file share that is mounted on each node in the DC/OS cluster.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "demodcos.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/docker.tar.gz"
   ]
}
```

Deplpy the application with the DC/OC CLI.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## Next steps

In this tutorial you have configure DC/OS to use Azure Container Registry including the following tasks:

> [!div class="checklist"]
> * Deploy ACS cluster (if needed)
> * Deploy Azure Container Registry (if needed)
> * Configure ACR authentication on a DC/OS cluster
> * Uploaded an image to the Azure Container Registry
> * Run a container image from the Azure Container Registry

Advance to the next tutorial to learn how to monitor a DC/OS cluster with OMS.

> [!div class="nextstepaction"]
> [Create custom VM images](./container-service-monitoring-oms.md)
