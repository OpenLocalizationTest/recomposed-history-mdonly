<properties 
	pageTitle="Remotely debug an Azure API app" 
	description="Learn how to remotely debug an Azure API App using Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Protect an API app: Add Azure Active Directory or social provider authentication

## Overview

In the [Deploy an API app](/app-service-dotnet-deploy-api-app/) tutorial, you deployed an API app with **Available to anyone** access level. This tutorial shows how to protect an API app so that only authenticated users can access it.

You'll perform the following steps:

- Call the API app to verify that it's working.
- Apply authentication rules to the API app.
- Call the API app again to verify that it rejects unauthenticated requests.
- Log in to the configured provider.
- Call the API app again to verify that authenticated access works.

## Prerequisites

This tutorial works with the API app that you created in [Create an API app](/app-service-dotnet-create-api-app/) and deployed in [Deploy an API app](/app-service-dotnet-deploy-api-app/).

## Use the browser to call the API app 

The simplest way to verify that your API app is publicly accessible is to call it from a browser.

1. Open a browser window and enter in the address bar the URL that calls your API app's Get method.  The URL follows this pattern:

    	http://[resourcegroupname]gateway.azurewebsites.net/[apiappid]/api/contacts/get

	For example, if you named your resource group myfirstrg and your API app ID is myfirstapiapp, the URL would be the following:

    	http://myfirstrggateway.azurewebsites.net/myfirstapiapp/api/contacts/get

	Different browsers handle API calls differently. The image shows a successful call from a Chrome browser.

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)
	<!--todo: replace with screenshot not showing hunter etc.-->

2. Save the URL you used; you'll use it again later in the tutorial.

## Protect the API app

When you deployed your API app, you deployed it to a resource group. You can add web apps and other API apps to the same resource group, and each API app within the resource group can have one of three accessibility settings:
<!--todo: diagram showing different accessibility settings-->

- **Public (anonymous)** - Anyone can call the API app from outside the resource group without being logged in.
- **Public (authenticated)** - Only authenticated users are allowed to call the API app from outside the resource group.
- **Internal** - Only other API apps or web apps in the same resource group are allowed to call the API app.

When Visual Studio created the resource group for you, it also created a *gateway*.  A gateway is a special web app that handles all requests destined for API apps in the resource group.

When you go to the resource group's blade in the [preview portal](https://portal.azure.com/), you can see your API app and the gateway in the diagram.

![Resource group diagram](./media/app-service-api-dotnet-add-authentication/rgdiagram.png)

To configure your API app to accept only authenticated requests, you'll set its accessibility to **Public (authenticated)** and you'll configure the gateway to require authentication from a provider such as Azure Active Directory, Google, or Facebook.

1. In the Azure [preview portal] click **Browse > API Apps**, and then click the name of the API app that you want to protect.

	![Browse](./media/app-service-api-dotnet-add-authentication/browse.png)

	![Select API app](./media/app-service-api-dotnet-add-authentication/select.png)

2. In the API app blade, click **Settings**, and then click **Basic settings**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettings.png)

	![Click Basic settings](./media/app-service-api-dotnet-add-authentication/clickbasicsettings.png)

3. In the **Basic Settings** blade, change **Access Level** to **Public (authenticated)**, and then click **Save**.

	![Click Basic settings](./media/app-service-api-dotnet-add-authentication/setpublicauth.png)

	You have now protected the API app from unauthenticated access. Next you have to configure the gateway to specify which authentication provider to use.

4. Scroll left back to the API app blade, and then click the link to the gateway.

	![Click gateway](./media/app-service-api-dotnet-add-authentication/gateway.png)

7. In the **Gateway** blade, click **Settings**, and then click **Identity**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettingsingateway.png)

	![Click Identity](./media/app-service-api-dotnet-add-authentication/clickidentity.png)

	From the **Identity** blade you can navigate to different blades for configuring authentication using Azure Active Directory and several other providers.

	![Identity blade](./media/app-service-api-dotnet-add-authentication/identityblade.png)
  
3. Choose the identity provider you want to use, and follow the steps in the corresponding article to configure your API app with that provider. These articles were written for mobile apps, but the procedures are the same for API apps. Some of the procedures require you to use both the [management portal] and the [preview portal]. 

 - [Microsoft Account](../app-service-mobile-how-to-configure-microsoft-authentication-preview/)
 - [Facebook login](../app-service-mobile-how-to-configure-facebook-authentication-preview/)
 - [Twitter login](../app-service-mobile-how-to-configure-twitter-authentication-preview/)
 - [Google login](../app-service-mobile-how-to-configure-google-authentication-preview/)
 - [Azure Active Directory](../app-service-mobile-how-to-configure-active-directory-authentication-preview/)

