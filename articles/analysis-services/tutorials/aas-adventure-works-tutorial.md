---
title: "Azure Analysis Services Adventure Works Tutorial | Microsoft Docs"
description: Introduces the Adventure Works tutorial for Azure Analysis Services
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: erikre
editor: ''
tags: ''

ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
---
# Azure Analysis Services - Adventure Works tutorial
This tutorial provides lessons on how to create and deploy a tabular model at the 1400 compatibility level by using [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
  
  
## What you'll learn   
  
-   How to create a new tabular model project at the 1400 compatibility level in SSDT.
  
-   How to import data from a relational database into a tabular model project.  
  
-   How to create and manage relationships between tables in the model.  
  
-   How to create calculated columns, measures, and Key Performance Indicators that help users analyze critical business metrics.  
  
-   How to create and manage perspectives and hierarchies that help users more easily browse model data by providing business and application-specific viewpoints.  
  
-   How to create partitions that divide table data into smaller logical parts that can be processed independent from other partitions.  
  
-   How to secure model objects and data by creating roles with user members.  
  
-   How to deploy a tabular model to an Azure Analysis Services server or an on-premises SQL Server vNext Analysis Services server.  
  
## Scenario  
This tutorial is based on Adventure Works Cycles, a fictitious company. Adventure Works is a large, multinational manufacturing company that produces and distributes metal and composite bicycles to commercial markets in North America, Europe, and Asia. With headquarters in Bothell, Washington, the company employs 500 workers. Additionally, Adventure Works employs several regional sales teams throughout its market base. You are tasked with creating a tabular model for sales and marketing users to analyze Internet sales data in the AdventureWorksDW sample database.  
  
To complete the tutorial, you must complete a number of lessons. Within each lesson are a number of tasks; completing each task in order is necessary for completing the lesson. While in a particular lesson there may be several tasks that accomplish a similar outcome, but how you complete each task is slightly different. This is to show that there is often more than one way to complete a particular task, and to challenge you by using skills you've learned in previous lessons and tasks.  
  
The purpose of the lessons is to guide you through authoring a basic tabular model running by using many of the features included in SSDT. Because each lesson builds upon the previous lesson, you should complete the lessons in order. Once you've completed all of the lessons, you will have authored and deployed the Adventure Works Internet Sales sample tabular model on an Analysis Services server.  
  
This tutorial does not provide lessons or information about managing an Azure Analysis Services server in the Azure portal, managing a server or deployed database by using SQL Server Management Studio (SSMS), or using a reporting client application to connect to a deployed model to browse model data.  
  
## Prerequisites  
In order to complete this tutorial, you'll need the following:  
  
-   The latest version of [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).

-   The latest version of [SQL Server Management Studio (SSMS)(https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 
  
-   A client application such as [Power BI Desktop](https://powerbi.microsoft.com/desktop/) or Excel.    
  
-   A SQL Server or Azure SQL Database with the [AdventureWorksDW2014 sample database](http://go.microsoft.com/fwlink/?LinkID=335807). This sample database includes the data necessary to complete this tutorial.  
  
-   An Azure Analysis Services or SQL Server vNext Analysis Services instance to deploy your model to. Sign up for a free [Azure Analysis Services trial](https://azure.microsoft.com/services/analysis-services/). Sign up and download [SQL Server vNext Community Technology Preview](https://www.microsoft.com/evalcenter/evaluate-sql-server-vnext-ctp).
  
## Lessons  
This tutorial includes the following lessons:  
  
|Lesson|Estimated time to complete|  
|----------|------------------------------|  
|[Lesson 1: Create a new tabular model project](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 minutes|  
|[Lesson 2: Get data](../tutorials/aas-lesson-2-Get-data.md)|20 minutes|  
|[Lesson 3: Mark as Date Table](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 minutes|  
|[Lesson 4: Create relationships](../tutorials/aas-lesson-4-create-relationships.md)|10 minutes|  
|[Lesson 5: Create calculated columns](../tutorials/aas-lesson-5-create-calculated-columns.md)|15 minutes|
|[Lesson 6: Create measures](../tutorials/aas-lesson-6-create-measures.md)|30 minutes|  
|[Lesson 7: Create Key Performance Indicators](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15 minutes|  
|[Lesson 8: Create perspectives](../tutorials/aas-lesson-8-create-perspectives.md)|5 minutes|  
|[Lesson 9: Create hierarchies](../tutorials/aas-lesson-9-create-hierarchies.md)|20 minutes|  
|[Lesson 10: Create partitions](../tutorials/aas-lesson-10-create-partitions.md)|15 minutes|  
|[Lesson 11: Create roles](../tutorials/aas-lesson-11-create-roles.md)|15 minutes|  
|[Lesson 12: Analyze in Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)|20 minutes| 
|[Lesson 13: Deploy](../tutorials/aas-lesson-13-deploy.md)|5 minutes|  
  
## Supplemental lessons  
This tutorial also includes supplemental lessons. These lessons are not required to complete the tutorial, but can be helpful in better understanding advanced tabular model authoring features.  
  
|Lesson|Estimated time to complete|  
|----------|------------------------------|  
|[Detail Rows](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 minutes|
|[Dynamic security](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 minutes|
|[Ragged hierarchies](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 minutes| 

  
## Next step  
To begin the tutorial, continue to the first lesson: [Lesson 1: Create a New Tabular Model Project](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
  
  

