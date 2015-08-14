<properties
	pageTitle="Get Started with Azure Mobile Apps for Xamarin.iOS apps"
	description="Follow this tutorial to get started using Azure Mobile Apps for Xamarin.iOS development."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="normesta"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>


#Create a Xamarin.iOS app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Overview

This tutorial shows you how to add a cloud-based backend service to a Xamarin.iOS mobile app using an Azure Mobile App backend.  You will create both a new Mobile App backend and a simple _Todo list_ Xaamrin.iOS app that stores app data in Azure.

Completing this tutorial is a prerequisite for all other Mobile Apps tutorials for Xamarin.Android apps.
 
##Prerequisites

To complete this tutorial, you need the following:

* An active Azure account. If you don't have an account, you can sign up for an Azure trial and get up to 10 free Mobile Apps that you can keep using even after your trial ends. For details, see [Azure Free Trial](http://azure.microsoft.com/pricing/free-trial/).
 
* [Visual Studio Community 2013] or later.  If you install Visual Studio Community 2013, install [Xamarin] separately.  You can install the Xamarin tools when you install Visual Studio 2015.

* A Mac with [Xcode] v7.0 or later and [Xamarin Studio] installed.
 
     >[AZURE.NOTE]If you plan to build your app on a Windows computer by using Visual Studio, you'll still need access to a networked Mac to do it.
 
>[AZURE.NOTE] If you want to get started with Azure App Service before signing up for an Azure account, go to [Try App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), where you can immediately create a short-lived starter Mobile App in App Service. No credit cards required; no commitments.

## Create a new Azure Mobile App backend

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Download the server project

1. On your PC, visit the [Azure Portal]. Click **Browse All** > **Mobile Apps**, then click the Mobile App backend that you just created.

2. In the Mobile App blade, click **Settings** and under **Mobile App** click **Quickstart** > **Xamarin.iOS**.
 
3. Under **Download and run your server project**, click **Download**. Extract the compressed project files to your PC, and open the solution in Visual Studio.
 
## Test your backend project locally

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publish server project to Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Download and run the Xamarin.iOS app

1. On your Mac, open the [Azure Portal] in a browser window.

>[AZURE.NOTE] It's easier to run your Xamarin.iOS app on a Mac. You can run the Xamarin.iOS app by using Visual Studio on your Windows computer if you want, but it's a bit more complicated because you have to connect to a networked Mac. If you're interested in doing that, see [Installing Xamarin.iOS on Windows].

2. Under **Download and run your Xamarin.iOS project**, click the **Download** button.

  	This downloads a project that contains a client application that is connected to your mobile app. Save the compressed project file to your local computer, and make a note of where you save it.

3. Extract the project that you downloaded, and then open it in Xamarin Studio (or Visual Studio).

	![][9]

	![][8]

4. Press the **F5** key to build the project and start the app in the iPhone emulator.

5. In the app, type meaningful text, such as _Learn Xamarin_ and then click the **+** button.

	![][10]

	This sends a POST request to the new mobile app backend hosted in Azure. Data from the request is inserted into the TodoItem table. Items stored in the table are returned by the mobile app backend, and the data is displayed in the list.

>[AZURE.NOTE]You can review the code that accesses your mobile app backend to query and insert data in the QSTodoService.cs C# file.

##Next steps

* [Add authentication to your app ](app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md)
  <br/>Learn how to authenticate users of your app with an identity provider.

* [Add push notifications to your app](app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md)
  <br/>Learn how to send a very basic push notification to your app.

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
[JavaScript backend version]: ../partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: ../app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: ../app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/