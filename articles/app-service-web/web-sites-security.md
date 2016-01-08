<properties
	pageTitle="Secure an app in Azure App Service"
	description="Learn how to secure a web app, mobile app backend, or API app in Azure App Service."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="01/08/2015"
	ms.author="cephalin"/>


#Secure an app in Azure App Service

This article helps you get started on securing your web app, mobile app backend, or API app in Azure App Service. 

Security in Azure App Service has two levels: 

- **Infrastructure and platform** - You trust azure to have the services you need to actually run things securely in the cloud.
- **Application** - You need to design the app itself securely. This includes how you integrate with Azure Active Directory, how you manage certificates, and how you make sure that you can securely talk to different services. 

Because App Service maintains the Azure VMs, storage, network connections, web frameworks, management and integration features and much more, it is actively secured and hardened and goes 
through vigorous compliance and checks on a continuous basis to make sure that:

- Your App Service apps are isolated from both the Internet from other customers' Azure resources.
- Communication between your App Service app and other Azure resources in a resource group stays within Azure and doesn't cross any network boundaries.
- All communication between your App Service app and external resources, such as PowerShell management, command-line interface, Azure SDKs, REST APIs, and hybrid connections, are properly encrypted.
- 24-hour threat management protects your App Service app resources from malware, DDoS, and other threats. 

For more information, see [Azure Trust Center](/support/trust-center/security/).

Briefly, this means that Azure secures the infrastructure and the platform while you need to secure your application code itself. 


Securing your apps in a cloud environment has two elements: what the cloud provider (Azure) does for you and what you need to do (what Azure doesn't do for you). 

> [AZURE.NOTE] A full discussion of security considerations for web-based applications is beyond the scope of this document. As a starting point for further guidance on securing web applications, see the [Open Web Application Security Project (OWASP)]( https://www.owasp.org/index.php/Main_Page), specifically the [top 10 project.](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), which lists the current top 10 critical web application security flaws, as determined by OWASP members.


One of the challenges of developing a web app is how to provide a safe and secure service for your customers. 


Never seen as plaintext in the DB, we have enhanced client drivers in SQL to do the encrypt/decrypt as the data passes through, and the client drivers integrate with your HSM or key management system so only application components and people who have access to the key management will see the encrypted data. And that addresses the MITM attack, a lot of the problems. Something a lot of people keep asking about in the cloud. And you have key masking features where we mask data. So we've introduced a lot of security features that are great for on-prem or in the cloud. If you listen to the news it's clear that on-prem isn't any safer than the cloud. So we're trying to provide security features for everyone.

Keyvault is becoming a key story. They put all the certificates there, the secrets there,this is a central place, and they use it for all the secrets inside configuration rather than having it as part of their configuration and the whole management around that. Keyvault is becoming critical for storing all of that. That's a fantastic service.

## What Azure does for you

SQL Database
- [Securing your SQL Database](sql-database-security.md)
- [Get started with SQL Database Threat Detection](sql-database-threat-detection-get-started.md)

We highly integrate with SQL in our web apps and all the connection strings are encrypted across the board and only being open on the box when actually the app runs. Also when you deploy with ARM, all the secrets in the same group stays within Azure and not cross any network boundaries. It doesn't even go to your local dev box if you don’t want to. So you can have a full deployment, all the secrets stay within the datacenter and none of the secrets leak out.

Check out . 




##<a name="https"></a> Secure communications

If you use the ***.azurewebsites.net** domain name created for your web app, you can immediately use HTTPS, as an SSL certificate is provided for all ***.azurewebsites.net** domain names. If your site uses a [custom domain name](web-sites-custom-domain-name.md), you can upload an SSL certificate to [enable HTTPS](web-sites-configure-ssl-certificate.md) for the custom domain.

##<a name="develop"></a> Secure development

### Publishing profiles and publish settings

When developing applications, performing management tasks, or automating tasks using utilities such as **Visual Studio**, **Web Matrix**, **Azure PowerShell** or the **Azure Command-Line Interface (Azure CLI)**, you can use either a *publish settings* file or a *publishing profile*. Both authenticate you to Azure, and should be secured to prevent unauthorized access.

* A **publish settings** file contains

	* Your Azure subscription ID

	* A management certificate that allows you to perform management tasks for your subscription *without having to provide an account name or password*.

* A **publishing profile** file contains

	* Information for publishing to your web app

If you use a utility that uses publish settings or publish profile, import the file containing the publish settings or profile into the utility and then **delete** the file. If you must keep the file, to share with others working on the project for example, store it in a secure location such as an **encrypted** directory with restricted permissions.

Additionally, you should make sure the imported credentials are secured. For example, **Azure PowerShell** and the **Azure Command-Line Interface (Azure CLI)** both store imported information in your **home directory** (*~* on Linux or OS X systems and */users/yourusername* on Windows systems.) For extra security, you may wish to **encrypt** these locations using encryption tools available for your operating system.

### Configuration settings, and connection strings
It's common practice to store connection strings, authentication credentials, and other sensitive information in configuration files. Unfortunately, these files may be exposed on your website, or checked into a public repository, exposing this information.

Azure App Service allows you to store configuration information as part of the Web Apps runtime environment as **app settings** and **connection strings**. The values are exposed to your application at runtime through *environment variables* for most programming languages. For .NET applications, these values are injected into your .NET configuration at runtime.

**App settings** and **connection strings** are configurable using the [Azure Portal](http://portal.azure.com) or utilities such as PowerShell or the Azure CLI.

For more information on app settings and connection strings, see [Configuring web apps](web-sites-configure.md).

### FTPS

Azure provides secure FTP access access to the file system for your web app through **FTPS**. This allows you to securely access the application code on the web app as well as diagnostics logs. The FTPS link for your web app can be found with the following steps:

1. Open the [Azure Portal](http://portal.azure.com).
2. Select **Browse All**.
3. From the **Browse** blade, select **Web Apps**.
4. From the **Web Apps** blade, Select the desired web app.
5. From the web app's blade, select **All settings**.
6. From the **Settings** blade, select **Properties**.
7. The FTP and FTPS links are provided on the **Settings** blade. 

For more information on FTPS, see [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

>[AZURE.NOTE] If you want to get started with Azure App Service before signing up for an Azure account, go to [Try App Service](http://go.microsoft.com/fwlink/?LinkId=523751), where you can immediately create a short-lived starter web app in App Service. No credit cards required; no commitments.

## Next steps

For more information on the security of the Azure platform, information on reporting a **security incident or abuse**, or to inform Microsoft that you will be performing **penetration testing** of your site, see the security section of the [Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/security/).

For more information on **web.config** or **applicationhost.config** files in web apps, see [Configuration options unlocked in Azure App Service web apps](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

For information on logging information for web apps, which may be useful in detecting attacks, see [Enable diagnostic logging](web-sites-enable-diagnostic-log.md).

## What's changed
* For a guide to the change from Websites to App Service see: [Azure App Service and Its Impact on Existing Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)
