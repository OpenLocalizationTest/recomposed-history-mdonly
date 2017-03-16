---
title: Create a scalable Node.js+MongoDB app in Azure | Microsoft Docs 
description: Learn how to create a scalable Node.js+MongoDB app in Azure. This tutorial shows you how to deploy an Express.js web app.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''

ms.assetid: 8877ddc8-1476-45ae-9e7f-3c75917b4564
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/16/2017
ms.author: cephalin

---
# Create a scalable Node.js+MongoDB app in Azure
This tutorial shows you how to deploy an Express.js app to Azure App Service. In the process, you can glean some general knowledge on how to configure your Express.js app to run in App Service.

You should have working knowledge of Node.js. This tutorial is not intended to help you with issues related to developing Node.js apps in general.

## Prerequisites
* [Node.js](https://nodejs.org/)
* [Git](http://www.git-scm.com/downloads)
* [Azure CLI 2.0](/cli/azure/install-az-cli2)

## Step 0: Create a local Node.js app

1. Open the command-line terminal of your choice and `CD` to a working directory.

2. Install the [Express.js application generator](https://expressjs.com/starter/generator.html).

        npm install express-generator -g

3. In your working directory, create an Express.js app with the generator and run it:

        express expressAzureTutorial --git
        cd expressAzureTutorial
        npm install
        npm start

    Make sure you can navigate to the default home page at http://localhost:3000, then type `Ctrl-C` (or `⌘-C` in MacOS) to stop the app.

4. Initialize a Git repository and commit your files. In the application root (where package.json is), run the following Git commands:

        git init
        git add .
        git commit -m "first commit in this Azure tutorial!"

<a name="step1"></a>
## Step 1: Deploy to Azure App Service

1. Log in to Azure like so:

        az login

    Follow the prompt to continue the login in a browser with a Microsoft account that has your Azure subscription.

3. Create a [resource group](../azure-resource-manager/resource-group-overview.md) with a name. 

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    To see what possible values you can use for `--location`, use the `az appservice list-locations` CLI command.

3. Create a "FREE" [App Service plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) with a name. 

    ```azurecli
    az appservice plan create --name my-expressjs-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

    This plan is created in West Europe in the same region as the resource group, since `--location` is not specified.

4. Create a new web app with a unique name in `<app_name>`.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-expressjs-appservice-plan
    ```

3. Set the deployment user for App Service. You will deploy code using these credentials later.
   
    ```azurecli
    az appservice web deployment user set --user-name <username> --password <minimu-8-chars-letters-and-numbers>
    ```

1. Configure local Git deployment for your new web app with the following command:

    ```azurecli
    az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup
    ```

    You get a JSON output like this, which means that the remote Git repository is configured:

    ```json
    {
    "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
    }
    ```

6. Add the URL in the JSON as a Git remote for your local repository (called `azure` for simplicity).

    ```
    git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
    ```
   
7. Deploy your sample code to the `azure` Git remote. When prompted, use the deployment credentials you configured earlier.

    ```
    git push azure master
    ```

7. Launch your live Azure app in the browser:

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

## Step 2: Stream diagnostic logs

To stream logs from your app in Azure directly to your terminal, do the following:

2. Start log streaming.

    ```azurecli
    az appservice web log tail --name <app_name> --resource-group myResourceGroup
    ```

2. Refresh your web app in the browser to get some web traffic.

3. To stop log streaming at anytime, type `Ctrl-C` (or `⌘-C` in MacOS).

## Step 3: Connect web app to MongoDB
In this section, you connect your Express.js app to a MongoDB instance. For MongoDB, you will use [Azure DocumentDB](../documentdb/documentdb-protocol-mongodb.md) database, which can support MongoDB client connections.

1. Create a DocumentDB account with MongoDB support, and retrieve the connection password.

    ```azurecli
    accountname="<documentdb_account_name>"
    az documentdb create --name $accountname --resource-group myResourceGroup --kind MongoDB
    password=$(az documentdb list-keys --name $accountname --resource-group myResourceGroup --query primaryMasterKey --output tsv)
    ```

    For PowerShell use the following instead

    ```ps
    $accountname="<documentdb_account_name>"
    az documentdb create --name $accountname --resource-group myResourceGroup --kind MongoDB
    $password=$(az documentdb list-keys --name $accountname --resource-group myResourceGroup --query primaryMasterKey --output tsv)
    ```

    Putting your settings in Azure app settings keeps sensitive data out of your source control (Git). Next, you will
    configure your development environment to use the same connection information.

2. Configure the MongoDB-formatted connection string as an application setting for your web app.

    ```azurecli
    az appservice web config appsettings update --settings dbconnstring="mongodb://$accountname:$password@$accountname.documents.azure.com:10250/tutorial?ssl=true&sslverifycertificate=false" --name <app_name> --resource-group myResourceGroup    
    ```

    > [!NOTE] 
    > The `ssl=true` option is important because [Azure DocumentDB requires it](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
    >
    >

3. In your command-line terminal, from your Express.js app's root directory, install the [MongoDB NPM package](https://www.npmjs.com/package/mongodb).

        npm install mongodb --save

4. Open `routes/index.js` and replace the content with the following code. This code connects to the MongoDB URL, inserts a document, and outputs all the documents as JSON to the view. 

    ```javascript
    var express = require('express');
    var router = express.Router();
    var MongoClient = require('mongodb').MongoClient;
    var output = null;

    MongoClient.connect(process.env.dbconnstring, function(err, db) {
      var collection = db.collection('documents')
      collection.insert({value: Math.random()}, function(err, result) {
        collection.find({}).toArray(function(err, docs) {
          output = JSON.stringify(docs);
          db.close()
        })
      })
    })

    /* GET home page. */
    router.get('/', function(req, res, next) {
      res.render('index', { title: 'Express', data: output });
    });

    module.exports = router;
    ```

    This code opens and closes a MongoDB connection at every request to `/index`, which is not very performant. However, it does demonstrate to you how you can use application settings in App Service to safeguard sensitive connection information.

5. Open `views/index.jade` and add the following code to the end to make the `index` view display the data from the MongoDB connection.

    ```
    p Database documents: #{data}
    ```

6. Push your changes to Azure.

    ```
    git add .
    git commit -m "added MongoDB access code."
    git push azure master
    ```

7. Launch your live Azure app in the browser:

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

## Step 4: Monitor with web server logs
In this section, you learn how to quickly turn on monitoring with server logs then download these logs. 

1. Enable all logging options for your web app.

    ```azurecli
    az appservice web log config --name <app_name> --resource-group myResourceGroup --application-logging true --detailed-error-messages true --failed-request-tracing true --web-server-logging filesystem
    ```

2. Navigate to a nonexistent page at your web app to generate some error entries. For example: `http://<app_name>.azurewebsites.net/404` 

3. Download the log files for review.

    ```azurecli
    az appservice web log download --name <app_name> --resource-group myResourceGroup
    ```

## Step 5: Scale web app to another region

1. Create a Traffic Manager propfile with a unique name and add it to your resource group.

    ```azurecli
    az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name <unique-dns-name>
    ```

2. Get the resource ID of your existing Node.js web app.

    ```azurecli
    az appservice web show --name <app_name> --resource-group myResourceGroup --query id --output tsv
    ```

3. Add an endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-1-resource-id>`:

    ```azurecli
    az network traffic-manager endpoint create --name <app_name>-westeurope --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id <web-app-1-resource-id>
    ```

4. Your Traffic Manager profile now has an endpoint that points to your web app. Query for its URL to try it out.

    az network traffic-manager profile show --name cephalin-express --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv

    Copy the output into your browser. You should get the default Express page again, with data from your database.

5. Let's add some identifying characteristic to your West Europe app. Add an environment variable.

    ```azurecli
    az appservice web config appsettings update --settings region="Europe" --name <app_name> --resource-group myResourceGroup    
    ```

6. Open `routes/index.js` and change the `router.get()` to use the environment variable.

    ```javascript
    router.get('/', function(req, res, next) {
      res.render('index', { title: 'Express ' + process.env.region, data: output });
    });
    ```

7. Save your changes and push them to Azure.

    ```
    git add .
    git commit -m "added region string."
    git push azure master
    ```

8. Refresh your browser on your Traffic Manager profile's URL. You should now see `Express Europe` in the homepage.

    Next, you create a new web app in Southeast Asia and add it to the profile.

4. Create an App Service plan and web app in the Southeast Asia region, and deploy the same code to it just like you did in [Step 1]<#step1>.

    ```azurecli
    az appservice plan create --name my-expressjs-appservice-plan-asia --resource-group myResourceGroup --location "Southeast Asia" --sku FREE
    az appservice web create --name <app_name>-asia --plan my-expressjs-appservice-plan-asia --resource-group myResourceGroup
    // In PowerShell, add $ to the beginning of the next command
    url=$(az appservice web source-control config-local-git --name <app_name>-asia --resource-group myResourceGroup --query url --output tsv)
    git remote add azureasia $url
    git push azureasia master
    ```

5. Add the same application settings to the new web app. Set the region to `"Asia"`.

    ```azurecli
    az appservice web config appsettings update --settings dbconnstring="mongodb://$accountname:$password@$accountname.documents.azure.com:10250/tutorial?ssl=true&sslverifycertificate=false" --name <app_name>-asia --resource-group myResourceGroup    
    az appservice web config appsettings update --settings region="Asia" --name <app_name>-asia --resource-group myResourceGroup    
    ```

    Since DocumentDB is a [geographically distributed](../documentdb/documentdb-distribute-data-globally.md) NoSQL service, you can use the same MongoDB connection string in the Southeast Asia web app. When the MongoDB client driver connects to your DocumentDB account, Azure automatically figures out where is the closest place to route the connection. No code change is necessary. You only need to add the regions you want to support to your DocumentDB account, which you will do next.

6. Add `Southeast Asia` as a region to your DocumentDB account.

    ```azurecli
    az documentdb update --locations "West Europe"=0 "Southeast Asia"=1 --name $accountname --resource-group myResourceGroup
    ```

3. To finish, add a second endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-2-resource-id>`:

    ```azurecli
    resourceid=$(az appservice web show --name <app_name>-asia --resource-group myResourceGroup --query id --output tsv)
    az network traffic-manager endpoint create -n <app_name>-southeastasia --profile-name myTrafficManagerProfile -g myResourceGroup --type azureEndpoints --target-resource-id resourceid
    ```

## More resources
