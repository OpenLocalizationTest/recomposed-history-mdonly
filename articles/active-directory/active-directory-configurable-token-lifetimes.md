<properties
   pageTitle="Configurable Token Lifetimes"
   description="This topic describes the accounts used and created and permissions required."
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/04/2016"
   ms.author="billmath"/>


# Configurable Token Lifetimes (Preview)

## Introduction
This feature is used by admins and developers to specify the lifetimes of tokens issued by Azure AD. Token lifetimes can be configured for all apps in a tenant, for a multi-tenant application, or for a specific Service Principal in a tenant.

In Azure AD, a policy object represents a set of rules enforced on individual applications or all applications in a tenant.  Each type of policy has a unique structure with a set of properties that are then applied to objects to which they are assigned.
A policy can be designated as the default for a tenant. This policy then takes effect on any application that resides within that tenant as long as it is not overridden by a policy with a higher priority. Policies can also be assigned to specific applications. The order of priority varies by policy type.

Token lifetime policies can be configured for access tokens, refresh tokens, and ID tokens.

### Access Tokens
An access token is used by a client to access a protected resource. An access token can only be used for a specific combination of user, client and resource. Access tokens cannot be revoked and are valid until their expiry. A malicious actor that has obtained an access token can use it for extent of its lifetime.  Adjusting access token lifetime is a tradeoff between improving system performance by reducing the number of times a client needs to acquire a fresh access token, and increasing the amount of time that the client retains access after the user’s account is disabled.

### Refresh Tokens
When a client acquires an access token to access a protected resource, it receives both a refresh token and an access token. The refresh token is used to obtain new access/refresh token pairs when the current access token expires. Refresh tokens are bound to combinations of user and client. They can be revoked and their validity is checked every time they are used. 
Policies can be set on resources to prevent refresh tokens older than a specified period from obtaining a new access/refresh token pair (Refresh Token Max Inactive Time). Additionally, policies can be used to set a period of time after the first refresh token was issued beyond which the refresh tokens are no longer accepted (Refresh Token Max Age).  Adjusting refresh token lifetime allows you to control when and how often the user is required to re-enter credentials instead of being silently re-authenticated when using a native client application.

### ID Tokens
ID tokens are passed to web sites and native clients and contain profile information about a user. An ID token is bound to a specific combination of user and client. ID tokens are considered valid until expiry.  Normally, a web application will match a user’s session lifetime in the application to the lifetime of the ID token issued for the user.  Adjusting ID token lifetime allows you to control how often the web application will expire the application session and require the user to be re-authenticated with Azure AD (either silently or interactively).

### Single Sign On Session Token
When a user authenticates with Azure AD, a single sign on session is established with the user’s browser and Azure AD.  The Single Sign On Session Token, in the form of a cookie, represents this session. It is important to note that the SSO session token is not bound to a specific resource/client application. SSO session tokens can be revoked and their validity is checked every time they are used.

There are two kinds of SSO session tokens. Persistent session tokens are stored as persistent cookies by the browser and non-persistent session tokens are stored as session cookies (these are destroyed when the browser is closed).
Non-persistent session tokens have a lifetime of 24 hours whereas persistent tokens have a lifetime of 180 days. Any time the SSO session token is used within its validity period, the validity period is extended another 24 hours or 180 days. If the SSO session token is not used within its validity period, it is considered expired and will no longer be accepted. 

Policies can be used to set a period of time after the first session token was issued beyond which the session tokens are no longer accepted (Session Token Max Age). 

### Token Lifetime Policy Properties
A token lifetime policy is a type of policy object that contains token lifetime rules.  The properties of the policy are used to control specified token lifetimes.  If no policy is set, the system enforces the default lifetime value.


### Configurable token lifetime properties
Property|Policy property string|Affects|Default|Minimum|Maximum|
----- | ----- | ----- |----- | ----- | ----- |
Access Token Lifetime|	AccessTokenLifetime|Access tokens, ID tokens, SAML2 tokens|1 hour|10 minutes|1 day|
Refresh Token Max Inactive Time|	MaxInactiveTime	|Refresh tokens	|14 days|10 minutes|	90 days|
Single-Factor Refresh Token Max Age|	MaxAgeSingleFactor	|Refresh tokens (for any users)	|90 days|10 minutes	|Until-revoked*|
Multi-Factor Refresh Token Max Age|	MaxAgeMultiFactor|	Refresh tokens (for any users)|	90 days|10 minutes|Until-revoked*|
Single-Factor Session Token Max Age	|MaxAgeSessionSingleFactor**	|Session tokens(persistent and non-persistent)|	Until-revoked	|10 minutes	|Until-revoked*|
Multi-Factor Session Token Max Age|	MaxAgeSessionMultiFactor***|	Session tokens (persistent and non-persistent)|	Until-revoked|	10 minutes|	Until-revoked*



