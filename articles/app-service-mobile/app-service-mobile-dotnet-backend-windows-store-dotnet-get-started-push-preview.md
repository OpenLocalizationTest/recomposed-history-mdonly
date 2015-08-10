<properties 
	pageTitle="Add push notifications to your Windows Runtime 8.1 universal app | Azure Mobile Apps" 
	description="Learn how to use Azure App Service Mobile Apps and Azure Notification Hubs to send push notifications to your Windows app." 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/09/2015" 
	ms.author="glenga"/>

# Add push notifications to your Windows Store app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Overview

This topic shows you how to send push notifications to a Windows Runtime 8.1 universal app by using Azure App Service Mobile Apps and Azure Notification Hubs. In this scenario, when a new item is added your Mobile App backend sends a push notification to all Windows apps registered with the Windows Notification Service (WNS).

This tutorial is based on the App Service Mobile App quickstart. Before you start this tutorial, you must first complete the quickstart tutorial [Create a Windows app](../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md).

##Prerequisites

To complete this tutorial, you need the following:

* An active [Microsoft Store account](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* Complete the [quickstart tutorial](../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md).


##<a name="review"></a>Review your server project configuration (optional)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-push-preview](../../includes/app-service-mobile-dotnet-backend-enable-push-preview.md)] 

##Register your app for push notifications

Before you can send push notifications to your Windows apps from Azure, you must submit your app to the Windows Store. You can then configure your server project to integrate with WNS.

1. In Visual Studio Solution Explorer, right-click the Windows Store app project, click **Store** > **Associate App with the Store...**. 

    ![][3]
    
2. In the wizard, click **Next**, sign in with your Microsoft account, type a name for your app in **Reserve a new app name**, then click **Reserve**.

3. After the app registration is successfully created, select the new app name, click **Next**, and then click **Associate**. This adds the required Windows Store registration information to the application manifest. 

7. Repeat steps 1 and 3 for the Windows Phone Store app project using the same registration you previously created for the Windows Store app.  

7. Navigate to the [Windows Dev Center](https://dev.windows.com/en-us/overview), sign-in with your Microsoft account, click the new app registration in **My apps**, then expand **Services** > **Push notifications**. 

8. In the **Push notifications** page, click **Live Services site** under **Microsoft Azure Mobile Services**.

9. In the **App Settings** tab, make a note of the values of **Client secret** and **Package SID**. 

    ![][6]

    > [AZURE.IMPORTANT] The client secret and package SID are important security credentials. Do not share these values with anyone or distribute them with your app.

##Configure Mobile App to send push requests

1. Log on to the [Azure Preview Portal], select **Browse** > **Mobile App** > your app > **Push notification services**.

2. In **Windows Notification Service**, enter the **Security key** (client secret) and **Package SID** that you obtained from the Live Services site, then click **Save**.

Your Mobile App backend is now configured to work with WNS.

<!-- URLs. -->
[Azure Preview Portal]: https://portal.azure.com/

##<a id="update-service"></a>Update the service to send push notifications

Now that push notifications are enabled in the app, you must update your app backend to send push notifications. Note that because push notifications are enabled by default in the quickstart project, you do not have to add the NuGet package for push notifications or enable it in the configuration.

1. In the server project, open **Controllers** > **TodoItemController.cs**, and add the following using statement:

		using Microsoft.Azure.Mobile.Server.Notifications;

2. In the **PostTodoItem** method, add the following code after the call to **InsertAsync**:  

        // Get the configuration settings for the server project
        HttpConfiguration config = this.Configuration;

        // Define a WNS message and payload.           
        WindowsPushMessage message = new WindowsPushMessage();
        message.XmlPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Create a new client for push notifications using the configured 
            // notification hub and send the message.
            var client = new PushClient(config);
            var result = await client.SendAsync(message);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    This code tells the notification hub used by the Mobile App backend to send a push notification after a new item is insertion.


## <a name="publish-the-service"></a>Publish the mobile backend to Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>Add push notifications to your app

1. In Visual Studio, open the shared **App.xaml.cs** project file and add the following `using` statements:

        using Windows.Networking.PushNotifications;         

4. In the same file, add the following **InitNotificationsAsync** method definition to the **App** class:
    
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    This code retrieves the ChannelURI for the app from WNS, and then registers that ChannelURI with your App Service Mobile App.
    
5. At the top of the **OnLaunched** event handler in **App.xaml.cs**, add the following call to the new **InitNotificationsAsync** method:

        InitNotificationsAsync();

    This guarantees that the short-lived ChannelURI is registered each time the application is launched.

6. In Solution Explorer double-click **Package.appxmanifest** of the Windows Store app, in **Notifications**, set **Toast capable** to **Yes**.

    From the **File** menu, click **Save All**.

7. Repeat the previous step in the Windows Phone Store app project.

Your app is now ready to receive toast notifications.

##<a id="test"></a>Test push notifications in your app

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push-preview](../../includes/app-service-mobile-windows-universal-test-push-preview.md)]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-submit-win8-app.png
[1]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/notification-hub-associate-win8-app.png
[4]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-select-app-name.png
[5]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit-app.png
[6]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 