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

To complete the procedure in this topic, you must have a Google account that has a verified email address. To create a new Google account, go to <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navigate to the <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> website, sign-in with your Google account credentials, click **Create Project**, provide a **Project name**, then click **Create**.

2. Click **Consent screen**, select your **Email Address**, enter a **Product Name**, then click **Save**. 

3. In the left navigation bar, click **API & Auth**. Then click **Credentials**, and then **Create new Client ID**.

     ![][0]

4. Select **Web application**. Type your App Services gateway URL in **Authorized JavaScript Origins**, and then replace the generated URL in **Authorized Redirect URI** with he URL of your gateway appended with the path, _/signin-google_. For example, `https://contoso.azurewebsites.net/signin-google`. Then click **Create client ID**.

5. Under **Client ID for web applications**, make a note of the values of **Client ID** and **Client secret**. 

    > [AZURE.IMPORTANT] The client secret is an important security credential. Do not share this secret with anyone or distribute it within a client application.

10. Log on to the [Azure Management Portal], and navigate to your App Services gateway.

11. Click the **User authentication** part and select **Google**. Paste in the Client ID and Client secret values which you obtained previously. Then click **Save**.

     ![][1]


You are now ready to use Google for authentication in your app.

## <a name="related-content"> </a>Related Content
Add Authentication to your Mobile App: [Xamarin.iOS](xamarin)


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-services-how-to-configure-google-authentication/app-services-google-create-client.png
[1]: ./media/app-services-how-to-configure-google-authentication/app-services-google-app-configure.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://portal.azure.com/
[xamarin]: /en-us/documentation/articles/app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview/
