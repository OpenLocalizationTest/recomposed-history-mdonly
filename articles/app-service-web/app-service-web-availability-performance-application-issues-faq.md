---
title: Application FAQs for Azure web apps | Microsoft Docs
description: Get answers to frequently asked questions about availability, performance, and application issues in the Web Apps feature of Azure App Service.
services: app-service\web
documentationcenter: ''
author: simonxjx
manager: cshepard
editor: ''
tags: top-support-issue

ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 5/16/2017
ms.author: v-six

---
# Availability, performance, and application FAQs for App Service web apps

This article has answers to frequently asked questions (FAQs) about availability, performance, and application issues for the [Web Apps feature of Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## My app's performance is slow.
Numerous factors might contribute to slow app performance. For detailed troubleshooting steps, see [Troubleshoot slow web app performance issues](app-service-web-troubleshoot-performance-degradation.md).

## How do I troubleshoot a high CPU consumption scenario?
In some high CPU consumption scenarios, your app might truly require more computing resources. In that case, consider scaling to a higher service tier so the application gets all the resources it needs. Other times, high CPU consumption might be caused by a bad loop or a coding practice. Getting insight into what's triggering increased CPU consumption is a two-part process. First, create a process dump, and then analyze the process dump. For detailed instructions, see [Capture and analyze a dump file for high CPU consumption for Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).


## How do I troubleshoot a high memory consumption scenario?
In some high memory consumption scenarios, your app might truly require more computing resources. In that case, consider scaling to a higher service tier so the application gets all the resources it needs. Other times, a bug in the code might cause a memory leak. A coding practice might also increase memory consumption. Getting insight into what's triggering high memory consumption is a two-part process. First, create a process dump, and then analyze the process dump. Crash Diagnoser from the Site Extension Gallery can efficiently perform both these steps. For detailed guidance, see [Capture and analyze a dump file for intermittent high memory for Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## How do I automate App Service web apps by using PowerShell?
You can use PowerShell cmdlets to manage and maintain App Service web apps. In our blog post [Automate web apps hosted in Azure App Service by using PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), we describe how to use Azure Resource Manager-based PowerShell cmdlets to automate common tasks. The blog post also has sample code for various web apps management tasks. 
For descriptions and syntax for all App Service web apps cmdlets, see [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0).

## How do I view my web app's event logs?

1. Sign in to the [Kudu website](https://*yourwebsitename*.scm.azurewebsites.net).
2. In the menu, select **Debug Console** > **CMD**.
3. Select the **LogFiles** folder.
4. To view event logs, select the pencil icon next to **eventlog.xml**.
5. To download the logs, run the PowerShell cmdlet `Save-AzureWebSiteLog -Name webappname`.

## How do I capture a user-mode memory dump of my web app?

1. Sign in to the [Kudu website](https://*yourwebsitename*.scm.azurewebsites.net).
2. Select the **Process Explorer** menu.
3. Right-click the **w3wp.exe** process or your WebJob process.
4. Select **Download Memory Dump** > **Full Dump**.

## How do I view process-level info for my web app?

Here are two simple ways to view process-level information for your web app:

**Azure portal**
1. Open the **Process Explorer** for the web app.
2. To see the details, select the **w3wp.exe** process.

**Kudu console**
1. Sign in to the [Kudu website](https://*yourwebsitename*.scm.azurewebsites.net).
2. Select the **Process Explorer** menu.
3. For the **w3wp.exe** process, select the **Properties** button.

## When I browse to my app, I see "Error 403 - This web app is stopped." How do I resolve this?

Three conditions can cause this error:

* The web app has reached a billing limit and your site has been disabled.
* The web app has been stopped in the portal.
* The web app has reached a resource quota limit that might apply to a Free or Shared scale mode.

To see what is causing the error and to resolve the issue, follow the steps in [Web Apps: "Error 403 – This web app is stopped"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## Where can I learn more about quotas and limits for various App Service plans?

For information about quotas and limits, see [App Service limits](../azure-subscription-service-limits.md#app-service-limits). 

## How do I decrease the response time for the first request after idle time?

By default, web apps are unloaded if they are idle for a set period of time. This way, the system can conserve resources. The downside is that the response to the first request after the web app is unloaded is longer to allow the web app to load and start serving responses. In Basic or Standard mode, you can turn on the **Always On** setting to keep the app always loaded. This eliminates longer load times after the app is idle.

1. In the Azure portal, go to your web app.
2. Select **Application settings**.
3. For **Always On**, select **On**.

## How do I enable failed request tracing?

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Go to your Azure web app.
3. Select **All Settings** > **Diagnostics Logs**.
4. For **Failed Request Tracing**, select **On**.
5. Select **Save**.
6. On the web app blade, select **Tools**.
7. Select **Visual Studio Online**.
8. If the setting is not **On**, select **On**.
9. Select **Go**.
10. Select **Web.config**.
11. In system.webServer, add this configuration (to capture a specific URL).

    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. To troubleshoot slow performance issues, add this configuration (if the capturing request is taking more than 30 seconds).
    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. To download the failed request traces, in the [portal](https://portal.azure.com), go to your website.
15. Select **Tools** > **Kudu** > **Go**.
18. In the menu, select **Debug Console** > **CMD**.
19. Select the **LogFiles** folder, and then select the folder with a name that starts with **W3SVC**.
20. To see the XML file, select the pencil icon.

## I see the message "Worker Process requested recycle due to 'Percent Memory' limit." How do I address this issue?

The maximum available amount of memory for a 32-bit process (even on a 64-bit operating system) is 2 GB. By default, the worker process is set to 32-bit in App Service (for compatibility with legacy web applications).

Consider switching to 64-bit processes so you can take advantage of the extra memory available in your Web Worker role. This triggers a web app restart, so schedule accordingly.

Also note that a 64-bit environment requires Basic or Standard mode. Free and Shared modes always run in a 32-bit environment.

For more information, see [Configure web apps in App Service](https://docs.microsoft.com/azure/app-service-web/web-sites-configure).

## Why does my request time out after 240 seconds?

Azure Load Balancer has a default *idle timeout* setting of 4 minutes. This is generally a reasonable response time limit for a web request. If your web app requires background processing, we recommend using Azure WebJobs. The Azure web app can call WebJobs and be notified when background processing is finished. You can choose from multiple methods for using WebJobs, including queues and triggers.

WebJobs is designed for background processing. You can do as much background processing as you want in WebJobs. For more information about WebJobs, see [Run background tasks with WebJobs](https://docs.microsoft.com/azure/app-service-web/web-sites-create-web-jobs).

## ASP.NET Core applications sometimes stop responding when hosted in App Service. How do I fix this issue?

If you notice your web app intermittently stops responding or you see a message that says "The specified CGI Application encountered an error and the server terminated the process" in your ASP.NET Core 1.0 app hosted in App Service, you might be running into a known issue with your [Kestrel version](https://github.com/aspnet/KestrelHttpServer/issues/1182).

This issue is fixed in 1.0.2 version of Kestrel. This version is included in the ASP.NET Core 1.0.3 update. To resolve this issue, make sure you update your app dependencies to use Kestrel 1.0.2. Alternatively, you can use one of two workarounds that are described in the blog post [ASP.NET Core 1.0 slow perf issues in App Service web apps](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## I can't find my log files in the file structure of my web app. How do I find them?

If you use the Local Cache feature of App Service, the folder structure of the LogFiles and Data folders for your App Service instance are affected. When Local Cache is used, subfolders are created in the storage LogFiles and Data folders. The subfolders use the naming pattern "unique identifier" + time stamp. Each subfolder corresponds to a VM instance in which the web app is running or has run.

To determine whether you are using Local Cache, see **Application settings** for App Service. If Local Cache is being used, the app setting `WEBSITE_LOCAL_CACHE_OPTION` is set to `Always`. For more information about Local Cache, see the [App Service Local Cache overview](https://docs.microsoft.com/azure/app-service/app-service-local-cache).

If you are not using Local Cache and are experiencing this issue, create a support ticket.

## I see the message "An attempt was made to access a socket in a way forbidden by its access permissions." How do I resolve this?

This error typically happens if the *outbound TCP connections* on the VM instance are exhausted. In App Service, limits are enforced for the maximum number of outbound connections that can be made for each VM instance. For more information, see [Cross-VM numerical limits](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

This error also might occur if you try to access a *local address* from your application. For more information, see [Local address requests](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

For more information, see a blog post about [outgoing connections to Azure websites](http://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## I can't remote debug my web app in Visual Studio. How do I address this?

Numerous factors could prevent you from attaching a Visual Studio debugger to your App Service web app. Our blog post [Manually attach a debugger to your Azure web app](https://blogs.msdn.microsoft.com/jpsanders/2016/02/09/manually-attach-a-debugger-to-azure-web-apps/) provides a workaround that might unblock you so you can debug your app.

## How do I use Visual Studio to remote debug my App Service web app?

For a detailed walkthrough that shows you how to debug your web app by using Visual Studio, see [Remote debug your App Service web app](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
