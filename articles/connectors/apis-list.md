<properties
	pageTitle="List of managed APIs | Microsoft Azure App Service"
	description="Get a complete list of the Microsoft-Managed APIs you can use to build Logic apps in Azure App Service"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="deonhe"/>

# Here's the list of Microsoft-managed APIs for building logic apps

Select an icon to learn how to quickly leverage these APIs to build logic apps that call these services.

For pricing information and a list of what is included with each Service Tier, see [Azure App Service Pricing](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] If you want to get started with Azure Logic Apps before signing up for an Azure account, go to [Try Logic App](https://tryappservice.azure.com/?appservice=logic), where you can immediately create a short-lived starter logic app in App Service. No credit cards required; no commitments.

|APIs||||
|-----------|-----------|-----------|-----------|
|![API Icon] [bingsearchicon] <br> [**Bing Search**][bingsearchdoc]|![API Icon] [boxicon] <br> [**Box**][boxDoc]|[![API Icon] [crmonlineicon] <br> **CRM Online**][crmonlinedoc]|[![API Icon] [dropboxicon] <br> **Dropbox**][dropboxdoc]|
|[![API Icon] [excelicon] <br> **Excel**][exceldoc]|[![API Icon] [ftpicon] <br> **FTP**][ftpdoc]|[![API Icon] [googledriveicon] <br> **Google Drive**][googledrivedoc]|[![API Icon] [microsofttranslatoricon] <br> **Translator**][microsofttranslatordoc]|
|[![API Icon] [office365icon] <br> **Office 365** <br> **Outlook**][office365outlookdoc]|[![API Icon] [office365icon] <br> **Office 365**<br> **Users**][office365usersdoc]|[![API Icon] [office365icon] <br> **Office 365** <br>**Video**][office365videodoc]|[![API Icon] [office365icon] <br> **Office**<br>**Unified**][officeunifieddoc]|
|[![API Icon] [onedriveicon] <br> **OneDrive**][onedrivedoc]|[![API Icon] [salesforceicon] <br> **Salesforce**][salesforcedoc]|[![API Icon] [servicebusicon] <br> **Service Bus**][servicebusdoc]|[![API Icon] [sftpicon] <br> **SFTP**][sftpdoc]|
|[![API Icon] [sharepointicon] <br> **SharePoint**<br>**Server**][sharepointdoc]|[![API Icon] [slackicon] <br> **Slack** <br>][slackdoc]|[![API Icon] [smtpicon] <br> **SMTP**][smtpdoc]|[![API Icon] [sqlicon] <br> **SQL**][sqldoc]|
|[![API Icon] [twilioicon] <br> **Twilio**][twiliodoc]|[![API Icon] [twittericon] <br> **Twitter**][twitterdoc]|[![API Icon] [yammericon] <br> **Yammer**][yammerdoc]

>[AZURE.INFO] These APIs will work with version 2015-12-01-preview of the service.

### APIs can be triggers
Several APIs provide triggers that can notify your app when specific events occur. For example, the FTP API has the OnUpdatedFile trigger. You can build an either a logic app or power app that listens to this trigger and takes an action whenever the trigger is fired. 

There are two types of triggers:
Poll Triggers: These triggers poll your service at a specified frequency to check for new data. When new data is available, a new instance of your Logic App runs with the data as input. To prevent the same data from being consumed multiple times, the trigger may clean-up data that has been read and passed to the Logic App. TODO: Examples of such connectors are _____________________________.
Push Triggers: These triggers listen for data on an endpoint or for an event to occur. Then, triggers a new instance of your app. The twitter API is one such example. TODO--verify that Twitter is a good example.


### APIs can be actions
APIs can also be used as actions within your apps. Actions are useful for looking up data which can then be used in the execution of your app. For example, you may need to look up customer data from a SQL database when processing an order. Or, you may need to write, update or delete data in a destination. You can do this using the actions provided by the APIs. Actions map to operations that are defined in the Swagger metadata.


[What's new](TODO)  
[Get help migrating to version 2015-12-01-preview](TODO)  
[Build a logic app now](../app-service-logic/app-service-logic-create-a-logic-app.md)
[Get started with Power apps now](../power-apps/powerapps-get-started-azure-portal.md)  

<!--API Documentation-->
[bingsearchDoc]: ../power-apps/powerapps-create-api-bingsearch.md "Search Bing for web, images, news and video."
[boxDoc]: ../power-apps/powerapps-create-api-box.md/ "Connects to Box and can upload, get, delete, list, and more file tasks."
[crmonlinedoc]: ../power-apps/powerapps-create-api-crmonline.md/ "Connect to Dynamics CRM Online and do more with your CRM Online data."
[dropboxdoc]: ../power-apps/powerapps-create-api-dropbox.md/ "Connect to Dropbox and can get, delete, list, and more file tasks."
[exceldoc]: ../power-apps/powerapps-create-api-excel.md/ "Connect to Excel."
[ftpdoc]: ../power-apps/powerapps-create-api-ftp.md/ "Connects to an FTP / FTPS server and do different FTP tasks, including uploading, getting, deleting files, and more."
[googledrivedoc]: ../power-apps/powerapps-create-api-googledrive.md/ "Connect to GoogleDrive and interact with your data."
[microsofttranslatordoc]: ../power-apps/powerapps-create-api-microsofttranslator.md/
[office365outlookdoc]: ../power-apps/powerapps-create-api-office365-outlook.md/ "The Office 365 Connector can send and receive emails, manage your calendar, and manage your contacts using your Office 365 account."
[officeunifieddoc]: ../power-apps/powerapps-create-api-bingsearch.md/
[office365usersdoc]: ../power-apps/powerapps-create-api-office365-users.md/
[office365videodoc]: ../power-apps/powerapps-create-api-video.md/
[onedrivedoc]: ../power-apps/powerapps-create-api-onedrive.md/ "Connects to your personal Microsoft OneDrive and upload, delete, list files, and more."
[salesforcedoc]: ../power-apps/powerapps-create-api-salesforce.md/ "Connect to your Salesforce account and manage  accounts, leads, opportunities, and more."
[servicebusdoc]: ../power-apps/powerapps-create-api-servicebus.md/ "Can send messages from Service Bus Queues and Topics and receive messages from Service Bus Queues and Subscriptions."
[sharepointdoc]: ../power-apps/powerapps-create-api-sharepointserver.md/ "Connects to on-premises Microsoft SharePoint Server or SharePoint Online to manage documents, and list items. Different authentication methods such as default credentials, OAuth 2.0, Windows authentication, and Form-Based authentication are supported."
[slackdoc]: ../power-apps/powerapps-create-api-slack.md/ "Connect to Slack and post messages to Slack channels."
[sftpdoc]: ../power-apps/powerapps-create-api-sftp.md/ "Connects to SFTP and can upload, get, delete files, and more."
[smtpdoc]: ../power-apps/powerapps-create-api-smtp.md/ "Connects to a SMTP server and can send email with attachments." 
[sqldoc]: ../power-apps/powerapps-create-api-sqlserver.md/ "Connects to on-premises SQL Server or an Azure SQL Database. You can create, update, get, and delete entries on a SQL database table. <br> MQ | Connects to IBM WebSphere MQ Server version 8, on-premises and on an Azure virtual machine running a Windows operating system. When using on-premises, VPN or Azure ExpressRoute can be used. The connector also includes the Microsoft Client for MQ.<br/><br/>No triggers. No actions.<br/><br/>**Note** Currently cannot be used with Logic Apps."
[twiliodoc]: ../power-apps/powerapps-create-api-twilio.md/ "Connects to Twilio and can send and get messages, get available numbers, managing incoming phone numbers, and more."
[twitterdoc]: ../power-apps/powerapps-create-api-twitter.md/ "Connects to Twitter and get timelines, post tweets, and more."
[yammerdoc]: ../power-apps/powerapps-create-api-yammer.md/ "Connects to Yammer to post messages and get new messages."

<!--Icon references-->
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[yammericon]: ./media/apis-list/yammericon.png
