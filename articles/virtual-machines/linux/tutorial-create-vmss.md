---
title: Create a Virtual Machine Scale Sets for Linux in Azure | Microsoft Docs
description: Create and deploy a highly available application on Linux VMs using a virtual machine scale set
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: ''

ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/18/2017
ms.author: iainfou
---

# Create a Virtual Machine Scale Set and deploy highly available application on Linux
In this tutorial, you learn how virtual machine scale sets in Azure allow you to quickly scale the number of virtual machines (VMs) running your app. You can scale the number of VMs in the scale set manually, or define rules to autoscale based on CPU usage, memory demand, or network traffic. To see a virtual machine scale set in action, you can build a Node.js app that runs across multiple Linux VMs.

To complete this tutorial, make sure that you have installed the latest [Azure CLI 2.0](/cli/azure/install-azure-cli).


## Scale Set overview
A virtual machine scale set allows you to deploy and manage a set of identical, auto-scaling virtual machines. Scale sets use the same components as you learned about in the previous tutorial to [Create highly available VMs](tutorial-high-availability.md) - VMs in a scale set are created in an availability set and distributed across logic fault and update domains.

VMs are created as needed in a scale set, there is no pre-provisioning of VMs. You can define autoscale rules to control how and when VMs are added or removed from the scale set. These rules can trigger based on metrics such as CPU load, memory usage, or network traffic.

Scale sets support up to 1,000 VMs when you use an Azure platform image. For production workloads, you may wish to [Create a custom VM image](tutorial-custom-images.md). You can create up to 100 VMs in a scale set when using a custom image.


## Define an app to scale
For production use, you may wish to [Create a custom VM image](tutorial-custom-images.md) that includes your application installed and configured. For this tutorial, lets customize the VMs on first boot to quickly see a scale set in action.

In a previous, you learned [How to customize a Linux virtual machine on first boot](tutorial-automate-vm-deployment.md) with cloud-init. You can use the same cloud-init configuration file to install NGINX and run a simple 'Hello World' Node.js app. Create a file named `cloud-init.txt` and paste the following configuration:

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
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## Create a scale set
Before you can create a scale set, create a resource group with [az group create](/cli/azure/group#create). The following example creates a resource group named `myTutorial6` in the `westus` location:

```azurecli
az group create --name myTutorial6 --location westus
```

Now create a virtual machine scale set with [az vmss create](/cli/azure/vmss#create). The following example creates a scale set named `myScaleSet`, use the cloud-int file to customize the VM, and generates SSH keys if they do not exist:

```azurecli
az vmss create \
  --resource-group myTutorial6 \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

It takes a few minutes to create and configure all the scale set resources and VMs.


## Allow web traffic
A load balancer was created automatically as part of the virtual machine scale set. The load balancer distributes traffic across a set of defined VMs using load balancer rules. You can learn more about load balancer concepts and configuration in the next tutorial, [How to load balance virtual machines in Azure](tutorial-load-balancer.md).

To allow traffic to reach the web app, create a rule with [az network lb probe create](/cli/azure/network/lb/probe#create). The following example creates a rule named `myLoadBalancerRuleWeb`:

```azurecli
az network lb rule create \
  --resource-group myTutorial6 \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## Test your app
To see your Node.js app on the web, obtain the public IP address of your load balancer with [az network public-ip show](/cli/azure/network/public-ip#show). The following example obtains the IP address for `myScaleSetLBPublicIP` created as part of the scale set:

```azurecli
az network public-ip show \
    --resource-group myTutorial6 \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Enter the public IP address in to a web browser. The app is displayed, including the hostname of the VM that the load balancer distributed traffic to:

![Running Node.js app](./media/tutorial-create-vmss/running-nodejs-app.png)

To see the scale set in action, you can force-refresh your web browser to see the load balancer distribute traffic across all the VMs running your app.


## Management tasks
Throughout the lifecycle of the scale set, you may need to run one or more management tasks. Additionally, you may want to create scripts that automate various lifecycle-tasks. The Azure CLI 2.0 provides a quick way to do those tasks. Here are a few common tasks.

### Increase or decrease VM instances
You can manually increase or decrease the number of virtual machines in the scale set with [az vmss scale](/cli/azure/vmss#scale). The following example increases the number of VMs in your scale set to `5`:

```azurecli
az vmss scale --resource-group myTutorial6 --name myScaleSet --new-capacity 5
```

Autoscale rules let you define how to scale up or down the number of VMs in your scale set in response to demand such as network traffic or CPU usage. Currently, these rules cannot be set in Azure CLI 2.0. Use the [Azure portal](https://portal.azure.com) to configure autoscale.

### Get connection info
To obtain connection information about the VMs in your scale sets, use [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). This command outputs the public IP address and port for each VM that allows you to connect with SSH:

```azurecli
az vmss list-instance-connection-info --resource-group myTutorial6 --name myScaleSet
```


## Next steps
Tutorial - [Load balance virtual machines](tutorial-load-balancer.md)

Further reading:
- [Azure Virtual Machine Scale Sets overview](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Azure Load Balancer overview](../../load-balancer/load-balancer-overview.md)
- [Control network traffic flow with network security groups](../../virtual-network/virtual-networks-nsg.md)