- *365 days is the maximum explicit length that can be set for these attributes.
- **If MaxAgeSessionSingleFactor is not set then this value takes the MaxAgeSingleFactor value. If neither parameter is set, the property takes on the default value (Until-revoked).
- ***If MaxAgeSessionMultiFactor is not set then this value takes the MaxAgeMultiFactor value. If neither parameter is set, the property takes on the default value (Until-revoked).

### Exceptions
Property|Affects|Default|
----- | ----- | ----- |
Refresh Token Max Inactive Time (federated users with insufficient revocation information)|Refresh tokens (Issued for federated users with insufficient revocation information)|12 hours|
Refresh Token Max Inactive Time (Confidential Clients)|	Refresh tokens (Issued for Confidential Clients)|90 days|Refresh token Max Age (Issued for Confidential Clients) |	Refresh tokens (Issued for Confidential Clients) |Until-revoked### PriorityToken Lifetime policies can be created and assigned to specific applications, tenants and service principals. This means that it is possible for multiple policies to apply to a specific application. The Token Lifetime policy that takes effect follows these rules:

- If a policy is explicitly assigned to the service principal, it will be enforced. 
- If no policy is explicitly assigned to the service principal, a policy explicitly assigned to the parent tenant of the service principal will be enforced. 
- If no policy is explicitly assigned to the service principal or the tenant, the policy assigned to the application will be enforced. 
- If no policy has been assigned to the service principal, the tenant, or the application object, the default values will be enforced (see table above).For more information on the relationship between application objects and service principal objects in Azure AD, see (https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-objects/).### EvaluationTokens are always evaluated based on the policies with the highest priority set on the resource being accessed. This means that although a specific refresh token may still be considered “valid” for a client accessing a specific resource, a user using the same refresh token may have to get a new one when attempting to access a different resource (with a stricter policy). Similarly, the same session token may be used to retrieve an ID token for a specific client application but may not retrieve one for another client application with stricter policies.## Configurable Policy Properties: In Depth### Access Token Lifetime**Affects:** Access tokens, ID tokens**Summary:** This policy controls how long access and ID tokens for this resource are considered valid. Reducing the access token lifetime mitigates the risk of an access or ID token being used by a malicious actor for an extended period of time (as they cannot be revoked) but also adversely impacts performance as the tokens will have to be replaced more often.### Refresh Token Max Inactive Time**Affects:** Refresh tokens**Summary:** This policy controls how old a refresh token can be before a client can no longer use it to retrieve a new access/refresh token pair when attempting to access this resource. Since a new Refresh token is usually returned a refresh token is used, the client must not have reached out to any resource using the current refresh token for the specified period of time before this policy would prevent access. This policy will force users who have not been active on their client to re-authenticate to retrieve a new refresh token. ### Single-Factor Refresh Token Max Age**Affects:** Refresh tokens**Summary:** This policy controls how long a user can continue to use refresh tokens to get new access/refresh token pairs after the last time they authenticated successfully with only a single factor. Once a user authenticates and receives a new refresh token, they will be able to use the refresh token flow (as long as the current refresh token is not revoked and it is not left unused for longer than the inactive time) for the specified period of time. At that point, users will be forced to re-authenticate to receive a new refresh token. Reducing the max age will force users to authenticate more often. Since single-factor authentication is considered less secure than a multi-factor authentication, it is recommended that this policy is set to an equal or lesser value than the Multi-Factor Refresh Token Max Age Policy.### Multi-Factor Refresh Token Max Age**Affects:** Refresh tokens**Summary:** This policy controls how long a user can continue to use refresh tokens to get new access/refresh token pairs after the last time they authenticated successfully with multiple factors. Once a user authenticates and receives a new refresh token, they will be able to use the refresh token flow (as long as the current refresh token is not revoked and it is not left unused for longer than the inactive time) for the specified period of time. At that point, users will be forced to re-authenticate to receive a new refresh token. Reducing the max age will force users to authenticate more often. Since single-factor authentication is considered less secure than a multi-factor authentication, it is recommended that this policy is set to an equal or greater value than the Single-Factor Refresh Token Max Age Policy.### Single-Factor Session Token Max Age**Affects:** Session tokens (persistent and non-persistent)**Summary:** This policy controls how long a user can continue to use session tokens to get new ID and session tokens after the last time they authenticated successfully with only a single factor. Once a user authenticates and receives a new session token, they will be able to use the session token flow (as long as the current session token is not revoked or expired) for the specified period of time. At that point, users will be forced to re-authenticate to receive a new session token. Reducing the max age will force users to authenticate more often. Since single-factor authentication is considered less secure than a multi-factor authentication, it is recommended that this policy is set to an equal or lesser value than the Multi-Factor Session Token Max Age Policy.### Multi-Factor Session Token Max Age**Affects:** Session tokens (persistent and non-persistent)**Summary:** This policy controls how long a user can continue to use session tokens to get new ID and session tokens after the last time they authenticated successfully with multiple factors. Once a user authenticates and receives a new session token, they will be able to use the session token flow (as long as the current session token is not revoked or expired) for the specified period of time. At that point, users will be forced to re-authenticate to receive a new session token. Reducing the max age will force users to authenticate more often. Since single-factor authentication is considered less secure than a multi-factor authentication, it is recommended that this policy is set to an equal or greater value than the Single-Factor Session Token Max Age Policy.## Sample Token Lifetime PoliciesBeing able to create and manage token lifetimes for apps, service principals, and your overall tenant exposes all kinds of new scenarios possible in Azure AD.  We're going to walk through a few common policy scenarios that will help you impose new rules for:

