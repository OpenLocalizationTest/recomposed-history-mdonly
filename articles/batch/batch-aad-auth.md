---
title: Use Azure Active Directory to authenticate Azure Batch service solutions | Microsoft Docs
description: Batch supports Azure AD for authentication from the Batch service.
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: ''
tags: 

ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
---

# Authenticate Batch service solutions with Active Directory

Azure Batch supports authentication with [Azure Active Directory][aad_about] (Azure AD) for the Batch service. Azure AD is Microsoft’s multi-tenant cloud based directory and identity management service. Azure itself uses Azure AD for the authentication of its customers, service administrators, and organizational users.

In this article, we explore using Azure AD to authenticate from applications that use the Batch .NET library. In the context of the Batch .NET APIs, we show how to use Azure AD to authenticate a subscription administrator or co-administrator, using integrated authentication. We also show how to create and use a service principal in an application that runs unattended.

## Azure AD and pool allocation mode

When you create a new Batch account, you can specify whether pools will be allocated in the default Batch service subscription, or in a subscription that you choose. Your choice affects how you authenticate requests made to resources in that account.

- **Batch service subscription**. By default, Batch pools are allocated in a Batch service subscription. If you choose this option, you can authenticate requests to resources in that account with either [Shared Key](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service) or with Azure AD.
- **User subscription.** You can also specify that Batch pools are allocated in a specified user subscription. If you choose this option, you must authenticate with Azure AD.

## Authenticate your Batch service solution using integrated authentication

If a user will interact with your Batch service solution while it is running, you'll need to authenticate that user. Azure AD provides integrated authentication to gather a user's credentials and acquire an authentication token for use with the Batch service.

The following sections describe how to configure your application for integrated authentication. 

## Register your application with Azure AD

