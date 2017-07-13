---
title: Continuous Deployment with Azure Web App on Linux | Microsoft Docs
description: How to setup continuous deployment in Azure Web App on Linux.
keywords: azure app service, linux, oss, acr
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: erikre
editor: ''

ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc

---
# Continuous Deployment with Azure Web App on Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

In this tutorial, you configure continuous deployment for a custom container image from Managed [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/) repositories or [Docker Hub](https://hub.docker.com).

## Step 1 - Log in to Azure

Log in to the Azure portal at http://portal.azure.com

## Step 2 - Enable Docker Hub continuous deployment

In the **[Azure portal](https://portal.azure.com/)**, click the **App Service** option on the left of the page.

Click on the name of your app that you want to configure Docker Hub continuous deployment for.

In the **App settings**, add an app setting called `DOCKER_ENABLE_CI` with the value `true`.

![insert image of app setting](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## Step 3 - Add a web hook

### Azure Container Registry

In your registry portal blade, click **Webhooks**, create a new webhook by clicking **Add**. In the **Create webhook** blade, give your webhook a name. For the Webhook URI, you need to provide the following endpoint:
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Make sure that you define the scope as the repo that contains your container image.

![insert image of webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Click **Create** to save configuration.

When the image gets updated, the web app get updated automatically with the new image.

### Docker Hub

In your Docker Hub page, click **Webhooks**, then **CREATE A WEBHOOK**.

![insert image of adding webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

For the Webhook URL, you need to have the following endpoint:
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

![insert image of adding webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

You can obtain your `publishingusername` and `publishingpwd` by downloading the web app publish profile using the Azure portal.

![insert image of adding webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

When the image gets updated, the web app get updated automatically with the new image.

## Next steps
* [What is Azure Web App on Linux?](./app-service-linux-intro.md)
* [Creating Apps in Azure Web App on Linux](./app-service-linux-how-to-create-web-app.md)
* [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)
* [Using PM2 Configuration for Node.js in Azure Web App on Linux](app-service-linux-using-nodejs-pm2.md)
* [Using .NET Core in Azure Web App on Linux](app-service-linux-using-dotnetcore.md)
* [Using Ruby in Azure Web App on Linux](app-service-linux-ruby-get-started.md)
* [How to use a custom Docker image for Azure Web App on Linux](./app-service-linux-using-custom-docker-image.md)
* [Azure App Service Web App on Linux FAQ](./app-service-linux-faq.md) 



