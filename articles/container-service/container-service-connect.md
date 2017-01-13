---
title: Connect to an Azure Container Service cluster | Microsoft Docs
description: Connect to a Kubernetes, DC/OS, or Docker Swarm cluster in Azure Container Service from a remote computer
services: container-service
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure

ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2017
ms.author: rogardle

---
# Connect to an Azure Container Service cluster
After [deploying an Azure Container Service cluster](container-service-deployment.md), you need to connect to the cluster to deploy and manage workloads. This article describes how to connect to the cluster from a remote computer. The Kubernetes, DC/OS, and Docker Swarm clusters all expose REST endpoints. For Kubernetes,
this endpoint is securely exposed on the internet, and you can access it by running the `kubectl` command-line tool from any internet-connected machine. For DC/OS 
and Docker Swarm, you must create a secure shell (SSH) tunnel to securely connect to the REST endpoint. 

> [!NOTE]
> Kubernetes support in Azure Container Service is currently in preview.
>


## Connect to a Kubernetes cluster

Follow these steps to install and configure `kubectl` on your computer.

> [!NOTE] 
> * On Linux or OS X, you might need to run the commands in this section using `sudo`.
> * These commands assume that you have an appropriate private SSH key for the cluster installed in `$HOME/.ssh/id_rsa`. See these 
instructions for [OS X and Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)
or [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md)
for more information. If the SSH connection isn't working, you may need to 
[reset your SSH keys](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

### Install kubectl
One way to install this
tool is to use the `az acs kubernetes install cli` Azure CLI 2.0 (Preview) command. To run this command, make sure that you [installed](/cli/azure/install-az-cli2) the latest Azure CLI 2.0 (Preview) and logged in to an Azure account (`az login`).

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Alternatively, you can download the client directly from the [releases page](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md#downloads-for-v146).

### Download cluster credentials
Once you have `kubectl` installed, you need to copy the cluster credentials to your machine. One way to do
get the credentials is with the `az acs kubernetes get-credentials` command. Pass the name of the resource group and the name of the container service resource:


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

This command downloads the cluster credentials to `$HOME/.kube/config`, where `kubectl` expects it to be located.

Alternatively, you can use `scp` to securely copy the file from `$HOME/.kube/config` on the master VM to your local machine. For example:

```console
mkdir $HOME/.kube/config
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

If you are on Windows, you need to use Bash on Ubuntu on Windows, the PuTTy secure file copy client, or a similar tool.



### Use kubectl

Once you have `kubectl` configured, you can test the connection by listing the nodes in your cluster:

```console
kubectl get nodes
```

You can try other `kubectl` commands. For example, you can view the Kubernetes Dashboard. First, run a proxy to the Kubernetes API server:

```console
kubectl proxy
```

The Kubernetes UI is now available at: `http://localhost:8001/ui`.

For more information, see the [Kubernetes quick start](http://kubernetes.io/docs/user-guide/quick-start/).

## Connect to a DC/OS or Swarm cluster

The DC/OS and Docker Swarm clusters that are deployed by Azure Container Service expose REST endpoints. However, these endpoints are not open to the outside world. To manage these endpoints, you must create a Secure Shell (SSH) tunnel. After an SSH tunnel has been established, you can run commands against the cluster endpoints and view the cluster UI through a browser on your own system. The following sections walk you through creating an SSH tunnel from computers running Linux, OS X, and Windows operating systems.

> [!NOTE]
> You can create an SSH session with a cluster management system. However, we don't recommend this. Working directly on a management system exposes the risk for inadvertent configuration changes.
> 

### Create an SSH tunnel on Linux or OS X
The first thing that you do when you create an SSH tunnel on Linux or OS X is to locate the public DNS name of load-balanced masters. Follow these steps:


1. In the [Azure portal](https://portal.azure.com), browse to the resource group containing your container service cluster. Expand the resource group so that each resource is displayed. 

2. Locate and select the virtual machine of the master. In a DC/OS cluster, this resource has a name beginning with **dcos-master-**. 

    The **Virtual machine** blade contains information about the public IP address, which includes the DNS name. Save this name for later use. 

    ![Public DNS name](media/pubdns.png)

3. Now open a shell and run the `ssh` command by specifying the following values: 

    **PORT** is the port of the endpoint that you want to expose. For Swarm, use port 2375. For DC/OS, use port 80.  
    **USERNAME** is the user name that was provided when you deployed the cluster.  
    **DNSPREFIX** is the DNS prefix that you provided when you deployed the cluster.  
    **REGION** is the region in which your resource group is located.  
    **PATH_TO_PRIVATE_KEY** [OPTIONAL] is the path to the private key that corresponds to the public key you provided when you created the cluster. Use this option with the `-i` flag.

    ```bash
    ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
    ```
    > [!NOTE]
    > The SSH connection port is 2200--not the standard port 22.
    > 

See the examples for DC/OS and Swarm in the following sections.    

### DC/OS tunnel
To open a tunnel to the DC/OS-related endpoints, run a command that is similar to the following:

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

You can now access the DC/OS-related endpoints at:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Similarly, you can reach the rest APIs for each application through this tunnel.

### Swarm tunnel
To open a tunnel to the Swarm endpoint, run a command that is similar to the following:

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Now you can set your DOCKER_HOST environment variable as follows. You can continue to use your Docker command-line interface (CLI) as normal.

```bash
export DOCKER_HOST=:2375
```

### Create an SSH tunnel on Windows
There are multiple options for creating SSH tunnels on Windows. This section describes how to use PuTTY to create the tunnel.

1. [Download PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) to your Windows system.

2. Run the application.

3. Enter a host name that is comprised of the cluster admin user name and the public DNS name of the first master in the cluster. The **Host Name** looks similar to `adminuser@PublicDNSName`. Enter 2200 for the **Port**.

    ![PuTTY configuration 1](media/putty1.png)

4. Select **SSH > Auth**. Add a path to your private key file (.ppk format) for authentication. You can use a tool such as [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) to generate this file from the SSH key used to create the cluster.

    ![PuTTY configuration 2](media/putty2.png)

5. Select **SSH > Tunnels** and configure the following forwarded ports:

    * **Source Port:** Use 80 for DC/OS or 2375 for Swarm.
    * **Destination:** Use localhost:80 for DC/OS or localhost:2375 for Swarm.

    The following example is configured for DC/OS, but will look similar for Docker Swarm.

    > [!NOTE]
    > Port 80 must not be in use when you create this tunnel.
    > 

    ![PuTTY configuration 3](media/putty3.png)

6. When you're finished, click **Session > Save** to save the connection configuration.

7. To connect to the PuTTY session, click **Open**. When you connect, you can see the port configuration in the PuTTY event log.

    ![PuTTY event log](media/putty4.png)

After you've configured the tunnel for DC/OS, you can access the related endpoint at:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

After you've configured the tunnel for Docker Swarm, open your Windows settings to configure a system environment variable named `DOCKER_HOST` with a value of `:2375`. Then, you can access the Swarm cluster through the Docker CLI.

## Next steps
Deploy and manage containers in your cluster:

* [Work with Azure Container Service and Kubernetes](container-service-kubernetes-ui.md)
* [Work with Azure Container Service and DC/OS](container-service-mesos-marathon-rest.md)
* [Work with the Azure Container Service and Docker Swarm](container-service-docker-swarm.md)

