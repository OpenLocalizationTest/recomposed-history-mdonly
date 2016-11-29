---
title: N-series driver setup for Linux | Microsoft Docs
description: How to set up NVIDIA GPU drivers for N-series VMs running Linux in Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management

ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/29/2016
ms.author: danlep

---
# Set up GPU drivers for N-series VMs
To take advantage of the GPU capabilities of Azure N-series VMs running a supported Linux distribution, you must install NVIDIA graphics drivers on each VM after deployment. This article is also available for [Windows VMs](virtual-machines-windows-n-series-driver-setup?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

For N-series VM specs, storage capacities, and disk details, see [Sizes for virtual machines](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

For more information about the NVIDIA GPUs on the N-series VMs, see:
* [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (for Azure NC VMs)
* [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (for Azure NV VMs)

> [!NOTE]
> Currently, Linux GPU support is only available on Azure NC VMs running Ubuntu Server 16.04 LTS. 


## Install GPU drivers for NC VMs on Ubuntu 16.04 LTS

Following are instructions to set up the CUDA Toolkit on an Azure NC VM running Ubuntu 16.04 LTS.


1. Make an SSH connection to the Azure N-series VM.

2. To verify that the system has a CUDA-capable GPU, run the following command:

    ```bash
    lspci | grep -i nvdia
    ```
    You will see output similar to the following example (showing an NVIDIA Tesla K80 card):

    ![lspci command output](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

3. To download and install the [CUDA Toolkit 8.0](http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_8.0.44-1_amd64.deb) on the Ubuntu VM, run the following commands:

    ```bash
    CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.44-1_amd64.deb

    wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

    sudo dpkg -i /tmp/${CUDA_REPO_PKG}
    
    rm -f /tmp/${CUDA_REPO_PKG}
    
    sudo apt-get update
    
    sudo apt-get install cuda-drivers
    ``` 

    The installation can take several minutes.

## Verify driver installation


To query the GPU device state, run the [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) command-line utility installed with the driver.

![NVIDIA device status](./media/virtual-machines-linux-n-series-driver-setup/smi.png)




