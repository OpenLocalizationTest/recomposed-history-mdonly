---
title: How to use Send Grid in Azure Functions | Microsoft Docs
description: Shows how to use Send Grid in Azure Functions
services: functions
documentationcenter: na
author: rachelappel
manager: erikre

ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: rachelap

---
# How to use Send Grid in Azure Functions

## SendGrid Overview

SendGrid is an SMTP provider in Azure that allows you to send email programmatically by using their API. The SendGrid API supports C#, Go, Java, NodeJS, Python, Ruby, and PHP. 

> [!IMPORTANT]
> SendGrid is currently in Preview mode. 

To use the SendGrid API in an Azure Function, you must have a [SendGrid account](http://SendGrid.com), if you do not have one already. Additionally, you must have a SendGrid API Key. Log into your [SendGrid account](http://SendGrid.com) and click **Settings** then **API Key** to generate an API key. Keep this key available as you will use it in an upcoming step.

You are now ready to create an Azure Function app.

## Create an Azure Function app 

Azure Function Apps are containers for one or more Azure functions. Azure functions are just that - a function. Each Azure function is tied to one trigger, which is an event that causes the function to run.
Each function can contain zero or more input and zero or output bindings. Bindings are services that you can use in a function. In the case of SendGrid, it's an output binding that you can use to send email. 

1. Login to the Azure Portal and [create an Azure Function App](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) or open an existing Function app. 
2. Create an Azure function. To keep it simple, choose a manual trigger and C#. 

 ![Create an Azure function](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## Configure SendGrid for use in an Azure Function app

You must store your SendGrid Api Key as an app setting in order to use it in a function. The ApiKey field is not your actual SendGrid api key, but an app setting you define that represents your actual api key. This is for security reasons, as your key is stored in Azure separate from any code or files that might be checked into source code control.

1. Create an **AppSettings** key in your function app's **Application Settings**.

 ![Create an Azure function](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

Function apps also requre a reference to external assemblies before you may use them.  

2. List the SendGrid assembly in the **project.json** file. If no project.json file exists, create one. Azure will automatically create the **project.lock.json** file to keep track of dependencies.

 ![Reference SendGrid assembly](./media/functions-how-to-use-sendgrid/functions-reference-sendgrid-assembly.png)
 
 The contents of the **project.json** file should look like the following:

```json
 {
  "frameworks": {
    "net46":{
      "dependencies": {
        "Sendgrid": "8.0.5"
      }
    }
   }
}
```
Now that you have created and configured the Function App and function, you can write the code to send an email.

## Configure SendGrid output bindings

SendGrid is available as an Azure function output binding. To create a SendGrid output binding:

1. Go to the **Integrate** tab of the function in the Azure portal.
1. Click on **New Output** to create a new SendGrid output binding.
2. Fill in the **API Key** and **Message parameter name** properties. You can enter the other properties now if you want, or code them instead. These settings can be used as defaults.

 ![Configure SendGrid output bindings](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

Adding a binding to a function creates a file called **function.json** in your function's folder. This file contains all the same information that you see in the Azure function's **Integrate** tab, but in Json format. 
Setting the **ApiKey**, **message**, and **from** fields creates the following **function.json** file. 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

You may modify this file yourself directly if you prefer.

## Write code that creates and sends email

The SendGrid API contains all the commands you need to create and send an email.  

1. Replace the code in the function with the following code:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
  
    return message;
}
```

Notice the first line contains the ```#r``` directive that references the SendGrid assembly. After that, you can use a ```using``` statement to more easily access the objects in that namespace. 
In the code, you will create new instances of ```Mail```, ```Personalization```, and ```Content``` objects that compose the email. When you return the message, SendGrid will deliver it. 

The function's signature also contains an extra out parameter of type ```Mail``` named ```message```. Both input and output bindings express themselves as function parameters in code. 

2. Test your code by clicking **Test** and entering a message into the **Request body** field, then clicking the **Run** button.

 ![Test your code](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. Check email to verify that SendGrid sent the email. It should go to the address in the code from step 1, and contain the message from the **Request body**.

## Next steps
This article has demonstrated how to use the SendGrid service to create and send email. To learn more about using Azure Functions in your apps, see the following topics: 

- [Best practices for Azure Functions](../azure-functions/functions-best-practices)
Lists some best practices to use when creating Azure Functions.

- [Azure Functions developer reference](../azure-functions/functions-reference)
Programmer reference for coding functions and defining triggers and bindings.

- [Testing Azure Functions](../azure-functions/functions-test-a-function)
Describes various tools and techniques for testing your functions.