<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Windows Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Windows Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# <a name="getting-started"> </a>Get started with Mobile Services

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/en-us/develop/mobile/tutorials/get-started" title="Windows Store">Windows Store</a><a href="/en-us/develop/mobile/tutorials/get-started-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-ios" title="iOS">iOS</a> <a href="/en-us/develop/mobile/tutorials/get-started-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/get-started-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-xamarin-android" title="Xamarin.Android">Xamarin.Android</a><a href="/en-us/develop/mobile/tutorials/get-started-sencha/" title="Sencha">Sencha</a>

</div>

This tutorial shows you how to add a cloud-based backend service to a Windows Phone 8 app using Windows Azure Mobile Services. In this tutorial, you will create both a new mobile service and a simple _To do list_ app that stores app data in the new mobile service. The mobile service that you will create uses the supported .NET languages using Visual Studio for server-side business logic and to manage the mobile service. To create a mobile service that lets you write your server-side business logic in JavaScript, see the [JavaScript backend version] of this topic.

A screenshot from the completed app is below:

![][0]

>[WACOM.NOTE]To complete this tutorial, you need a Windows Azure account that has the Windows Azure Mobile Services feature enabled. If you don't have an account, you can create a free trial account in just a couple of minutes. For details, see <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A30A4DDE2&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F" target="_blank">Windows Azure Free Trial</a>. <br />This tutorial requires <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. A free trial version is available.

## <a name="create-new-service"> </a>Create a new mobile service

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Create a new Windows Phone app

Once you have created your mobile service, you can follow an easy quickstart in the Management Portal to either create a new app or modify an existing app to connect to your mobile service. 

In this section you will create a new Windows Phone 8 app that is connected to your mobile service.

1.  In the Management Portal, click **Mobile Services**, and then click the mobile service that you just created.

2. In the quickstart tab, click **Windows Phone 8** under **Choose platform** and expand **Create a new Windows Phone 8 app**.

   	![][6]

   	This displays the three easy steps to create a Windows Phone app connected to your mobile service.

  	![][7]

3. If you haven't already done so, download and install [Visual Studio 2012 Express for Windows Phone] on your local computer.

4. Click **Create TodoItem table** to create a table to store app data.

5. Under **Download and run app**, click **Download**. 

  	This downloads a solution contains projects for both the mobile service and for the sample _To do list_ application that is connected to your mobile service. Save the compressed project file to your local computer, and make a note of where you save it.

5. Under **Publish your service to the cloud**, download your publish profile, save the downloaded file to your local computer, and make a note of where you save it.

## Test the app against the local mobile service

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

## Publish your mobile service

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>In the Windows Phone app project, open the App.xaml.cs file, locate the code that creates a <a href="http://msdn.microsoft.com/en-us/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a> instance, comment-out the code that creates this client using <em>localhost</em> and uncomment the code that creates the client using the remote mobile service URL, which looks like the following:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://<your_mobile_service>.azure-mobile.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        );</code></pre>

	<p>The client will now access the mobile service published to Windows Azure.</p></li>

<li><p>Press the <strong>F5</strong> key to rebuild the project and start the app.</p></li>

<li><p>In the app, type meaningful text, such as <em>Complete the tutorial</em>, and then click <strong>Save</strong>.</p>

	<p><img src="./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png" alt="" /></p>
<p>This sends a POST request to the new mobile service hosted in Windows Azure.</p>
</li>
</ol>

## <a name="next-steps"> </a>Next Steps
Now that you have completed the quickstart, learn how to perform additional important tasks in Mobile Services: 

* [Get started with data]
  <br/>Learn more about storing and querying data using Mobile Services.

* [Get started with authentication]
  <br/>Learn how to authenticate users of your app with an identity provider.

* [Get started with push notifications] 
  <br/>Learn how to send a very basic push notification to your app.

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png





[6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-service-startup.pnG

[10]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Management Portal]: https://manage.windowsazure.com/
[JavaScript version]: /en-us/documentation/articles/mobile-services-windows-phone-get-started