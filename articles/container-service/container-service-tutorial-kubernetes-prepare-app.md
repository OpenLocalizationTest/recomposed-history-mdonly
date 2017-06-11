---
title: Azure Container Service tutorial - Prepare App | Microsoft Docs
description: Azure Container Service tutorial - Prepare App 
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
ms.date: 06/09/2017
ms.author: nepeters
---

# Azure Container Service tutorial - Prepare App

Throughout the Azure Container Service tutorial set, a sample application will be deployed and managed in Kubernetes cluster. In this tutorial, a sample application will be prepared on your local system for use throughout the remaining tutorials. Steps completed are:

> [!div class="checklist"]
> * Clone an existing application code repository
> * Create container images from application
> * Test the application locally

## Prerequisites

To complete this tutorial, you will need a Docker development environment. Docker provides packages that easily configure a Docker development environment on any Mac or Windows system.

[Docker for Mac]( https://docs.docker.com/docker-for-mac/)

[Docker for Windows](https://docs.docker.com/docker-for-windows/) 

## Create container images

```bash
git clone https://github.com/neilpeterson/azure-kubernetes-samples.git
```

```bash
docker build ./azure-kubernetes-samples/flask-mysql-vote/azure-vote/ -t azure-vote-front
```

```bash
docker images
```

Output:

```bash
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front    latest              67e8582e68a8        About a minute ago   445 MB
ubuntu              latest              7b9b13f7b9c0        6 days ago           118 MB
```

## Test application locally

```bash
docker network create azure-vote
```

Note, in this example the MySQL database file is hosted inside of the container. In a subsequent tutorial, the database file will be moved to a mounted volume which will provide data integrity in the event of container restart / regeneration.

```bash
docker run -v /tmp/docker-mysql:/var/lib/mysql -p 3306:3306 -d --network azure-vote --name azure-vote-back -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote mysql:latest
```

```bash
docker run -p 8000:8000 -d --network=azure-vote --name azure-vote-front -e MYSQL_DATABASE_USER=dbuser -e MYSQL_DATABASE_PASSWORD=Password12 -e MYSQL_DATABASE_DB=azurevote -e MYSQL_DATABASE_HOST=azure-vote-back azure-vote-front
```

```bash
docker ps
```

Output:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
1ca1c3b27791        azure-vote-front    "/usr/bin/supervis..."   9 seconds ago       Up 7 seconds        0.0.0.0:8000->8000/tcp   azure-vote-front
478e4de611e0        mysql:latest        "docker-entrypoint..."   30 seconds ago      Up 28 seconds       0.0.0.0:3306->3306/tcp   azure-vote-back
```

Browse to `http://localhost:8000` to see the running application. 

Note, in this example the application is exposed on port 8000 which is default to the Python Gunicorn HTTP server. In subsequent tutorials, an ingress controller will be utilized to route incoming traffic and will also expose the application on port 80, or a different port of your choosing. 

![Image of Kubernetes cluster on Azure](media/container-service-kubernetes-tutorials/vote-app.png)

## Delete Resources

```bash
docker rm -f azure-vote-front
```

```bash
docker rm -f azure-vote-back
```

```bash
docker network rm azure-vote
```

## Next steps

A sample application was prepared and tested in a Docker development environment. Tasks covered included:

> [!div class="checklist"]
> * Clone an existing application code repository
> * Create container images from application
> * Test the application locally

Advance to the next tutorial to learn about storing container images in an Azure Container Registry.

> [!div class="nextstepaction"]
> [Push images to Azure Container Registry](./container-service-tutorial-kubernetes-prepare-acr.md)




