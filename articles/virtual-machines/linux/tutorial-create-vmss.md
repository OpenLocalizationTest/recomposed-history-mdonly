---
title: Create a highly available app in Azure using Virtual Machine Scale Sets | Microsoft Docs
description: Create and deploy a highly available application on Linux VMs using a virtual machine scale set and the Azure CLI.
services: virtual-machine-scale-sets
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: ''

ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
---

# Create a highly available application on Linux with Virtual Machine Scale Sets
This tutorial shows you how to create a highly available application in a virtual machine scale set. You also learn how to automate the configuration of the virtual machines in the scale set. 

A virtual machine scale set allows you to deploy and manage a set of identical, auto-scaling virtual machines. Scale sets use the same concepts as you learned about to [Build a load balanced, highly available application on Linux virtual machines in Azure](tutorial-load-balance-nodejs.md), such as availability sets, fault and update domains, and load balancers. With a scale set, these resources are created and managed for you. The number of VMs in your scale set can be increased or decreased in response to the customer demand. You can [use a custom image](capture-image.md) as the source for your virtual machine, or configure the VMs during deployment with **cloud-init** as with this tutorial.


## Step 1 - Log in to Azure
To complete this tutorial, open a terminal window and make sure that you have installed the latest [Azure CLI 2.0](/cli/azure/install-azure-cli). If you are not already logged in to your Azure subscription, log in with [az login](/cli/azure/#login) and follow the on-screen directions:

```azurecli
az login
```


## Step 2 - Define your app
You use the same **cloud-init** config from the previous tutorial where you manually created a highly available application. This time, we expand a little to also configure a data disk. For more information about using **cloud-init**, see [Use cloud-init to customize a Linux VM during creation](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Create a file named `cloud-init.txt` and paste the following configuration:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
  - (echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
  - sudo mkfs -t ext4 /dev/sdc1
  - sudo mkdir /datadrive
  - sudo mount /dev/sdc1 /datadrive
```


## Step 3 - Create virtual machine scale set
First, create a resource group with [az group create](/cli/azure/group#create). The following example creates a resource group named `myResourceGroup` in the `westus` location:

```azurecli
az group create --name myResourceGroupVMSS --location westus
```

Now create a virtual machine scale set with [az vmss create](/cli/azure/vmss#create). This command adds a data disk to each VM, and uses **cloud-init** to install and configure your app. The following example creates a scale set named `myScaleSet`:

```azurecli
az vmss create \
  --resource-group myResourceGroupVMSS \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --data-disk-sizes-gb 10 \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```


## Step 4 - Configure firewall
A load balancer was created as part of the virtual machine scale set. The load balancer distributes traffic across a set of defined VMs using load balancer rules. To allow traffic to reach the web app, create a rule with [az network lb probe create](/cli/azure/network/lb/probe#create). The following example creates a rule named `myLoadBalancerRuleWeb`:

```azurecli
az network lb rule create \
  --resource-group myResourceGroupVMSS \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## Step 5 - Management tasks
Throughout the lifecycle of the scale set, you may need to run one or more management tasks. Additionally, you may want to create scripts that automate various lifecycle-tasks. The Azure CLI 2.0 provides a quick way to do those tasks. Here are a few common tasks.

### Set instance count
You can manually increase or decrease the number of virtual machines in the scale set with [az vmss scale](/cli/azure/vmss#scale). The following example increases the number of VMs in your scale set to `5`:

```azurecli
az vmss scale --resource-group myResourceGroupVMSS --name myScaleSet --new-capacity 5
```

Autoscale rules allow you define how to scale up or down the number of VMs in your scale set in response to customer demand such as network traffic or CPU resources. At this time, these rules cannot be set in Azure CLI 2.0. Use the [Azure portal](https://portal.azure.com) to configure autoscale.

### Get connection info
To obtain connection information about the VMs in your scale sets, use [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). This command outputs the public IP address and port for you to connect with SSH:

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroupVMSS --name myScaleSet
```

### Delete resource group
Deleting a resource group also deletes all resources contained within.

```azurecli
az group delete --name myResourceGroupVMSS
```

## Next steps
In this tutorial we defined the web app with **cloud-init** and configured each VM during deployment. For information on capturing an image of an existing VM to use as the source for each VM in your scale set, see [How to generalize and capture a Linux virtual machine](capture_image.md).

To learn more about some of the virtual machine scale set features introduced in this tutorial, see the following information:

- [Azure Virtual Machine Scale Sets overview](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Azure Load Balancer overview](../../load-balancer/load-balancer-overview.md)
- [Control network traffic flow with network security groups](../../virtual-network/virtual-networks-nsg.md)