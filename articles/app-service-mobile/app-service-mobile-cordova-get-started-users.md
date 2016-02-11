<properties
	pageTitle="Add authentication on Apache Cordova with Mobile Apps | Azure App Service"
	description="Learn how to use Mobile Apps in Azure App Service to authenticate users of your Apache Cordova app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft."
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="ggailey777"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Add authentication to your Apache Cordova app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Summary

In this tutorial, you add authentication to the todolist quickstart project on Apache Cordova using a supported identity provider. This
tutorial is based on the [Get started with Mobile Apps] tutorial, which you must complete first.

##<a name="register"></a>Register your app for authentication and configure the App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restrict permissions to authenticated users

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ In Visual Studio, open the project that you created when you completed the tutorial [Get started with Mobile Apps], then run your application
in the **Google Android Emulator** and verify that an Unexpected Connection Failure is shown after the app starts.

    This happening becuase the app attempts to access the backend as an unauthenticated user.  The backend redirects the user to an authentication
    page using OAuth.  However, the application does not trust the OAuth endpoint.

Next, you will update the app to authenticate users before requesting resources from the Mobile App backend.

##<a name="add-authentication"></a>Add authentication to the app

1. Open your project in **Visual Studio**, then open the <tt>www/index.html</tt> file for editing.

2. Locate the `Content-Security-Policy` meta tag in the head section.  You will need to add the OAuth host to the list of allowed sources.

    | Provider               | SDK Provider Name | OAuth Host                  |
    | :--------------------- | :---------------- | :-------------------------- |
    | Azure Active Directory | aad               | https://login.windows.net   |
    | Facebook               | facebook          | https://www.facebook.com    |
    | Google                 | google            | https://accounts.google.com |
    | Microsoft              | microsoftaccount  | https://login.live.com      |
    | Twitter                | twitter           | https://api.twitter.com     |

    An example Content-Security-Policy (implemented for Azure Active Directory) is as follows:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    You should replace <tt>https://login.windows.net</tt> with the OAuth host from the table above.  Consult the [Content-Security-Policy documentation]
    for more information on this meta tag.

    Note that some authentication providers do not require Content-Security-Policy changes when used on appropriate
    mobile devices.  For example, no Content-Security-Policy changes are required when using Google authentication
    on an Android device.

3. Open the <tt>www/js/index.js</tt> file for editing.  Although the project will build and run with just the changes
   that have already been made, it is considered Best Practice to explicitly call a login() method to initiate a login
   action.  Locate the `onDeviceReady()` method.  Under the client creation code, add the following:

        // Login to the service
        client.login('SDK Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    For example, for Azure Active Directory, use:

        client.login('aad')

    The login() method is an async function that returns a JavaScript Promise.  The rest of the initialization is placed inside
    the promise response so that it is not executed until the login() method completes.

4. Run your project.  When the project has finished initializing, your application will show the OAuth login page for the
   chosen authentication provider.

<!-- URLs. -->
[Get started with Mobile Apps]: app-service-mobile-cordova-get-started.md
[Content-Security-Policy documentation]: http://content-security-policy.com/
