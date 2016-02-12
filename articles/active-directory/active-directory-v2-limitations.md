<properties
	pageTitle="v2.0 Endpoint Limitations & restrictions | Microsoft Azure"
	description="A list of limitations & restrictions with the Azure AD v2.0 endpoint."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="dastrock"/>

# Should I use the v2.0 endpoint? 

When building applications that integrate with Azure Active Directory, you will need to decide if the v2.0 endpoint and authentication protocols will meet your needs.  The original Azure AD app model is still fully supported and in some respects, is more feature rich than v2.0.  However, the v2.0 endpoint [introduce significant benefits](active-directory-v2-compare.md) for developers that may entice you to use the new programming model.  Over time, v2.0 will grow to encompass all Azure AD features, so that you will only ever need to create the v2.0 endpoint.

In the meantime, this article is intended to help you determine if the v2.0 endpoint is for you.  Our recommenedation is to use v2.0 unless something mentioned here prevents you from doing so.  We will continue to update this article over time to reflect the current state of the the v2.0 endpoint, so check back to re-evaluate your requirements against the v2.0 capabilities.

If you have an existing app with Azure AD that does not use the v2.0 endpoint, there's no need to start from scratch.  In the near future we will be providing a way for you to enable your exisiting Azure AD applications for use with the v2.0 endpoint.

## Restrictions on apps
The following types of apps are not currently supported by the v2.0 endpoint.  For a description of the supported types of apps, refer to [this article](active-directory-v2-flows.md).

##### Standalone web APIs
With the v2.0 endpoint, you have the ability to [build a Web API that is secured using OAuth tokens](active-directory-v2-flows.md#web-apis) from the v2.0 endpoint.  However, that Web API will only be able to receive tokens from a client that shares the same Application Id.  Building a third party web service that is accessed from several different clients is not supported.  3rd party clients will not be able to request or obtain permissions to your web API.

To see how to build a Web API that accepts tokens from a well-known client with the same App Id, see the v2.0 endpoint Web API samples in our [Getting Started](active-directory-appmodel-v2-overview.md#getting-started) section.

##### Daemons/Server Side Apps
Apps that contain long running processes or that operate without the presence of a user also need a way to access secured resources, such as Web APIs.  These apps can authenticate and get tokens using the app's identity (rather than a user's delegated identity) using the OAuth 2.0 client credentials flow.  

This flow is not currently supported by the v2.0 endpoint - which is to say that apps can only get tokens after an interactive user sign-in flow has occurred.  The client credentials flow will be added in the near future.  If you would like to see the client credentials flow in the generally available Azure AD app model, check out the [Daemon sample on GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

##### Chained web APIs (on-behalf-of)
Many architectures include a Web API that needs to call another downstream Web API, both secured by the v2.0 endpoint.  This scenario is common in native clients that have a Web API backend, which in turn calls a Microsoft Online service such as Office 365 or the Graph API.

This chained Web API scenario can be supported using the OAuth 2.0 Jwt Bearer Credential grant, otherwise known as the On-Behalf-Of Flow.  However, the On-Behalf-Of flow is not currently supported for the v2.0 endpoint.  To see how this flow works in the generally available Azure AD service, check out the [On-Behalf-Of code sample on GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## Restrictions on users
At this moment, every app using the v2.0 endpoint will be publicly exposed to all users with a Microsoft Account or Azure AD account. Any user with either type of account will be able to successfully navigate to or install your app, enter their credentials, and consent to your app's permissions.  You can write your app's code to reject sign-ins from certain sets of users - but that will not prevent them from being able to consent to the app.

Effectively, your apps can not restrict the types of users that can sign into the app.  You will not be able to build Line of Business apps (restricted to users in one organization), apps available to only enterprise users (with an Azure AD account), or apps only available to consumers (with a Microsoft Account).

## Restrictions on app registrations
At this point in time, all apps that want to integrate with the v2.0 endpoint must create a new app registration at [apps.dev.microsoft.com](https://apps.dev.microsoft.com).  Any existing Azure AD or Microsoft Account apps will not be compatible with the v2.0 endpoint, nor will apps registered in any portal besides the new App Registration Portal.  We plan to provide a way to enable existing applications for use as a v2.0 app, but at this time, there is no migration path for an app to the v2.0 endpoint.

Similarly, apps registered in the new App Registration Portal will work exclusively with the v2.0 endpoint.  You can not use the App Registration Portal to create apps that will integrate successfully with the Azure AD or Microsoft Account services.

Apps that are registered in the new Application Registration Portal are currently restricted to a limited set of redirect_uri values.  The redirect_uri for web apps and services must begin with the scheme or `https`, while the redirect_uri for all other platforms must use the hard-coded value of `urn:ietf:oauth:2.0:oob`.

To learn how to register an app in the new Application Registration Portal, refer to [this article](active-directory-v2-app-registration.md).

## Restrictions on services & APIs
The v2.0 endpoint currently supports sign-in for any app registered in the new Application Registration Portal, provided it falls into the list of [supported authentication flows](active-directory-v2-flows.md).  However, these apps will only be able to acquire OAuth 2.0 access tokens for a very limited set of resources.  The v2.0 endpoint will only issue access_tokens for:

- The app that requested the token.  An app can acquire an access_token for itself, if the logical app is comprised of several different components or tiers.  To see this scenario in action, check out our [Getting Started](active-directory-appmodel-v2-overview.md#getting-started) tutorials.
- The Outlook Mail, Calendar and Contacts REST APIs, all of which are located at https://outlook.office.com.  To learn how to write an app that accesses these APIs, refer to these [Office Getting Started](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) tutorials.
- The Microsoft Graph APIs.  To learn about the Microsoft Graph and all the data that is available, visit [https://graph.microsoft.io](https://graph.microsoft.io).

More Microsoft Online services will be added in the future, as well as support for your own Web APIs and services.

## Restrictions on libraries & SDKs
Not all languages and platforms have libraries that support the v2.0 endpoint.  The set of authentication libraries is currently limited to .NET, iOS, Android, NodeJS, and Javascript.  Corresponding code samples and tutorials for each are available in our [Getting Started](active-directory-appmodel-v2-overview.md#getting-started) section.  Note that in most cases these v2.0 compatibile libraries are in a preview state - they are fully supported but not necessarily cast in stone.  Their APIs and implementations are subject to change at any point in time. 

We still reccomend using our authentication libraries for building the v2.0 endpoint, despite their preview state.  But if you would prefer not to use the libraries or are building your application in a language not in the above list, you have two alternatives.  You can craft and send HTTP messages yourself, using the [OAuth 2.0 and OpenID Connect Protocol Reference](active-directory-v2-protocols.md) as a guide.  Or, you can choose from one of the many open source authentication libraries that are likely available for your platform of choice.  The v2.0 protocol reference will also be helpful in using/adapting open source libraries for use in the v2.0 endpoint.

## Restrictions on protocols
The v2.0 endpoint only supports Open ID Connect & OAuth 2.0.  However, not all features and capabilities of each protocol have been incorporated into the v2.0 endpoint.  Some examples include:

- The OpenID Connect `end_sesssion_endpoint`

To better understand the scope of protocol functionality supported in the v2.0 endpoint, read through our [OpenID Connect & OAuth 2.0 Protocol Reference](active-directory-v2-protocols.md).

## Advanced Azure AD developer features
There is a set of developer features available in the Azure Active Directory service that are not yet supported for the v2.0 endpoint, including:

- Group claims for Azure AD users
- Application Roles & Role Claims
- Conditional Access Policies