---
title: Azure AD v2 JS SPA Guided Setup - Configure | Microsoft Docs
description: How JavaScript SPA applications can call an API that require access tokens by Azure Active Directory v2 endpoint
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: ''

ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
---

### Create an application (Express)
Now you need to register your application in Microsoft Application Registration Portal:

1.	Register your application via [Microsoft Application Registration Portal](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.	Enter Application Name and your email
3.	Make sure the option to Guided Setup is checked
4.	Follow the instructions to obtain the `msalconfig.js` code containing the application Id.

### Add your application registration information to your solution (Advanced)

1.	Go to [Microsoft Application Registration Portal](https://apps.dev.microsoft.com/portal/register-app)
2.	Enter Application Name and your email
3.	Make sure the option to Guided Setup is unchecked
4.	Click `Add Platforms`, then then select `Web`

### Add a Redirect URL to your application

Add the URL for your index.html page based on your web server, then click *Save*.

> #### Visual Studio Instructions for Redirect URL
> If you are using Visual Studio, configure your project to use SSL, and then use the SSL URL to configure your application’s registration information using below instructions:
> 1.	In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
> 2.	Change `SSL Enabled` to `True`:<br/> ![Project properties](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
> 3.	Copy the value from `SSL URL` above, paste in a `Redirect URLs` field, and click ‘Save’

### Configure your JavaScript SPA application

1.	Create a `config.js` file containing the application registration information. If you are using Visual Studio, follow the steps below to create config.js:
a.	Select the project (project root folder), right click and select: `Add` > `New Item` > `JavaScript File`. Name it `msalconfig.js`.
2.	Add the following code to your `msalconfig.js` file:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Replace <code>Enter_the_Application_Id_here</code> with the Application Id you just registered 
</li>
</ol>