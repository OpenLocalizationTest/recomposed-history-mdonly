<properties 
	pageTitle="Dedicated Groups in Azure Active Directory | Microsoft Azure" 
	description="A topic that explains how to manage groups in Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="swadhwa" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>

 # Dedicated Groups in Azure Active Directory

In Azure Active Directory, dedicated groups are created automatically and group membership for the dedicated groups is also automatic. You cannot add or remove members to and from dedicated groups through the Azure management portal, Windows PowerShell cmdlets, or programmatically. To enable dedicated groups, In the Azure Management Portal, on the Configure tab, set the Enable Dedicated Groups switch to Yes.

In the current public preview release, once the Enable Dedicated Groups switch is set to Yes, you can further enable the directory to automatically create the All Users dedicated group by setting the Enable “All Users” Group switch to Yes. You can then also edit the name of this dedicated group by typing it in the Display Name for “All Users” Group field.

The All Users dedicated group can be useful if you want to assign the same permissions to all the users in your directory. For example, you can grant all users in your directory access to a SaaS application by assigning access for the All Users dedicated group to this application.
