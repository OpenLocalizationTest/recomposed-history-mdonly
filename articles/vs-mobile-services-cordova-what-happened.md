<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Getting Started](/documentation/articles/vs-mobile-services-cordova-getting-started/)
> - [What Happened](/documentation/articles/vs-mobile-services-cordova-what-happened/)

###<span id="whathappened">What happened to my project?</span>

#####References Added

The Azure Mobile Service Client plugin included with all Multi-Device Hybrid Apps has been enabled.
  
#####Connection string values for Mobile Services

Under `services\mobileServices\settings`, a new JavaScript (.js) file with a **MobileServiceClient** was generated containing the selected mobile service’s application URL and application key. The file contains the initialization of a mobile service client object, similar to the following code.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Learn more about mobile services](http://azure.microsoft.com/documentation/services/mobile-services/)