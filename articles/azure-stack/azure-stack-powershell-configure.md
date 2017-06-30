---
title: Configure PowerShell for use with Azure Stack | Microsoft Docs
description: Learn how to configure PowerShell for Azure Stack.
services: azure-stack
documentationcenter: ''
author: SnehaGunda
manager: byronr
editor: ''

ms.assetid:
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun

---

# Configure PowerShell for use with Azure Stack 

This article describes the steps required to connect to an Azure Stack Development Kit instance by using PowerShell. After you connect, you can access the portal and deploy resources through PowerShell. You can use the steps described in this article either from the development kit, or from a Windows-based external client if you are connected through VPN.

This article has detailed instructions to configure PowerShell for Azure Stack. However, if you want to quickly install and configure PowerShell, you can use the script provided in the [Get up and running with PowerShell](azure-stack-powershell-configure-quickstart.md) topic. 

## Prerequisites
* Install [Azure Stack-compatible Azure PowerShell modules](azure-stack-powershell-install.md).  
* Download the [tools required to work with Azure Stack](azure-stack-powershell-download.md).  

## Import the Connect PowerShell module

After you download the required tools, navigate to the downloaded folder and import the **Connect** PowerShell module. To import the Connect module, run the following command in an elevated PowerShell session:

```PowerShell
Set-ExecutionPolicy RemoteSigned
Import-Module .\Connect\AzureStack.Connect.psm1
```

## Configure the PowerShell environment

To configure your Azure Stack environment, do the following:

1. Register an AzureRM environment that targets your Azure Stack instance by using one of the following cmdlets:

   * **Administrative environment**

       ```PowerShell
       Add-AzureStackAzureRmEnvironment `
         -Name "AzureStackAdmin" `
         -ArmEndpoint "https://adminmanagement.local.azurestack.external"
       ```

   * **User environment**

       ```PowerShell
       Add-AzureStackAzureRmEnvironment `
         -Name "AzureStackUser" `
         -ArmEndpoint "https://management.local.azurestack.external" 
       ```
   
   After you've registered the AzureRM environment, you can use all the AzureRM cmdlets in your Azure Stack environment. The output of the previous cmdlet is shown in the following screenshot:

   ![Get environment details](media/azure-stack-powershell-configure/getenvdetails.png)

2. Get the GUID value of the Active Directory tenant that is used to deploy Azure Stack. If your Azure Stack environment is deployed by using:  

   * **Azure Active Directory (Azure AD)**
   
      * To access the **administrative environment**, use:
        ```PowerShell
        $TenantID = Get-AzsDirectoryTenantId `
          -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
          -EnvironmentName AzureStackAdmin
        ```

      * To access the **user environment**, use:
        ```PowerShell
        $TenantID = Get-AzsDirectoryTenantId `
          -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
          -EnvironmentName AzureStackUser
        ```

   * **Active Directory Federation Services**
   
      * To access the **administrative environment**, use:
        ```PowerShell
        $TenantID = Get-AzsDirectoryTenantId `
          -ADFS `
          -EnvironmentName AzureStackAdmin
        ```

      * To access the **user environment**, use:
        ```PowerShell 
        $TenantID = Get-AzsDirectoryTenantId `
          -ADFS `
          -EnvironmentName AzureStackUser 
        ```

## Sign in to Azure Stack

To sign in to your Azure Stack environment, do the following:

1. Sign in to the Azure Stack environment by using one of the following two cmdlets:

   * To sign in to the **administrative portal**, use:
    
       ```PowerShell
       Login-AzureRmAccount `
         -EnvironmentName "AzureStackAdmin" `
         -TenantId $TenantID `
       ```

   * To sign in to the **user portal**, use:

       ```PowerShell
       Login-AzureRmAccount `
         -EnvironmentName "AzureStackUser" `
         -TenantId $TenantID `
       ```

## Next steps
* [Develop templates for Azure Stack](azure-stack-develop-templates.md)
* [Deploy templates with PowerShell](azure-stack-deploy-template-powershell.md)
