﻿<properties
   pageTitle="Azure Virtual Machine DotNet Core Tutorial 5"
   description="Azure Virtual Machine DotNet Core Tutorial"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/29/2016"
   ms.author="nepeters"/>

# Automating Application Deployment with Virtual Machine Extensions

Once all Azure infrastructural requirements have been identified and translated into a deployment template, the actual application deployment needs to be addressed. Application deployment here is referring to installing the actual application binaries onto Azure resources. For the Music Store sample, .Net Core, NGINX, and Supervisor need to be installed and configured on each virtual machine. The Music Store binaries then need to be installed on the virtual machine, and the Music Store database pre-created.

This document details how Virtual Machine extensions can automate application deployment and configuration to Azure virtual machines. All dependencies and unique configurations are highlighted. For the best experience, pre-deploy an instance of the solution to your Azure subscription and work along with the Azure Resource Manager template. The complete template can be found here – [Music Store Deployment on Ubuntu]( https://github.com/neilpeterson/nepeters-azure-templates/blob/master/dotnet-core-music-linux-vm-sql-db/azuredeploy.json).

## Configuration Script

Virtual Machine extensions are specialized programs that execute against virtual machines to provide configuration automation. Extensions are available for many specific purposes such as anti-virus, logging configuration, and Docker configuration. A custom script extension can be used to run any script against a virtual machine. With the Music Store sample, it is up to the custom script extension to configure the Ubuntu virtual machines and install the Music Store application.

Before detailing how virtual machine extensions are declared in an Azure Resource Manager template, examine the script that is run. This script configures the Ubuntu virtual machine to host the Music Store application. When run, the script installs all needed software, installs the Music store application from source control, and prepare the database. 

To learn more about hosting a .Net Core application on Linux, see [Publish to a Linux production environment](https://docs.asp.net/en/latest/publishing/linuxproduction.html). 


```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/neilpeterson/nepeters-azure-templates/master/dotnet-core-music-linux-vm-sql-db/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/dotnet-core-music-linux-vm-sql-db/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/dotnet-core-music-linux-vm-sql-db/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## VM Script Extension

VM Extensions can be run against a virtual machine at build time by including the extension resource in the Azure Resource Manager template. The extension can be added with the Visual Studio Add Resource wizard, or by inserting valid JSON into the template. The custom script extension can be seen in the Music Store template on line [**365**]( https://github.com/neilpeterson/nepeters-azure-templates/blob/master/dotnet-core-music-linux-vm-sql-db/azuredeploy.json#L365) of the sample template. Notice that the VM extension JSON is nested inside the Virtual Machine resource.

Notice in the below JSON that the script is stored in GitHub. This script could also be stored in Azure Blob storage. Also, note that the template allows the script execution string to constructed such that template parameters values can be used as parameters for script execution. In this case data is provided when deploying the templates, and these values can then be used when executing the script.


```none
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(parameters('vmname-front'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/dotnet-core-music-linux-vm-sql-db/support-scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicstoresqlName'), ' ', parameters('adminUsername'), ' ', parameters('adminPassword'))]"
    }
  }
}
```

For more information on using the custom script extension, see [Custom script extensions with Resource Manager templates](./virtual-machines-linux-extensions-customscript.md).

## Next Step

<hr>

[Explore More Azure Resource Manager Templates](https://github.com/Azure/azure-quickstart-templates)
