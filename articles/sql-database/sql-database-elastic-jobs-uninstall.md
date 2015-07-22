<properties 
	pageTitle="How to uninstall elastic database job tool" 
	description="How to uninstall elastic database job tool" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="ddove; sidneyh"/>

## To uninstall the Elastic Database job components using the Azure portal

1. Open the [Azure portal](https://ms.portal.azure.com/).
2. Navigate to the subscription that contains **Elastic Database jobs** components, namely the subscription in which Elastic Database jobs components were installed.
3. Click **Browse** and click **Resource groups**.
4. Select the resource group named "__ElasticDatabaseJob".
5. Delete the resource group.

## To uninstall the Elastic Database job components using PowerShell

1.	Launch a Microsoft Azure PowerShell command window and navigate to the \content sub-directory under the Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x folder: Type cd \content

	`PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*>cd content`

2.	Execute the .\UninstallElasticDatabaseJobs.ps1 PowerShell script.

    `Unblock-File .\UninstallElasticDatabaseJobs.ps1` 
	`PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*>.\UninstallElasticDatabaseJobs.ps1`

Or simply, execute the following script:
		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job is uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job is now uninstalled."

## Next steps

To re-install the Elastic Database jobs, see [Installing the Elastic Database job service](sql-database-elastic-jobs-service-installation.md)

For an overview of the Elastic Database job service, see [Elastic jobs overview](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-job-uninstall/
 