<properties 
	pageTitle="How to configure Google authentication for your App Services application 
	description="Learn how to configure Google authentication for your App Services application." 
    services="app-services" 
	documentationCenter="" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# How to configure your application to use Google login

This topic shows you how to configure Azure App Services to use Google as an authentication provider. 

>[AZURE.NOTE] This tutorial is about [Azure Mobile Services](http://azure.microsoft.com/en-us/services/mobile-services/), a solution to help you build scalable mobile applications for any platform. Mobile Services makes it easy to sync data, authenticate users, and send push notifications. This page supports the <a href="http://azure.microsoft.com/en-us/documentation/articles/mobile-services-ios-get-started-users/">Get Started with Authentication</a> tutorial which shows how to log users into your app. If this is your first experience with Mobile Services, please complete the tutorial <a href="http://azure.microsoft.com/en-us/documentation/articles/mobile-services-ios-get-started/">Get Started with Mobile Services</a>.

To complete the procedure in this topic, you must have a Google account that has a verified email address. To create a new Google account, go to <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navigate to the <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> website, sign-in with your Google account credentials, click **Create Project**, provide a **Project name**, then click **Create**.

   	![][1]

2. Click **Consent screen**, select your **Email Address**, enter a **Product Name**, then click **Save**. 

3. Click **API & Auth**, click **Credentials**, then click **Create new Client ID**.

   	![][2]

4. Select **Web application**, type your mobile service URL in **Authorized JavaScript Origins**, replace the generated URL in **Authorized Redirect URI** with the URL of your mobile service appended with the path _/login/google_, and then click **Create client ID**.

	>[AZURE.NOTE] For a .NET backend mobile service published to Azure by using Visual Studio, the redirect URL is the URL of your mobile service appended with the path _signin-google_ your mobile service as a .NET service, such as <code>https://todolist.azure-mobile.net/signin-google</code>. 

   	![][3]

5. Under **Client ID for web applications**, make a note of the values of **Client ID** and **Client secret**. 

   	![][4]

    > [AZURE.IMPORTANT] The client secret is an important security credential. Do not share this secret with anyone or distribute it with your app.

You are now ready to use a Google login for authentication in your app by providing the client ID and client secret values to Mobile Services.

## <a name="related-content"> </a>Related Content
Add Authentication to your Mobile App: [Xamarin.iOS](xamarin)


<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/app-services-how-to-configure-google-authentication/app-services-google-new-project.png
[2]: ./media/app-services-how-to-configure-google-authentication/app-services-google-create-client.png
[3]: ./media/app-services-how-to-configure-google-authentication/app-services-google-create-client2.png
[4]: ./media/app-services-how-to-configure-google-authentication/app-services-google-create-client3.png
[5]: ./media/app-services-how-to-configure-google-authentication/app-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://portal.azure.com/
[xamarin]: /en-us/documentation/articles/app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview/
