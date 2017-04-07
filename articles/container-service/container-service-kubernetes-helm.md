---
title: Deploy containers with Helm in Azure Kubernetes | Microsoft Docs
description: Use the Help packaging tool to deploy containers on a Kubernetes cluster in Azure Container Service 
services: container-service
documentationcenter: ''
author: sauryadas
manager: madhana
editor: ''
tags: acs, azure-container-service
keywords: ''

ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: saudas
ms.custom: 

---
# Use Helm to deploy containers on a Kubernetes cluster 

[Helm](https://github.com/kubernetes/helm/) is an open-source packaging tool that helps you install and manage the lifecycle of Kubernetes applications. Similar to Linux package managers such as Apt-get and Yum, Helm is used to manage Kubernetes charts, which are packages of preconfigured Kubernetes resources. This article shows you how to work with Helm on a Kubernetes cluster deployed in Azure Container Service.

Helm has two components: 
* The **Helm CLI** is a client that runs on your machine locally or in the cloud  

* **Tiller** is a server that runs on the Kubernetes cluster and manages the lifecycle of your Kubernetes applications 
 
## Prerequisites

* [Create a Kubernetes cluster](container-service-kubernetes-walkthrough.md) in Azure Container Service

* [Install and configure `kubectl`](container-service-connect.md) on a local computer

* [Install Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) on a local computer

## Helm basics 

To view information about the Kubernetes cluster that you will be installing Tiller and deploying your applications to, type the following command:

```bash
kubectl cluster-info 
```
![kubectl cluster-info](media/container-service-kubernetes-helm/clusterinfo.png)
 
After you have installed Helm, install Tiller on your Kubernetes cluster by typing the following command:

```bash
helm init
```
When it completes successfully, you see output like the following:

![Tiller installation](media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
To view all the Helm charts available in the repository, type the following command:

```bash 
helm search 
```

You see output like the following:

![Helm search](media/container-service-kubernetes-helm/helm-search.png)
 
To update the charts to get the latest versions, type:

```bash 
helm repo update 
```
## Deploying an nginx-ingress chart 
 
To deploy an Nginx ingress controller chart, type a single command:

```bash
helm install stable/nginx-ingress 
```


if you type kubectl get svc to view all services that are running on the cluster, you will see that an IP address is being assigned to (you will see pending while it is in process; take about a couple of mins) the ingress-controller. If you navigate the external-ip you should have the nginx backend running. 
 
 
To see a list of all the charts installed on your cluster type 
$ helm list 
or 
$helm ls 
 
 
 
 
 
Deploying a simple mariadb chart and installing a client to connect to the DB Now let uss deploy a mariadb chart and deploy a mariadb client to connect to the database 
To deploy the mariadb chart, type 
$ helm install - - name v1 stable/mariadb 
where - - name is a tag used for releases 
Tip: if the deployment fails, try $ helm repo update and try again 
 
 
To view all the charts deployed on your cluster, type 
$helm list 
 
 
 
 
 
 
To view all deployments running on your cluster, type 
$ kubectl get deployemnts 
 
 
 
Finally, to run a pod to access the client, type 
$ kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
 
 
 
To connect to the client, type 
# mysql –h v1-mariadb [you should replace v1-mariadb with the name of your deployment] 
 
 
You can now use standard sql commands to create databases, tables, etc. 
For example, Create DATABASE testdb1 creates an empty database. 
 
 
 
You can learn more about helm charts in detail here 
## Next steps

* See the [Helm documentation](https://github.com/kubernetes/helm/blob/master/docs/index.md) for more about 


Now that you have a functioning cluster, see these documents for connection and management details:

* [Connect to an Azure Container Service cluster](container-service-connect.md)
* [Work with Azure Container Service and DC/OS](container-service-mesos-marathon-rest.md)
* [Work with Azure Container Service and Docker Swarm](container-service-docker-swarm.md)
* [Work with Azure Container Service and Kubernetes](container-service-kubernetes-walkthrough.md)