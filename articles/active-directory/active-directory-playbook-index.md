---
title: Azure Active Directory PoC playbook | Microsoft Docs
description: Explore and quickly implement Identity and Access Management scenarios 
services: active-directory
keywords: azure active directory, playbook, Proof of Concept, PoC
documentationcenter: ''
author: dstefan
manager: asuthar

ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/28/2017
ms.author: dstefan

---
# Azure Active Directory Proof of Concept Playbook 

This article provides guidelines to explore different Azure AD capabilities in a Proof of Concept (PoC). The intended audience of this document is Identity Architects, IT Professionals, and System Integrators

## How to use this Playbook

1. Use the [Theme](#theme) section and pick the area(s) of interest based on your needs.  
2. Scope the PoC by choosing the scenarios that align with your business goals. The shorter the better. We recommend to do it as short and concise as possible to convey the value to the stakeholders while minimizing the complexity to realize it.  
3. Use the PoC Implementation section to understand the scenarios, and what would they mean for your environment. In each scenario, we describe how to set it up (what we call building blocks), and how to navigate the scenarios. 
4. Each building block explains the pre-requisites needed, as well as an approximate time to complete. This can help you during the planning process. 
5. Based on 1-3 Above, define the environment in which to execute. We encourage to strive for a production environment to get a good feel of the experience for your users. 
6. When having conflicting requirements, use this helpful tradeoff matrix 
   * Theme-centric showing of value  
   * Smoothness to prepare, to set up, and to execute the scenarios 
   * Minimal time to execute the target scenarios 
   * As close to production as feasible within your constraints 
>[!NOTE]
> Throughout this document, you will see some specific third party applications and products mentioned as examples for convenience. Azure AD supports thousands of applications in our application gallery that you can use based on your needs and environment. 

## PoC Ingredients 

### Theme
Azure AD provides identity and access solutions across multiple areas in the enterprise. We classify the scenarios in the following areas: 

* Lots of apps, one identity 
* Increase your security 
* Scale with Self Service 

Defining a theme to frame the PoC helps to focus the efforts that resonates with business goals, which oftentimes are the triggers of the interest in a proof of concept in the first place. 