- Token Lifetimes
- Token Max Inactive Times
- Token Max AgeWe'll walk through a few scenarios including:

- Managing a Tenant's Default Policy
- Creating a Policy for Web Sign in
- Creating a Policy for Native App calling a Web API
- Managing an Advanced Policy 

### PrerequisitesIn the sample scenarios we'll be creating, updating, linking, and deleting policies on apps, service principals, and your overall tenant.  If you are new to Azure AD, checkout [this article](active-directory-howto-tenant.md) to help you get started before proceeding with these samples.  

1. To begin, download the latest [Azure AD PowerShell Cmdlet Preview](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.2). 2.	Once you have the Azure AD PowerShell Cmdlets, run Connect command to sign into your Azure AD admin account. You'll need to do this whenever you start a new session.                Connect-AzureAD -Confirm3.	You'll also need a way to access the **Object ID** of your apps and service principals.  For applications, we recommend using the [Azure Portal](https://portal.azure.com/).  For Service Principals, you can query the [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) or go to our [Graph Explorer Tool](https://graphexplorer.cloudapp.net/) and sign into your Azure AD account to see all your tenant's service principals.4.	Run the following command to see all policies that have been created in your tenant.  This command should be used after most operations in the following scenarios.  It will also help you get the **Object ID** of your policies.                 Get-AzureADPolicy### Scenario: Managing a Tenant's Default PolicyIn this scenario, we will manage a Token Lifetime policy for Access/Id and Single-Factor Refresh Tokens that is applied across your tenant. This policy will be applied to every application in your tenant, and each service principal that doesn’t already have a policy set to it. 1.	**Create a Token Lifetime Policy.**  This policy will intrinsically be less secure, but will allow your users to sign in less often.  We set the Access/Id Token lifetime to 12 hours, and the Single-Factor Refresh Token to "until-revoked" meaning it won't expire unless an admin revokes it or a password change.		        PS> New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"12:00:00`",`"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName TenantDefaultPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy		To see your new policy and get it's ObjectID, run the following command.        Get-AzureADPolicy&nbsp;&nbsp;2.	**Update the Policy**You've decided that the first policy is not quite as secure as your service requires, and have decided you want your Single-Factor Refresh Tokens tp expire in 2 days. Run the following command. 		        PS> Set-AzureADPolicy -ObjectId <ObjectID FROM GET COMMAND> -DisplayName TenantDefaultPolicyUpdatedScenario -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"12:00:00`",`"MaxAgeSingleFactor`":`"2.00:00:00`"}}")		&nbsp;&nbsp;3. **You're done!** Run an app or service principal in your tenant to see the changes. ### Scenario: Creating a Policy for Web Sign inIn this scenario, we will create and assign a policy to the service principal of a web app.  This policy will set the lifetime of the Access/Id Tokens and the Max Age of a Multi-Factor Session Token.  1.	**Create a Token Lifetime Policy.**This policy for Web Sign in will set the Access/Id Token lifetime and the Max Single-Factor Session Token Age to 2 hours.        PS> New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"02:00:00`",`"MaxAgeSessionSingleFactor`":`"02:00:00`"}}") -DisplayName WebPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicyTo see your new policy and get it's ObjectID, run the following command.        Get-AzureADPolicy&nbsp;&nbsp;2.	**Assign the policy to your service principal.**We're going to link this new policy with the a service principal. Run the following command.                PS> Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>&nbsp;&nbsp;3.	**You're Done!** Run the service principal in your tenant to see the changes.  ### Scenario: Creating a Policy for Native App calling a Web APIIn this scenario, we will create and assign a policy to a Web API that is being called by a Native App.  This policy will improve the app's security by imposing a strict set of rules. The policy will be applied to the Web API, that way when the Native App requests it as a resource this policy will be applied. 1.	**Create a Token Lifetime Policy.** This command will create a strict policy for a Web API                 PS> New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"00:15:00`",`"MaxInactiveTime`":`"00:35:00`",`"MaxAgeMultiFactor`":`"06:00:00`",`"MaxAgeSingleFactor`":`"01:00:00`"}}") -DisplayName WebApiDefaultPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy         To see your new policy and get it's ObjectID, run the following command.        Get-AzureADPolicy&nbsp;&nbsp;2.	**Assign the policy to your Web API**.We're going to link this new policy with the a service principal. Run the following command.      PS> Add-AzureADApplicationPolicy -ObjectId <ObjectID of the App> -RefObjectId <ObjectId of the Policy>&nbsp;&nbsp;3.	**You're Done!** Run a Native app that accesses your Web API to see the changes. ### Scenario: Managing an Advanced Policy 

In this scenario, we will create and manage a policy that is applied to multiple objects.  This will give some insight into the priority of policies explained above, and will also help you manage more complicated scenarios.  While the policy will be a simple ruleset, the entities it's applied to is not. 

1.	**Create a Token Lifetime Policy**

So far pretty simple. We've created a tenant default policy that sets the Access/Id Token lifetime to 12 hours. 

        PS> New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"12:00:00`"}}") -DisplayName ComplexPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy

