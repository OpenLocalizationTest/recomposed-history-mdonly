<properties
	pageTitle="How to protect a Web API backend with Azure Active Directory and API Management"
	description="Learn how to protect a Web API backend with Azure Active Directory and API Management." 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2015"
	ms.author="sdanie"/>

# How to protect a Web API backed with Azure Active Directory and API Management

The following video shows how to build a Web API backend and protect it using OAuth 2.0 protocol with Azure Active Directory and API Management.  It showsThis article provides an overview and additional information for the steps in the video. This 24 minute video shows you how to:

-	Build a Web API backend and secure it with AAD
-	Import the API into API Management
-	Configure the Developer portal to call the API
-	Configure a desktop application to call the API
-	COnfigure a JWT validation policy to pre-authorize requests

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## Create an Azure AD directory

To secure your Web API backed using Azure Active Directory you must first have a an AAD tenant. In this video a tenant named **APIMDemo** is used. To create an AAD tenant, sign-in to the [Azure portal](https://manage.windowsazure.com) and click **New**, **App Services**, **Active Directory**, **Directory**, **Custom Create**. 

![Azure Active Directory][api-management-create-aad-menu]

In this example a directory named **APIMDemo** is created with a default domain named **DemoAPIM.onmicrosoft.com**. This directory is throughout the video.

![Azure Active Directory][api-management-create-aad]

## Create a Web API service secured by Azure Active Directory

>[AZURE.NOTE] Explain why we used a Web App, due to the auto configuration of AAD, and how this isn't supported right now in API App. If I can find it, link out to documentation that explains how to add the ADD after the fact ot projects that were already created.

In this step, a Web API backend is created using Visual Studio 2013. This step of the video starts at 1:30. To create Web API backend project in Visual Studio click **File**, **New**, **Project**, and choose **ASP.NET Web Application** from the **Web** templates list. In this video the project is named **APIMAADDemo**. Click **OK** to create the project. 

![Visual Studio][api-management-new-web-app]

Click **Web API** from the **Select a template list** to create a Web API project. To configure Azure Directory Authentication click **Change Authentication**.

![New project][api-management-new-project]

Click **Organizational Accounts**, and specify the **Domain** of your AAD tenant. In this example the domain is **DemoAPIM.onmicrosoft.com**. The domain of your directory can be obtained from the **domains** tab of your directory.

![Domains][api-management-aad-domains]

Configure the desired settings in the **Change Authentication** dialog box and click **OK**.

![Change authentication][api-management-change-authentication]

When you click **OK** Visual Studio will attempt to register your application with your Azure AD directory and you may be prompted to sign in by Visual Studio. Sign in using an administrative account of your directory.

![Sign in to Visual Studio][api-management-sign-in-vidual-studio]

To configure this project as an Azure Web API check the box for **Host in the cloud** and then click **OK**.

![New project][api-management-new-project-cloud]

You may be prompted to sign in to Azure, and then you can configure the Web App.

![Configure][api-management-configure-web-app]

In this example a new **App Service plan** named **APIMAADDemo** is specified.

Click **OK** to configure the Web App and create the project.

## Add the code to the Web API project

The Web API in this example implements a basic calculator service using a model and a controller. To add the model for the service, right-click **Models** in **Solution Explorer** and choose **Add**, **Class**. Name the class `CalcInput` and click **Add**.

Add the following `using` statement to the top of the `CalcInput.cs` file.

	using Newtonsoft.Json;

 Replace the generated class with the following code.

    public class CalcInput
    {
        // TODO
    }

Right-click **Controllers** in **Solution Explorer** and choose **Add**, **Controller**. Choose **Web API Controller - Empty** and click **Add**. Type **CalcController** for the Controller name and click **Add**.

![Add Controller][api-management-add-controller]

Add the following `using` statement to the top of the `CalcController.cs` file.

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Replace the generated controller class with the following code. This code implements the `Add`, `Subtract`, `Multiply`, and `Divide` operations of the Basic Calculator API.

    public class CalcController : ApiController
    {
    }

Press **F6** to build and verify the solution.

## Publish the project to Azure

5:45

Right-click the **APIMAADDemo** project in Visual Studio and choose **Publish**. Keep the default settings in the **Publish Web** dialog box and click **Publish**.

![Web Publish][api-management-web-publish]

## Grant permissions to the Azure AD backend service application

A new application for the backend service is created in your Azure AD directory as part of the configuring and publishing process of your Web API project.

![Application][api-management-aad-backend-app]

Click the name of the application to configure the required permissions. Navigate to the **Configure** tab and scroll down to the **permissions to other applications** section. Click the **Application Permissions** drop-down beside **Windows** **Azure Active Directory**, check the box for **Read directory data**, and click **Save**.

![Add permissions][api-management-aad-add-permissions]

>[AZURE.NOTE] If **Windows** **Azure Active Directory** is not listed under permissions to other applications, click **Add application** and add it from the list.

Stopping at 7:00

## Import the Web API into API Management

APIs are configured from the API publisher portal, which is accessed through the Azure management portal. To reach the publisher portal, click **Manage** in the Azure Portal for your API Management service. If you have not yet created an API Management service instance, see [Create an API Management service instance][] in the [Manage your first API][] tutorial.

![Publisher portal][api-management-management-console]

To import the calculator API, click **APIs** from the **API Management** menu on the left, and then click **Import API**.

![Import API button][api-management-import-api]

![Add new API][api-management-import-new-api]

Perform the following steps to configure the calculator API.

1. Click **From URL**, enter **http://calcapi.cloudapp.net/calcapi.json** into the **Specification document URL** textbox, and click the **Swagger** radio button.
2. Type **calc** into the **Web API URL suffix** textbox.
3. Click in the **Products (optional)** box and choose **Starter**.
4. Click **Save** to import the API.

Once the API is imported, the summary page for the API is displayed in the publisher portal.

![API summary][api-management-imported-api-summary]

## Configure an API Management OAuth 2.0 server

Security->OAuth 2.0, add server
Name
Client registration URL - placeholder
Authorization code grant type (default)
Authorization end point - get it from the AD Application

## Configure the developer portal to call the API

Does this step need to go before configuring the OAuth 2.0 server? Or did I miss that in the video?

Add application, web application, APIMDeveloperPortal. Signon-url (where did they get that), 2nd URL, made up.

## Configure a desktop application to call the API

## Configure a JWT validation policy to pre-authorize requests

API Management supports multi-region deployment which enables API publishers to distribute a single API management service across any number of desired Azure regions. This helps reduce request latency perceived by geographically distributed API consumers and also improves service availability if one region goes offline. 

When an API Management service is created initially, it contains only one [unit][] and resides in a single Azure region, which is designated as the Primary Region. Additional regions can be easily added through Azure Portal. API Management proxy server is deployed to each region and call traffic will be routed to the closest proxy. When a region goes offline, the traffic is automatically re-directed to the next closest proxy. 

> [AZURE.IMPORTANT] Multi-region deployment is only available in the **[Premium][]** tier.

## <a name="add-region"> </a>Deploy an API Management service instance to a new region






[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-howto-protect-backend-with-aad/api-management-imported-api-summary.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[]: ./media/api-management-howto-protect-backend-with-aad/.png
[]: ./media/api-management-howto-protect-backend-with-aad/.png
[]: ./media/api-management-howto-protect-backend-with-aad/.png
[]: ./media/api-management-howto-protect-backend-with-aad/.png
[]: ./media/api-management-howto-protect-backend-with-aad/.png
[]: ./media/api-management-howto-protect-backend-with-aad/.png
[]: ./media/api-management-howto-protect-backend-with-aad/.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Manage your first API]: api-management-get-started.md
