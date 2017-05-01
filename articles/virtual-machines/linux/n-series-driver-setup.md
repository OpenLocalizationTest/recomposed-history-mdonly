---
title: Azure N-series driver setup for Linux | Microsoft Docs
description: How to set up NVIDIA GPU drivers for N-series VMs running Linux in Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/01/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017

---

# Set up GPU drivers for N-series VMs running Linux

To take advantage of the GPU capabilities of Azure N-series VMs running a supported Linux distribution, NVIDIA graphics drivers must be installed on each VM. This article provides driver setup steps after you deploy an N-series VM. Driver setup information is also available for [Windows VMs](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


For N-series VM specs, storage capacities, and disk details, see [GPU Linux VM sizes](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



## Supported distributions and drivers

The following distributions from the Azure Marketplace are supported to run NVIDIA graphics drivers on N-series Linux VMs.

### NC VMs (Tesla K80 card)
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* CentOS-based 7.3 

**Supported drivers**: NVIDIA CUDA 8.0, driver branch R375. [Installation steps](#install-CUDA-drivers-for-NC-VMs)


### NV VMs (Tesla M60 card)
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* CentOS-based 7.3 


**Supported drivers**: NVIDIA GRID 4.2, driver branch R367. [Installation steps](#install-GRID-drivers-for-NV-VMs)


> [!WARNING] 
> Installation of third-party software on Red Hat products affects the Red Hat support terms. See the [Red Hat Knowledgebase article](https://access.redhat.com/articles/1067).
>




## Install CUDA drivers for NC VMs

Here are steps to involve NVIDIA drivers on Linux NC VMs from the NVIDIA CUDA Toolkit 8.0. 

C and C++ developers can optionally install the full Toolkit to build GPU-accelerated applications. For more information, see the [CUDA Installation Guide](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Driver download links provided here are current at time of publication. For the latest drivers, visit the [NVIDIA](http://www.nvidia.com/) website.

To install CUDA Toolkit, make an SSH connection to each VM. To verify that the system has a CUDA-capable GPU, run the following command:

```bash
lspci | grep -i NVIDIA
```
You will see output similar to the following example (showing an NVIDIA Tesla K80 card):

![lspci command output](./media/n-series-driver-setup/lspci.png)

Then run commands specific for your distribution.

### Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
The installation can take several minutes.

To optionally install the complete CUDA toolkit, type:

```bash
sudo apt-get install cuda
```

Reboot the VM and proceed to verify the installation.

### CentOS 7.3 or Red Hat Enterprise Linux 7.3

> [!IMPORTANT] 
> Because of a known issue, NVIDIA CUDA driver installation fails on NC24r VMs running CentOS 7.3 or Red Hat Enterprise Linux 7.3.
>

First, get updates. 

```bash
sudo yum update

sudo reboot
```

Reconnect to the VM and continue installation with the following commands:

```bash
sudo yum install kernel-devel

sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install dkms

CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo yum install cuda-drivers
```

The installation can take several minutes. To optionally install the complete CUDA toolkit, type:

```bash
sudo yum install cuda
```

Reboot the VM and proceed to verify the installation.


### Verify driver installation


To query the GPU device state, SSH to the VM and run the [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) command-line utility installed with the driver. 

Output similar to the following appears:

![NVIDIA device status](./media/n-series-driver-setup/smi.png)


### CUDA driver updates

We recommend that you periodically update CUDA drivers after deployment.

#### Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

After the update completes, restart the VM.

#### CentOS 7.3 or Red Hat Enterprise Linux 7.3

```bash
sudo yum update
```

## Install GRID drivers for NV VMs


## Incompatibility of Linux kernel 4.4.0.75 on Ubuntu 16.04 LTS

There is a known issue with Azure N-series VMs running the 4.4.0-75 Linux kernel on Ubuntu 16.04 LTS. You should not upgrade past kernel version 4.4.0-72. Otherwise, the NVIDIA drivers may stop working on Ubuntu 16.04 LTS VMs.


To explicitly install 4.4.0-72: 

```bash
sudo apt-get update && sudo apt-get install linux-image-4.4.0-72-generic linux-tools-4.4.0-72 linux-cloud-tools-4.4.0-72 linux-headers-4.4.0-72
```


To explicitly uninstall 4.4.0-75: 

```bash
dpkg -l | awk '{print $2}' | grep 4.4.0-75 | xargs sudo apt-get purge -y
```

To reinstall CUDA drivers (optional):

```bash
dpkg -l | awk '{print $2}' | grep nvidia | xargs sudo apt-get install --reinstall -y
```

After driver installation, reboot the VM.



## Next steps

* For more information about the NVIDIA GPUs on the N-series VMs, see:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (for Azure NC VMs)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (for Azure NV VMs)

* If you want to capture an image of a Linux VM on which you installed NVIDIA drivers, see [How to generalize and capture a Linux virtual machine](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).