﻿---
title: Configuration and Management | Microsoft Docs
description: Configuration and Management
services: azure-service-management, web-apps
documentationcenter: ''
author: simonxjx
manager: cshepard
editor: ''
tags: top-support-issue

ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: web-apps
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 5/12/2017
ms.author: v-six

---
# How do I set the server Timezone for my web app?

* In the Azure Portal, open the Application settings menu of your Azure App Service.
* In the 'Application Settings' menu, scroll down to find 'App settings' and add a setting as shown below:
    * Key = WEBSITE_TIME_ZONE
    * Value = Desired Time Zone
* Save Changes

# Why do my continuous WebJobs fail sometimes?

By default, web apps are unloaded if they are idle for some period of time. This lets the system conserve resources.

In Basic or Standard mode, you can enable Always On to keep the web app loaded all the time.

If your web app runs continuous WebJobs, you should enable Always On, or the webjobs may not run reliably.

For more details, please click [here](https://azure.microsoft.com/en-us/documentation/articles/web-sites-create-web-jobs/#CreateContinuous).

# How to get the outbound IP address for my web app?

Follow these steps to get the list of outbound IP addresses for your web app.
1. Open the **Properties** menu of your web app.
2. Search for OUTBOUND IP ADDRESSES.
You will see the list of Outbound IP addresses listed here.

If your site is hosted on **Application Service Environment**, then please click [here](https://azure.microsoft.com/en-us/documentation/articles/app-service-app-service-environment-network-architecture-overview/#outbound-network-addresses) for instructions to get Outbound IP Address.

# How can I get reserved or dedicated Inbound IP Address for my Azure App Service?

If you need to configure a dedicated\reserved IP address for inbound calls made to the azure web app site, you will need to install and configure an IP based SSL certificate.

Please note that in order to do this your App Service Plan should be in Basic or higher pricing tier.

# Can I export my App Service certificatefor use outside of Azure, such as for a website hosted elsewhere? OR Can I export my App Service certificate for use with other Azure services such as Cloud Services?

App Service certificates are Azure resources and are not intended for use outside of your Azure App Service applications. You cannot export them for use outside of Azure.  App Service certificates cannot be moved between Azure services. They are tied to your App Service applications running under your subscription.

For more details please see [here](https://social.msdn.microsoft.com/Forums/azure/en-US/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


# Why am I seeing 'Partially Succeeded' when I try to perform backup of my web app?

One of the frequent causes for this is that some of your files are in use by the application and hence these files are locked while you performed the backup. This prevents these files from being backed up which results in the 'Partially Succeeded' status. You can potentially prevent this from excluding files from the backup process and choosing to backup only what is needed as explained [here](http://www.zainrizvi.io/2015/06/05/creating-partial-backups-of-your-site-with-azure-web-apps/).

# How can I remove a header from the HTTP Response?

In order to remove the headers, you need to update your site’s web.config file.

For more Information, please see [here](https://azure.microsoft.com/en-us/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

# Is Azure App Service compliant with PCI Standards 3.0 and 3.1?

The Azure App Service Web App is currently in compliance with PCI DSS version 3.0 Level 1. PCI version 3.1 is on our roadmap and planning is already underway on how adoption will proceed.

PCI DSS version 3.1 certification requires disabling TLS 1.0, which is currently not an option for most App Service Plans. However, If you are using App Service Environments or are willing to migrate your workload to App Service Environments, you can get greater control of your environment including disabling TLS 1.0 by contacting Azure Support. In the near future, we will make these settings configurable.

For more information please click here [KB3124528](https://support.microsoft.com/en-us/kb/3124528).

# How do I use staging environment and configure deployment slots?

When you deploy your web app to App Service, you can deploy to a separate deployment slot instead of the default production slot when running in the **Standard** or **Premium** App Service plan mode. Deployment slots are actually live web apps with their own hostnames. Web app content and configurations elements can be swapped between two deployment slots, including the production slot.

Please review this detailed [document](https://azure.microsoft.com/en-us/documentation/articles/web-sites-staged-publishing/) on creating, configuring, using and managing deployment slots.

# How can I access and review WebJob Logs?

Follow these steps to review webjob logs:
1. Login to KUDU website https://*yourwebsitename*.scm.AzureWebsites.net/azurejobs/#/jobs/
2. Select the webjob
3. Click on the Toggle Output button
4. Click on the download link to download the output file
5. For individual runs, click on the Individual Invoke
6. Click on the Toggle Output button
7. Click on the download link

# Why am I getting a Hybrid Connection error with SQL Server with a System.OverflowException: 'Arithmetic operation resulted in an overflow'?

If you are trying to use Azure Hybrid Connections to access SQL Server, an update on May 10, 2016 to .NET may cause connections to fail.

When it fails you will see errors that look like this:

```Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```
## Resolution
We are working to update the Hybrid Connection Manager to fix this issue. In the mean time, please review this [article](https://blogs.msdn.microsoft.com/waws/2016/05/17/hybrid-connection-error-with-sql-server-system-overflowexception-arithmetic-operation-resulted-in-an-overflow/) for workarounds.

# How can I add or edit a URL Rewrite rule?

Here are the steps to add/edit URL Rewrite rule.
1. Configure IIS Manager to connect to your Azure App Service web app using the steps provided at this [link](https://azure.microsoft.com/en-us/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. Then, Add / Edit URL Rewrite rule using IIS Manager using the steps provided at this [link](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

# How can I control inbound traffic to my App service?

At site level, we have two options:
* You can enable Dynamic IP restrictions, click [here](https://azure.microsoft.com/en-us/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) for more details
* You can enable Module Security, click [here](https://azure.microsoft.com/en-us/blog/modsecurity-for-azure-websites/) for more details

If you are using App Service Environment, you can use [Barracuda firewall](https://azure.microsoft.com/en-us/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

# How do I block ports in an Azure App Service web app ?

In the Azure App Services shared tenant environment, it is not possible to block specific ports due the nature of the infrastructure.  Also note that TCP ports 4016, 4018 and 4020 may be open for Visual Studio Remote debugging.

In App Service Environment (ASE), you have full control over Inbound / Outbound traffic and you can use NSG (Network Security groups) to restrict / block specific ports. For more information on ASE, please see [here](https://azure.microsoft.com/en-us/blog/introducing-app-service-environment/).

# How do I capture F12 traces?

## F12 HTTP Trace

1. In Internet Explorer, go to your website

 Note: It is important to log in before doing the next steps, otherwise the F12 trace will capture sensitive login data.
2. Press F12, verify that the Network tab is selected, and then click the green Play button.
3. Do the steps that reproduce the issue.
4. Click the red Stop button.
5. Click the Save button (disk icon), and save the HAR file (in Internet Explorer and Edge)
6. Right click and select Save as HAR with content (in Chrome)

## F12 Console output

1. Click the Console tab.
2. For each tab that contains more than zero items, select the tab (Errors, Warnings, and Information)

 Note: if the tab isn’t selected, its icon will become grey/black when you move the cursor away from it.
3. Right-click in the message area of the pane, click Copy all, and paste & save these into a file.

To view HAR file, you can use this [website](http://www.softwareishard.com/har/viewer/).

# Error when trying to connect an Azure App Service Web App to a VNET that is connected to ExpressRoute.

When trying to connect an Azure App Service Web App to a VNET that's connected to ExpressRoute it will fail with the following error:

"Gateway is not a VPN gateway".

This scenario is currently not supported:

Point-to-site is not supported: You can't enable point-to-site VPN connections to the same VNet that is connected to ExpressRoute.

Point-to-site VPN and ExpressRoute cannot coexist for the same VNet.

For more information please click [here](https://azure.microsoft.com/en-us/documentation/articles/expressroute-howto-coexist-classic/#limits-and-limitations).

# How can I connect an Azure App Service Web App to a VNET with a static routing (policy based) gateway?

Currently it's not supported to connect an Azure App Service Web App to a VNET that has an Static routing (policy based) gateway.

If your target virtual network already exists, it must have point-to-site VPN enabled with a Dynamic routing gateway before it can be connected to an app. You cannot enable point-to-site Virtual Private Network (VPN) if your gateway is configured with Static routing.

For more information, please click [here](https://azure.microsoft.com/en-us/documentation/articles/web-sites-integrate-with-vnet/#getting-started).

# In an App Service Environment (ASE) why can I only create one App Service Plan (ASP) even though I have 2 workers available?

In order to provide fault tolerance, an App Service Environment (ASE) requires that for each worker pool you have at least one additional compute resource allocated.

Each worker pool needs at least one additional compute resource which cannot be assigned workload.
For more information, please click [here](https://azure.microsoft.com/en-us/documentation/articles/app-service-web-how-to-create-an-app-service-environment/#compute-resource-pools).

# Why am I seeing timeouts when trying to create an App Service Environment?

Sometimes creating an App Service Environment (ASE) will fail with the following error in the Activity logs:
```ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```
Make sure that NONE of the following are true:
* Subnet is too small.
* Subnet is not empty.
* ExpressRoute not allowing network connectivity requirements of an ASE
* Bad Network Security Group (NSG) not allowing network connectivity requirements of an ASE
* Forced Tunneling enabled

For more information, please see [here](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

# Why am I not able to delete my App Service Plan?

A common cause for this issue is that there are App Services associated with the App Service Plan you are trying to delete.

First remove all the associated App Services from the App Service Plan. This should allow you to delete the App Service Plan.

# How do I schedule a Webjob?

You can create a scheduled Webjob using CRON expressions
1. Create a settings.job file
2. In this JSON file include a schedule property with a CRON expression as shown below
```{ "schedule": "{second}
{minute} {hour} {day}
{month} {day of the week}" }
```
For more information on scheduled WebJobs, please see [here](https://azure.microsoft.com/en-us/documentation/articles/web-sites-create-web-jobs/#CreateScheduledCRON).

# How can I perform penetration testing for my Azure App Service

To perform penetration testing, you will need to submit a request. Click [here](https://security-forms.azure.com/penetration-testing/terms) to submit a request.

# How can I use a custom domain name for my web app

Several frequently asked questions are answered in our 7 minute screencast at this [link](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). We provide a walkthrough of how to add a custom domain name so that you can use your own URL instead of the AzureWebSites.net URL with your App Service.

In addition, our documentation also provides a detailed walkthrough on mapping a custom domain name. [Link to the documentation](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-custom-domain-name).

# How can I purchase a custom domain for my Web App?

The article at this [link](https://azure.microsoft.com/en-us/documentation/articles/custom-dns-web-site-buydomains-web-app/) explains how to buy and configure a custom domain with App Service Web Apps.

# How do I configure a custom domain name for a web app in Azure App Service that uses Traffic Manager?

The article at this [link](https://azure.microsoft.com/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/) provides instructions for using a custom domain name with Azure App Service that use Traffic Manager for load balancing.

# How can I upload and configure an SSL certificate?

The steps to upload and configure a custom SSL certificate can be found in the documentation at this [link](https://azure.microsoft.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/#step-2-upload-and-bind-the-custom-ssl-certificate).

# How to buy and configure an SSL certificate in Azure for my Azure App Service?

[This article](https://azure.microsoft.com/en-us/documentation/articles/web-sites-purchase-ssl-web-site/) explains how to buy and configure an SSL certificate in Azure for your Azure App Service in simple steps.

# I am trying to move Application Insights resources. What are some things I should be aware of?

Application Insights does not currently enable the move operation. So if your original resource group also includes an Application Insights resource, you cannot move that resource. If you include the Application Insights resource when moving App Service apps, the entire move operation fails. However, the Application Insights and App Service plan do not need to reside in the same resource group as the app for the app to function correctly.

To understand how you can approach this scenario, please review the guidance in the documentation at this [link](https://docs.microsoft.com/en-gb/azure/azure-resource-manager/resource-group-move-resources#app-service-limitations).

# Guidance, checklist and other considerations for Resource Move operations

The documentation in the link below shows you how to move resources to either a new subscription or a new resource group in the same subscription. You can find information about the resource move checklist, which services enable move and which services don't, App Service limitations and several other useful topics in good detail.

Click [here](https://docs.microsoft.com/en-gb/azure/azure-resource-manager/resource-group-move-resources#app-service-limitations) to access the documentation.

# My App Service Certificate is flagged for fraud. How can I take care of this?

During the Domain verification of an App Service Certificate purchase users might see the following error:

“Your certificate has been flagged for possible fraud. The request is currently under review. If the certificate does not become usable within 24 hours, please contact Azure Support.”

As the message indicates, this fraud verification process may take up to 24 hours to complete and during this time user will continue to see this message.

If your App Service Certificate continues to show this message after 24 hours of wait time then, please run the following PowerShell script to contact [certificate provider](https://www.godaddy.com/) directly to resolve this issue.
```Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzureRmResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

# Why does my Azure App Service certificate  show "The certificate has been revoked. You should replace it with a new certificate as soon as possible.”

If your App Service Certificate is showing "The certificate has been revoked. You should replace it with a new certificate as soon as possible.”, you may want to review the [Incident Report](https://blogs.msdn.microsoft.com/waws/2017/01/11/incident-report-from-godaddy-regarding-azure-app-service-certificates/) from GoDaddy regarding Azure App Service Certificates. The article also outlines the next steps.

# How do I schedule my WebJobs to run at a specific time?

For scheduling WebJobs we recommend that you add a CRON expression to the WebJobs settings. This is discussed in more detail at this article. https://github.com/projectkudu/kudu/wiki/WebJobs#scheduling-a-triggered-webjob

# How does Authentication\Authorization work in App Service ?

We have detailed documentation on authentication and authorization at this [link](https://docs.microsoft.com/azure/app-service/app-service-security-readme) which also has information on configuring with various Identify providers as seen below:
* [How to configure your app to use Azure Active Directory login](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication)
* [How to configure your app to use Facebook login](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication)
* [How to configure your app to use Google login](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-google-authentication)
* [How to configure your app to use Microsoft Account login](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication)
* [How to configure your app to use Twitter login](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication)

# How can I redirect the default *.azurewebsites.net domain to my custom domain on Azure Web Apps?

When you create a new website using Azure Web Apps you get a default <sitename>.azurewebsites.net domain assigned to your site. If you add a custom host name to your site and don’t want users to be able to access your default *.azurewebsites.net domain anymore, the post at the following link explains how to redirect all traffic aimed at your site’s default domain to your custom domain instead.

http://www.zainrizvi.io/2016/04/07/block-default-azure-websites-domain/
