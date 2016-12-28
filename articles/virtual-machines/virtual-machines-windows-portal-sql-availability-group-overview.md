---
title: Overview of SQL Server Availability Groups | Azure VM 
description: "This article introduces SQL Server Availability Groups on Azure virtual machines."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management

ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: "12/28/2016"
ms.author: mikeray

---

# Introducing SQL Server Always On Availability Groups on Azure Virtual Machines #

This article introduces SQL Server Availability Groups on Azure Virtual Machines. 

Always On Availability Groups on Azure Virtual Machines are similar to Always On Availability Groups on premises. For more information, see [Always On Availability Groups (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

The diagram illustrates the parts of a complete SQL Server Availability Group in Azure Virtual Machines.

![Availability Group](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

The key difference for an Availability Group in Azure Virtual Machines is that the Azure virtual machines, require a [load balancer](../load-balancer/load-balancer-overview.md). The load balancer holds the IP addresses for the availability group listener. If you have more than one availability group each group requires a listener. One load balancer can support multiple listeners.

When you are ready to build a SQL Server Availability Group on Azure Virtual Machines, refer to these tutorials.

## Automatically Create an Availability Group from a template

[Configure Always On availability group in Azure VM automatically - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## Manually Create an Availability Group in Azure portal

You can also create the virtual machines yourself without the template. First, complete the prerequisites, then create the availability group. See the following topics: 

- [Configure prerequisites for SQL Server Always On Availability Groups on Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Create Always On Availability Group to improve availability and disaster recovery](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## Configure Load balancers

[Create a load balancer for the availability group listener](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).

## Concepts