As an example, the following screen shots show what you should see in the [management portal] pages and [preview portal] blades after you have set up Azure Active Directory authentication.

In the [preview portal], The **Azure Active Directory** blade has a **Client ID** from the application you created in the Azure Active Directory tab of the [management portal], and **Allowed Tenants** has your Azure Active Directory tenant (for example, "contoso.onmicrosoft.com").

![Azure Active Directory blade](./media/app-service-api-dotnet-add-authentication/tdinaadblade.png)

In the [management portal], the **Configure** tab for the application you created in the **Azure Active Directory** tab has the **Sign-on URL**, **App ID URI**, and **Reply URL** from the **Azure Active Directory** blade in the [preview portal].

![Management portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal1.png)

![Management portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal2.png)

![Management portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal3.png)

![Management portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal4.png)

	(The Reply URL in the image shows the same URL twice, once with `http:` and once with `https:`.)

## Verify that authentication works 

1. Open a browser window, and in the address bar enter the URL that calls your API app's `Get` method, as you did earlier.

	This time the attempt to access the API app results in an error message.

	![Chrome Get response fail](./media/app-service-api-dotnet-add-authentication/chromegetfail.png)

2. In the browser, go to the login URL: 

    	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	For example, if you named your resource group myfirstrg and you configured the gateway for Azure Active Directory authentication, the URL would be the following:

    	http://myfirstrggateway.azurewebsites.net/login/aad

	Notice that unlike the earlier URL, this one does not include your API app name:  the gateway is authenticating you, not the API app.  The gateway handles authentication for all API apps in the resource group.

3. Enter your credentials when the browser displays a login page. 
 
	If you configured Azure Active Directory login, use one of the users listed in the **Users** tab for the application you created in the Azure Active Directory tab of the [management portal], such as admin@contoso.onmicrosoft.com.

	![AAD users](./media/app-service-api-dotnet-add-authentication/aadusers.png)

	![Login page](./media/app-service-api-dotnet-add-authentication/ffsignin.png)

4. When the "login complete" message appears, enter the URL to your API app's Get method again.

	This time because you've authenticated, the call is successful. The gateway recognizes that you are an authenticated user and passes your request on to your API app.

	![Login completed](./media/app-service-api-dotnet-add-authentication/logincomplete.png)

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)
	<!--todo:replace with image showing fictional names-->

## Use Postman to send a Post request

When you log in to the gateway, the gateway sends back an authentication token.  This token must be included with all requests from external sources that go through the gateway. When you access an API with a browser, the browser typically stores the token in a cookie and sends it along with all subsequent calls to the API.

So you can see what is happening in the background, in this section you use a browser tool to create and submit a Post request, and you get the authorization token from the cookie and include it in an HTTP header.

These instructions show how to use the Postman tool in the Chrome browser, but you could do the same thing with any REST client tool and browser developer tools.

1. In a Chrome browser window, go through the steps shown in the previous section to authenticate, and then open developer tools (F12).

	![Go to Resources tab](./media/app-service-api-dotnet-add-authentication/resources.png)

3. In the **Resources** tab of Chrome developer tools, find the cookies for your gateway, and triple-click the Value of the **x-zumo-auth** cookie to select all of it.

	**Note:**  Make sure you get all of the cookie's value. If you double-click, you'll get only the first part of it.

5. Right-click the **Value** of the **x-zumo-auth** cookie, and then click **Copy**.

	![Copy auth token](./media/app-service-api-dotnet-add-authentication/copyzumotoken.png)

4. Install the Postman extension in your Chrome browser if you haven't done so yet.

6. Open the Postman extension.

7. Enter the Request URL.
 
		http://[resourcegroupname]gateway.azurewebsites.net/[apiappid]/api/contacts
    
8. Click **Headers**, and then add an *x-zumo-auth* header. Paste the token value from the clipboard into the **Value** field.

9. Add a *Content-Type* header with value *application/json*.

10. Click **form-data**, and then add a *contact* key with the following value:

		{   "Id": 0,   "Name": "Li Yan",   "EmailAddress": "yan@contoso.com" }

11. Click Send.

	![Add headers and body](./media/app-service-api-dotnet-add-authentication/addcontact.png)

	The API app returns a *201 Created* response.

12. To verify that this request would not work without the authentication token, delete the authentication header and click Send again.

	You get a *403 Forbidden* response.

	![403 Forbidden response](./media/app-service-api-dotnet-add-authentication/addcontact.png)

## Next steps

You've seen how to protect an Azure API app by requiring Azure Active Directory or social provider authentication. For more information about Azure API apps, see [What are API apps?](../app-service-api-apps-why-best-platform/). 

[management portal]: https://manage.windowsazure.com/
[preview portal]: https://portal.azure.com/

