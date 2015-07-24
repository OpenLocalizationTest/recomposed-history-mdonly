<properties
	pageTitle="iOS Quick Start with Azure Mobile Apps"
	description="Get started using Azure Mobile Apps for iOS development"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="get-started-article"
	ms.date="07/24/2015"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>iOS Quick Start with Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

This tutorial walks you through a simple _Todo list_ iOS app. You'll use Azure Mobile Apps as the backend for storing data and server-side logic. 

To complete this tutorial, you need the following:

* A PC with Visual Studio Professional 2013 or later
* A Mac with the latest Xcode 
* An active Azure account. For details, see [Azure Free Trial](http://azure.microsoft.com/pricing/free-trial/) and [App Service Pricing](http://azure.microsoft.com/en-us/pricing/details/app-service/). With [Try App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), you can also create a short-lived starter app (no credit card, account, or commitment necessary.)

## Create Azure Backend

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Download Azure Backend Code

1. On your PC, visit [Azure Portal], click **Browse All** > **Mobile Apps** > the backend that you just created.

2. At the top of the blade, click **Add Client** > either **iOS (Objective-C)** or **iOS (Swift)**.

3. Click #2 > **Download and publish your service to the cloud** > **Download**.

## Publish Backend Service

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Download and Run iOS App

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../../includes/app-service-mobile-ios-run-app-preview.md)]


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Azure Portal]: https://portal.azure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
 