To see your new policy and get it's ObjectID, run the following command.
 
        Get-AzureADPolicy

&nbsp;&nbsp;2.	**Assign the Policy to a Service Principal**

Now we have a policy on the entire tenant.  Let's say we want to preserve this policy on a specific Service Principal, but update it for the entire tenant. 

        PS> Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.	**Set the IsTenantDefault flag to false using the following command**. 

        Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName ComplexPolicyScenario -IsTenantDefault $false
&nbsp;&nbsp;4.	**Create a new Tenant Default Policy**

        PS> New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"6:00:00`"}}") -DisplayName ComplexPolicyScenarioTwo -IsTenantDefault $true -Type TokenLifetimePolicy

&nbsp;&nbsp;5.	 **You're Done!** 

You now have the original policy linked to your service principal and the new policy set as your tenant default policy.  It's important to remember that policies applied to service principals have priority over tenant default policies. 


## Cmdlet Reference

### Manage Policies
The following cmdlets can be used to manage policies.

#### New-AzureADPolicy
Creates a new policy.

        PS > New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsTenantDefault <boolean> -Type <Policy Type> 

Parameters|Description|Example|
-----| ----- |-----|
-Definition| The array of strinfied JSON that contains all the rules of the policy.|-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") 
-DisplayName|String of the policy name|-DisplayName MyTokenPolicy
-IsTenantDefault|If true sets the policy as tenant's default policy, if false does nothing|-IsTenantDefault $true
-Type|The type of policy, for token lifetimes always use "TokenLifetimePolicy"|-Type TokenLifetimePolicy
-AlternativeIdentifier [Optional]|Sets an alternative id to the policy.|-AlternativeIdentifier myAltId


#### Get-AzureADPolicy         
Gets all AzureAD Policies or specified policy 
        
        PS > Get-AzureADPolicy 

