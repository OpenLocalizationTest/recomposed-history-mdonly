---
title: Create a Ruby App with Azure App Service Web App on Linux | Microsoft Docs
description: Learn to create a Ruby apps with Azure App Service Web App on Linux.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: erikre
editor: ''

ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: wesmc

---
# Create a Ruby App with Azure Web App on Linux - Preview

Azure App Service Web App on Linux is currently in public preview and supports running web apps natively on Linux.

## Overview

This tutorial will show you how to create a basic ruby on rails application locally and prepare/deploy it to Azure App Service on Linux.

## Prerequisites

* [Ruby 2.3.3 or higher](https://www.ruby-lang.org/documentation/installation/#rubyinstaller)  installed on your development machine.
* [Git](https://git-scm.com/downloads) is installed on your development machine
* An [active Azure subscription](https://azure.microsoft.com/pricing/free-trial/)
* This tutorial is written in the context of an Ubuntu environment. All system commands are bash specific.


## Create a new local rails application

1. Create a directory for the new app and change to that directory.

		mkdir ~/workspace
		cd ~/workspace

2. Initialize Ruby and check the version using the `ruby -v` command.

    ![Ruby init](./media/app-service-linux-ruby-get-started/ruby-version.png)

3. Install rails using the `gem install rails` command.

    ![Install rails](./media/app-service-linux-ruby-get-started/install-rails.png)

4. Create a new rails application called **hello-world** using the following command:

	If you are using Rails 5.1.0 or later, include the `--skip-yarn` option as following:

		rails new hello-world --skip-yarn

	For Rails prior to 5.1.0:

		rails new hello-world 

    ![New Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world.png)

    ![New Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world-2.png)

	If you are using Rails 5.1+, a package.json will be created if the `--skip-yarn` option is not used. We don't want it included with our deployment. As an alternative you can delete the package.json file or add it to the *.git-ignore* file in the directory as follows: 

		# Ignore package.json
		/package.json

5. Change to the *hello-world* directory, and start the server.

		cd hello-world
		rails server

    ![Start Hello-world](./media/app-service-linux-ruby-get-started/start-hello-world-server.png)
	
6. Using your web browser, navigate to `http://localhost:3000` to test the app locally.	

    ![Hello-world](./media/app-service-linux-ruby-get-started/hello-world.png)

## Prepare the app for Azure

By default, the ruby image runs the server with the `-e production` flag. This environment requires some setup for Azure Web App on Linux. The container takes care of some of that setup (such as setting a `SECRET_KEY_BASE`). You must prepare a root landing page as the default rails landing page, otherwise the website will fail to start.

To prepare a root landing page:

1. Open *~/workspace/hello-world/config/routes.rb* for editing. Add `root 'application#hello'` as shown below:

2. Open *~/workspace/hello-world/app/controllers/application_controller.rb* for editing. Add the following lines below as shown in the screenshot.

		def hello
			render html: "Hello, world from Azue Web App on Linux!"
		end

3. Your app is now configured. Using your web browser, navigate to `http://localhost:3000` to confirm.

	![Hello World configured](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

## Create a ruby website on Azure

1. Navigate to the [Azure portal](http://portal.azure.com) and login in with your subscription. Add a new *Web App on Linux* as shown below:

	![Create Web App on Linux](./media/app-service-linux-ruby-get-started/top-level-create.png)

2. Next, the **Create blade** opens as shown in the following image:

![The Create blade](./media/app-service-linux-ruby-get-started/create-blade.png)

	1. Give your web app a name.
	2. Choose an existing resource group or create a new one. (See available regions in the [limitations section](app-service-linux-intro.md).)
	3. Choose an existing Azure App Service plan or create a new one. (See App Service plan notes in the [limitations section](app-service-linux-intro.md).)
	4. Choose the application stack that you intend to use. You can choose between several versions of Node.js, PHP, .Net Core, and Ruby.

## Next steps
See the following links for more information regarding App Service on Linux. You can post questions and concerns on [our forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Creating Web Apps in App Service on Linux](app-service-linux-how-to-create-a-web-app.md)
* [How to use a custom Docker image for App Service on Linux](app-service-linux-using-custom-docker-image.md)
* [Using PM2 Configuration for Node.js in Web Apps on Linux](app-service-linux-using-nodejs-pm2.md)
* [Using .NET Core in Azure App Service Web Apps on Linux](app-service-linux-using-dotnetcore.md)
* [Using Ruby in Azure App Service Web Apps on Linux](app-service-linux-using-ruby.md)
* [Azure App Service Web Apps on Linux FAQ](app-service-linux-faq.md)

