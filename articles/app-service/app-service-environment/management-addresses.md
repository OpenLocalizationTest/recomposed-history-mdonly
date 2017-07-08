---
title: App Service Environment management addresses
description: Lists the management addresses used to command an ASE 
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch

ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
---
# App Service Environment management addresses

The App Service Environment(ASE) is a deployment of the Azure App Service into a subnet in your Azure Virtual Network (VNet).  The ASE management traffic traverses the user owned network.  For details on the ASE networking dependencies read [Networking considerations and the App Service Environment][http://docs.microsoft.com/azure/app-service/app-service-environment/network-info]  

This document lists the source IPs for management traffic to the ASE. There are a set of IP addresses that are used across all ASE instances and then there are additional IPs that break down to a regional level.  The incoming management traffic comes in from these IP addresses to ports 454 and 455.

If you want to restrict access to ports 454 and 455 with a Network Security Group then you need to use the IPs that are in the All regions section as well as the IPs matching the region your ASE is deployed in.  

| All regions | 70.37.57.58, 157.55.208.185, 52.174.22.21,13.94.149.179,13.94.143.126,13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141 |
| South Central US & North Central US | 23.102.188.65, 191.236.154.88 |
| Australia Southeast & Australia East | 23.101.234.41, 104.210.90.65 |
| US West & US East | 104.45.227.37, 191.236.60.72 |
| West Europe & North Europe | 191.233.94.45, 191.237.222.191 |
| West Central US & West US 2 | 13.78.148.75, 13.66.225.188 |
| Central US & East US 2 | 104.43.165.73, 104.46.108.135 |
| East Asia & Southeast Asia | 23.99.115.5, 104.215.158.33 |
| Japan East & Japan West | 104.41.185.116, 191.239.104.48 |
| Canada Central & Canada East | 40.85.230.101, 40.86.229.100 |
| UK West & UK South | 51.141.8.34, 51.140.185.75 |
| Korea South & Korea Central | 52.231.200.177, 52.231.32.117 |
| Brazil South & South Central US| 104.41.46.178, 23.102.188.65 |
| Central India & South India | 104.211.98.24, 104.211.225.66 |
| West India & South India | 104.211.160.229, 104.211.225.66 |