Parameters|Description|Example|
-----| ----- |-----|
-ObjectId [Optional]|The object Id of the Policy you would like to get. |-ObjectId &lt;ObjectID of Policy&gt; 

#### Get-AzureADPolicyAppliedObject         
Gets all apps and service principals linked to a policy
        
        PS > Get-AzureADPolicyAppliedObject -ObjectId <object id of policy> 

Parameters|Description|Example|
-----| ----- |-----|
-ObjectId|The object Id of the Policy you would like to get.|-ObjectId &lt;ObjectID of Policy&gt;

#### Set-AzureADPolicy
Updates an existing policy
        
        PS> Set-AzureADPolicy -ObjectId <object id of policy> -DisplayName <string> 
 
Parameters|Description|Example|
-----| ----- |-----|
-ObjectId|The object Id of the Policy you would like to get.|-ObjectId &lt;ObjectID of Policy&gt;
-DisplayName|String of the policy name|-DisplayName MyTokenPolicy
-Definition [Optional]|The array of strinfied JSON that contains all the rules of the policy.|-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") 
-IsTenantDefault [Optional]|If true sets the policy as tenant's default policy, if false does nothing|-IsTenantDefault $true
-Type [Optional]|The type of policy, for token lifetimes always use "TokenLifetimePolicy"|-Type TokenLifetimePolicy
-AlternativeIdentifier [Optional]|Sets an alternative id to the policy.|-AlternativeIdentifier myAltId

#### Remove-AzureADPolicy         
Deletes the specified policy

        PS> Remove-AzureADPolicy -ObjectId <object id of policy>

Parameters|Description|Example|
-----| ----- |-----|
-ObjectId|The object Id of the Policy you would like to get.|-ObjectId &lt;ObjectID of Policy&gt;

### Application Policies

#### Assign-AzureADApplicationPolicy         
Links the specified policy to an application

        PS > Add-AzureADApplicationPolicy -ObjectId <object id of application> -RefObjectId <object id of policy>

Parameters|Description|Example|
-----| ----- |-----|
-ObjectId|The object Id of the Application.|-ObjectId &lt;ObjectID of Application&gt; 
-RefObjectId|The object Id of the Policy. |-RefObjectId &lt;ObjectID of Policy&gt;

#### Get-AzureADApplicationPolicy        
Gets the policy assigned to an application

        PS > Get-AzureADApplicationPolicy -ObjectId <object id of application>

Parameters|Description|Example|
-----| ----- |-----|
-ObjectId|The object Id of the Application.|-ObjectId &lt;ObjectID of Application&gt; 

#### Remove-AzureADApplicationPolicy        
Removes a policy from an application

       PS > Remove-AzureADApplicationPolicy -ObjectId <object id of application> -PolicyId <object id of policy>

Parameters|Description|Example|
-----| ----- |-----|
-ObjectId|The object Id of the Application.|-ObjectId &lt;ObjectID of Application&gt; 
-PolicyId1| The ObjectId of Policy.|-PolicyId &lt;ObjectID of Policy&gt;

### Service Principal Policies

#### Assign-AzureADServicePrinipalPolicy         
Links the specified policy to a service principal

        PS > Add-AzureADServicePrincipalPolicy -ObjectId <object id of service principal> -RefObjectId <object id of policy>

Parameters|Description|Example|
-----| ----- |-----|
-ObjectId|The object Id of the Application.|-ObjectId &lt;ObjectID of Application&gt; 
-RefObjectId|The object Id of the Policy. |-RefObjectId &lt;ObjectID of Policy&gt;

#### Get-AzureADServicePrinipalPolicy        
Gets any policy linked to the specified service principal

        PS > Get-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>
 
Parameters|Description|Example|
-----| ----- |-----|
-ObjectId|The object Id of the Application.|-ObjectId &lt;ObjectID of Application&gt; 

#### Remove-AzureADServicePrinipalPolicy         
Removes the policy from specified service principal

        PS > Remove-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>  -PolicyId <object id of policy>
 
Parameters|Description|Example|
-----| ----- |-----|
-ObjectId|The object Id of the Application.|-ObjectId &lt;ObjectID of Application&gt; 
-PolicyId1| The ObjectId of Policy.|-PolicyId &lt;ObjectID of Policy&gt;

