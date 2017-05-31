---
title: Build a Java and MySQL web app in Azure
description: Learn how to get a Java app that connects to the Azure MySQL database service working in Azure appservice .
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/22/2017
ms.author: bbenz
---

# Build a Java and MySQL web app in Azure

This tutorial shows you how to create a Java web app in Azure and connect it to a MySQL database. 
When you are finished, you will have a Spring Boot framework to-do list application storing data in [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) running on [Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview).

![Java app running in Azure appservice](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create a MySQL database in Azure
> * Connect a sample app to the database
> * Deploy the app to Azure
> * Update and redeploy the app
> * Stream diagnostic logs from Azure
> * Monitor the app in the Azure portal


## Prerequisites

1. [Git](https://git-scm.com/)
1. [Java 7 JDK or above](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Prepare local MySQL 

In this step, you create a database in a local MySQL server for use in testing the app locally on your machine.

### Connect to MySQL server

Connect to your local MySQL server from the command line:

```bash
mysql -u root -p
```

If your command runs successfully, then your MySQL server is already running. If not, make sure that your local MySQL server is started by following the [MySQL post-installation steps](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

If you're prompted for a password, enter the password for the `root` account. If you don't remember your root account password, see [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

### Create a database 

In the `mysql` prompt, create a database and a table for the to-do items.

```sql
CREATE DATABASE tododb;
```

Exit your server connection by typing `quit`.

```sql
quit
```

## Create and run the sample app 

In this step, you clone sample app source code from a GitHub repo, configure the app to use the local MySQL database, and run the app on your computer. 

### Clone the sample

From the command prompt, navigate to a working directory and clone the sample repository. 

```bash
git clone https://github.com/Azure-Samples/spring-boot-mysql-todo
```

### Configure the app to use the MySQL database

Update the `spring.datasource.password` value in *src/main/resources/application.properties* with the same password used to open the MySQL command prompt:

```
spring.datasource.password=mysqlpass
```

### Build and run the sample

Build and run the sample using the Maven wrapper included in the repo:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Open your browser to http://localhost:8080 to see in the sample in action. As you add tasks to the list, you can view the updates in the MySQL database  with the folloowing commands issued from the MySQL command prompt:

```SQL
use testdb;
select * from todo_item;
```

Stop the application by hitting `Ctrl`+`C` in the command prompt. 

## Create an MySQL database in Azure

In this step, you create an [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) instance using the [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). You will configure the sample application to use this database in a later step in the tutorial.


1. Use the Azure CLI 2.0 in a terminal window to create the resources needed to host your Java application in Azure appservice. Log in to your Azure subscription with the [az login](/cli/azure/#login) command and follow the on-screen directions. 
   ```azurecli 
   az login 
   ```   
2. Create a [resource group](../azure-resource-manager/resource-group-overview.md) with the [az group create](/cli/azure/group#create) command. An Azure resource group is a logical container where related resources like web apps, databases, and storage accounts are deployed and managed. 
   ```azurecli
   az group create --name myResourceGroup  --location "North Europe"
   ```    
3. Create a server in Azure Database for MySQL (Preview) with the [az mysql server create](/cli/azure/mysql/server#create) command.    
Substitute your own unique MySQL server name where you see the `<mysql_server_name>` placeholder. This name is part of your MySQL server's hostname, `<mysql_server_name>.mysql.database.azure.com`, so it needs to be globally unique. Also substitute `<admin_user>` and `<admin_password>` with your own values.
```azurecli
az mysql server create --name <mysql_server_name> \ 
--resource-group myResourceGroup \ 
--location "North Europe" \
--admin-user <admin_user> \ 
--admin-password <admin_password>
```
When the MySQL server is created, the Azure CLI shows information similar to the following example:
```json
{
  "administratorLogin": "admin_user",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mysql_server_name.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysql_server_name",
  "location": "northeurope",
  "name": "mysql_server_name",
  "resourceGroup": "mysqlJavaResourceGroup",
  ...
}
```
4. Create a firewall rule for your MySQL server to allow client connections by using the [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) command. 
```azurecli
az mysql server firewall-rule create --name allIPs \ 
--server mysql_server_name  \ 
--resource-group myResourceGroup \ 
--start-ip-address 0.0.0.0 \ 
--end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure Database for MySQL (Preview) does not currently automatically enable connections from Azure services. As IP addresses in Azure are dynamically assigned, it is better to enable all IP addresses for now. As the service continues its preview, better methods for securing your database will be enabled.

## Configure the MySQL database in Azure

In the terminal window on your computer, connect to the MySQL server in Azure. Use the value you specified previously for `<admin_user>` and `<mysql_server_name>`.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### Create a database 

In the `mysql` prompt, create a database and a table for the to-do items.

```sql
CREATE DATABASE tododb;
```

### Create a user with permissions

Create a database user and give it all privileges in the `tododb` database. Replace the placeholders `<Javaapp_user>` and `<Javaapp_password>` with your own unique app name.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Exit your server connection by typing `quit`.

```sql
quit
```

## Deploy the app to Azure App Service

Create an Azure App Service plan with the **FREE** pricing tier using the  [az appservice plan create](/cli/azure/appservice/plan#create) CLI command. The appservice plan defines the physical resources used to host your apps. All applications assigned to an appservice plan share these resources, allowing you to save cost when hosting multiple apps. 

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

When the plan is ready, the Azure CLI will show similar information to the following example:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  <JSON data removed for brevity.>
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
``` 

### Create an Azure Web app

 Use the [az webapp create](/cli/azure/appservice/web#create) CLI command to create a web app definition in the `myAppServicePlan` App Service plan. The web app definition provides a URL to access your application with and configures several options to deploy your code to Azure. 

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Substitute the `<app_name>` placeholder with your own unique app name. This unique name will be used as the part of the default domain name for the web app, so the name needs to be unique across all apps in Azure. You can map a custom domain name entry to the web app before you expose it to your users.

When the web app definition is ready, the Azure CLI shows information similar to the following example: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    <JSON data removed for brevity.>
}
```

### Set Java and Tomcat versions

Set the Java version, Java App Server (container) type, and container version by using the [az appservice web config update](/cli/azure/appservice/web/config#update) command.

```azurecli
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### Configure the app to use the Azure SQL database

Before running the sample app, you'll need to set properties on the web app to use the Azure MySQL database you created. These properties override the values set in the application.properties inside the packaged web app. Set the following using [az webapp config appsettings](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings):

```azurecli
az webapp config appsettings set --settings  \ 
SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" \
--resource-group myResourceGroup --name app_name
```

```azurecli
az webapp config appsettings set --settings  \ 
SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name  \
--resource-group myResourceGroup --name app_name
```

```azurecli
az webapp config appsettings set --settings  \ 
SPRING_DATASOURCE_URL=Javaapp_password   \
--resource-group myResourceGroup --name app_name
```

### Get FTP deployment credentials 
You can deploy your application to Azure appservice in various ways including FTP, local Git, GitHub, Visual Studio Team Services, and BitBucket. 
For this example, FTP to deploy the .WAR file built previously on your local machine to Azure App Service.

To determine what credentials to pass along in an ftp command to the Web App, Use [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#list-publishing-profiles) command: 

```azurecli
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --o table
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### Upload the app using FTP

Use your favorite FTP method to deploy the .WAR file to the /site/wwwroot/webapps , removing the existing default (ROOT) application and replacing it with the sample.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### Test the Azure web app

Browse to `http://<app_name>.azurewebsites.net/` and add a few tasks to the list. 

![Java app running in Azure appservice](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Congratulations!** You're running a data-driven Java app in Azure appservice.
To update the app, repeat the maven clean package command and redeploy the app via FTP.

## Manage your Azure web app
Go to the Azure portal to see the web app you created by signing in to [https://portal.azure.com](https://portal.azure.com).

From the left menu, click **appservice**, then click the name of your Azure web app.

You should now be in your web app's _blade_ (a portal page that opens horizontally).

By default, your web app's blade shows the **Overview** page. This page gives you a view of how your app is doing. Here, you can also perform basic management tasks like browse, stop, start, restart, and delete. The tabs on the left side of the blade show the different configuration pages you can open.

In the **Application Settings** page, 

![Azure appservice Web App Application Settings](./media/app-service-web-tutorial-java-mysql/appservice-web-app-application-settings.png)

These tabs in the blade show the many great features you can add to your web app. The following list gives you just a few of the possibilities:
* Map a custom DNS name
* Bind a custom SSL certificate
* Configure continuous deployment
* Scale up and out
* Add user authentication

## More resources
- [Map an existing custom DNS name to Azure Web Apps](app-service-web-tutorial-custom-domain.md)
- [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
- [Web apps CLI scripts](app-service-cli-samples.md)