The Azure [Active Directory Authentication Library][aad_adal] (ADAL) provides a programmatic interface to Azure AD for use within your applications. To call ADAL from your application, you must register your application in an Azure AD tenant. When you register your application, you supply Azure AD with information about your application, including a name for it within the Azure AD tenant. Azure AD then provides an application ID that you use to associate your application with Azure AD at runtime. To learn more about the application ID, see [Application and service principal objects in Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

To register your Batch application, follow the steps in the [Adding an Application](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) section in [Integrating applications with Azure Active Directory][aad_integrate]. For the **Redirect URI**, you can specify any valid URI. It does not need to be a real endpoint.

After you've registered your application, you'll see the application ID:

![Register your Batch application with Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

## Grant the Batch service API access to your application

Next, you'll need to delegate access to your application to the Batch service API. The Azure AD identifier for the Batch service API is **Microsoft Azure Batch (MicrosoftAzureBatch)**.

Follow these steps in the Azure portal:

1. In the left-hand navigation pane of the Azure portal, choose **More Services**, click **App Registrations**.
2. Search for the name of your application in the list of app registrations:

    ![Search for your application name](./media/batch-aad-auth/search-app-registration.png)

3. Display the **Settings** blade. In the **API Access** section, select **Required permissions**.
4. In the **Required permissions** blade, click the **Add** button.
5. In step 1, search for **MicrosoftAzureBatch**, select **Microsoft Azure Batch (MicrosoftAzureBatch)**, and click the **Select** button.
6. In step 2, select the check box next to **Access Azure Batch Service** and click the **Select** button.
7. Click the **Done** button.

The **Required Permissions** blade now shows that your Azure AD application grants access to both the Azure AD and Azure Batch APIs. 

![API permissions](./media/batch-aad-auth/required-permissions-data-plane.png)


### Endpoints for authentication with Azure AD

To authenticate your Batch Management solutions with Azure AD, you'll need two well-known endpoints.

- The **Azure AD common endpoint** provides a generic credential gathering interface when a specific tenant is not provided, as in the case of integrated authentication:

    `https://login.microsoftonline.com/common`

- ???The **Azure Batch resource endpoint** is used to acquire a token for authenticating requests to the Batch service:

    `https://batch.core.windows.net/`


## Authenticate an unattended application with an Azure AD service principal

If your Batch application runs unattended, you can use an Azure AD service principal to authenticate. A service principal defines the policy and permissions for an application, providing the basis for a security principal to represent the application when accessing resources at run-time. To learn more about service principals, see [Application and service principal objects in Azure Active Directory](../active-directory/develop/active-directory-application-objects.md). To create a service principal using the Azure portal, see [Use portal to create Active Directory application and service principal that can access resources](../resource-group-create-service-principal-portal.md). You can also create a service principal with PowerShell or Azure CLI. 

To configure your application to authenticate with a service principal, first follow the steps outlined in these sections above:

- [Grant the Batch service API access to your application](#grant-the-batch-service-api-access-to-your-application)
- [Register your application with Azure AD](#register-your-application-with-azure-ad)

Then follow the steps

### Grant your application access to Batch resources in your subscription

To grant access , follow these steps:

1. In the Azure portal, select **Subscriptions** to view your subscriptions.
2. Select the subscription that includes the Batch account used for your unattended application.
3. In the **Settings** blade for that subscription, select **Access Control (IAM)**.
4. Click the **Add** button. 
5. From the **Role** drop-down, choose either the _Contributor_ or _Reader_ role. Selecting a role grants your application access to the resources in your Batch account. For more information on these roles, see [Get started with Role-Based Access Control in the Azure portal](../active-directory/role-based-access-control-what-is.md).  
6. In the **Select** field, enter the name of your application. Select your application from the list, and click **Save**.

Your application should now appear in your access control settings with an RBAC role assigned. 

![Assign an RBAC role to your application](./media/batch-aad-auth/app-rbac-role.png)

### Get the tenant ID for your Azure Active Directory

To get the tenant ID, follow these steps:

1. In the Azure portal, select your Active Directory.
2. Click **Properties**.
3. Copy the GUID value provided for the directory ID. This value is also called the tenant ID.

![Copy the directory ID](./media/batch-aad-auth/aad-directory-id.png)



## Best practices for using Azure AD with Batch

An Azure AD authentication token expires after one hour. When using a long-lived **BatchClient** object, we recommend that you retrieve a token from ADAL on every request to ensure you always have a valid token. 

To achieve this in .NET, write a method that retrieves the token from Azure AD and pass that method to a **BatchTokenCredentials** object as a delegate. The delegate method is called on every request to the Batch service to ensure that a valid token is provided. By default ADAL caches tokens, so a new token is retrieved from Azure AD only when necessary. For an example, see [Code example: Using Azure AD with Batch .NET](#code-example-using-azure-ad-with-batch-net) in the next section. For more information about tokens in Azure AD, see [Authentication Scenarios for Azure AD][aad_auth_scenarios].


## Code examples

### Code example: Using Azure AD integrated authentication with Batch .NET

To write Batch .NET code that authenticates with Azure AD, reference the [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) package and the [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) package.

Include the following `using` statements in your code:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Reference the Azure AD common endpoint and the Azure AD endpoint for the Batch service in your code:  

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/common";
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Reference your Batch account endpoint:

```csharp
private const string BatchAccountEndpoint = "https://myaccount.westcentralus.batch.azure.com";
```

Specify the application ID (client ID) for your application. The application ID is available from your app registration in the Azure portal; see the section titled [Grant the Batch service API access to your application](#grant-the-batch-service-api-access-to-your-application) to retrieve it. 

```csharp
private const string ClientId = "<application-id>";
```

Also specify a redirect URI, which can be any valid URI.

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Write a callback method to acquire the authentication token from Azure AD. The **AcquireTokenAsync** method prompts the user for their credentials and uses those credentials to acquire a new token.

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Construct a **BatchTokenCredentials** object that takes the delegate as a parameter. Use those credentials to open a **BatchClient** object. You can then use that **BatchClient** object for subsequent operations against the Batch service.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountEndpoint, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

The **GetAuthenticationTokenAsync** callback method shown above uses Azure AD for integrated authentication of a user who is interacting with the application. The call to the **AcquireTokenAsync** method prompts the user for their credentials, and the application proceeds once the user provides them.


### Code example: Using an Azure AD service principal with Batch .NET




## Next steps

For more information on running the [AccountManagement sample application][acct_mgmt_sample], see [Manage Batch accounts and quotas with the Batch Management client library for .NET](batch-management-dotnet.md).

To learn more about Azure AD, see the [Azure Active Directory Documentation](https://docs.microsoft.com/azure/active-directory/). In-depth examples showing how to use ADAL are available in the [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=active-directory) library.


[aad_about]: ../active-directory/active-directory-whatis.md "What is Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Authentication Scenarios for Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrating Applications with Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
