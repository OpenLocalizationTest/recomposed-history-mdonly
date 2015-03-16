<properties
	pageTitle="What are Web Apps?"
	description="Learn more about App Service Web Apps"
	services="app-service-web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="jaime.espinosa"/>

#What are Web Apps?

Azure App Service is a fully managed Platform as a Service (PaaS) offering for professional developers that brings a rich set of capabilities to web, mobile and integration scenarios. Quickly create and deploy mission critical Web apps that scale with your business by using Azure App Service.

Leverage the power of Azure App Service Web Apps to use the languages and frameworks you know and depend on, deploy your applications quickly to the Azure Cloud and continuously improve your code without having to worry about infrastructure ever again.

![Web Marketplace](./media/app-service-web-overview/marketplace.png)

## More than just Websites ##

Modern enterprises interact with their customers in ever more sophisticated ways. Companies of all types regard their corporate Web presence as a critical part of their business, a major component in their business plan. To accommodate this importance, enterprises are looking for a platform that will provide them with the agility, security and scalability.Furthermore, they require the capability to link to their existing business system, be able to quickly deploy new code and spin up instances across the globe. With Azure App Service and Web Apps, organizations can delight their customers quickly and cost-effectively.

## Why Web Apps? ##

Azure App Service Web Apps as a fully managed Platform-as-a-Service (PaaS) that enables you to build, deploy and scale enterprise-grade web Apps in seconds. Focus on your application code, and let Azure take care of the infrastructure to scale and securely run it for you. Web Apps are:

- **Familiar and Fast** - Use your existing skills to code in your favorite language, framework, and IDE. With just a few clicks, add versioning, updating, single sign-on, identity broker, isolated storage, and performance monitoring to your existing Web Apps.  Access a rich gallery to use as building blocks to accelerate your development. Experience unparalleled developer productivity with cutting edge capabilities like continuous integration, live-site debugging, and industry leading Visual Studio IDE.
- **Enterprise Grade** - Azure Web Apps are designed for building and hosting secure mission-critical applications. Build Active Directory integrated business apps that connect securely to on-premises resources, then host them on a secure cloud platform that is ISO, SOC2, and PCI compliant. All while enjoying enterprise level SLAs.
- **Global Scale** - Azure Web Apps are optimized to provide availability and automatic scale on a global datacenter infrastructure. Easily scale applications up or down on demand. With high availability provided within and across different geographical regions. Replicating data and hosting services in multiple locations is quick and easy, making expansion into new regions and geographies as simple as a mouse click.  

## Web Apps Concepts ##

- **Web Apps Gallery** - Select from an ever-growing list of existing Web Applications. Leverage the best of the OSS App community with one-click installation of packages such as Wordpress, Joomla and Drupal. Get your application development process start right by leveraging frameworks like .NET MVC, Django and CakePHP.
- **Auto Scaling** - Web Apps enable you to quickly scale-up or out to handle any incoming customer load. Manually select the number and size of VMs or set up auto-scaling to scale your servers based on load or schedule.
- **Continuous Integration** - Set up continuous integration and deployment workflows with VSO, GitHub, TeamCity, Hudson or BitBucket – enabling you to automatically build, test and deploy your web app on each successful code check-in or integration tests.
- **Deployment Slots** - Implement [Staged Deployment] [Slots] to verify your code in a pre-production environment which is identical to your production Web App. When satisfied, release a new version of your App with zero downtime by performing a swap operation. 
- **Testing in Production** - Take Staged Deployments to the next level and perform [A/B testing][Tip] to verify your new code with a configurable fraction of your live traffic. 
- **Webjobs** - Run any program or script on Web Apps VMs. Run jobs continuously or on a schedule and scale to run on multiple VMs. Use the Azure [WebJobs SDK][Webjobs] to integrate with Azure Storage or Service Bus.

## Getting Started ##
To get started with Web Apps, follow the [Create an ASP.NET Web App] [create] tutorial.

For more information on Azure App Service platform, see [Azure App Service][appservice].

[appservice]: ../app-service-value-prop-what-is/
[create]: ../web-sites-dotnet-get-started/
[Webjobs]: ../websites-dotnet-webjobs-sdk-get-started/
[Slots]: ../web-sites-staged-publishing/
[Tip]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx 
