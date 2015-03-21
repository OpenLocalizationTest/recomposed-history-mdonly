<properties 
	pageTitle="Enterprise Connectors in Microsoft Azure App Service | Azure" 
	description="Learn how to create an enterprise connector and add the connector to your App; microservices architecture" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="mandia"/>

# Enterprise Connectors in Microsoft Azure App Service


## What is an Enterprise Connector
Enterprise Connectors are existing "API Apps" that can connect to on-premises and cloud-based enterprise systems, including: 

Connector | Description
--- | ---
Marketo | Connect to Marketo and perform different actions such as Create/Update Leads, Get Lead Changes, Schedule Campaign, Request Campaign, Get Leads, Get Campaigns/List info, Add Leads to List, and Remove Leads from List.
MongoDB | Connect to the Mongo Server Database or on-premises Mongo server. On a MongoDB collection, perform different actions such as create, update, get, and delete entries. 
QuickBooks | Connect to QuickBooks and use your applications to create, update, read, delete, and query different entities from Intuit QuickBooks, such as customers, items, invoices, and so on.
Salesforce |Connect to SalesForce to to manage different entities such as Accounts, Leads, Opportunities, Cases, and so on in your Salesforce account. You can perform different actions such as create, update, upsert, query, and delete on the different entities. 
SugarCRM | Connect to SugarCRM online and use your applications to create, update, read, and delete and different types of modules, such as accounts, contacts, products, and so on.
SAP | Connect to an on-premises SAP server. Invoke RFCs, BAPIs, and tRFCs and also send IDOCs.


Using these connectors, you can complete different tasks on the enterprise system. For example, using the QuickBooks connector, you can do typical tasks within QuickBooks, like add a new customer, update an invoice, or delete an item. 

You can create as many connectors as you want and create them easily. You can also reuse a single connector within multiple scenarios or workflows.

For example, let's say you are at a conference and meet a customer that would like to purchase 10 items of your product. On your mobile device, you can open your Mobile App that uses the QuickBooks connector you created, add the Customer details, and create an invoice. 

While you're at the conference, a customer at your business location wants to pay an existing invoice. The employee opens an application that uses the same QuickBooks connector you created, searches for the Customer information, and updates the invoice to reflect the new balance. 

These are different flows and they use the same QuickBooks connector. You can create and use these connectors without writing any code. Let's get started. 


## Requirements to Get Started
When you create a connector that uses an on-premises system, like SAP, there are some required resources. These items must be created by you before they can be used within the connector. These requirements include: 

Requirement | Description
--- | ---
Service Bus Namespace and its Key values | When using on-premises SAP or MongoDB, a Service Bus Namespace and it's key values are needed. If you aren't connecting to an on-premises system, a Service Bus namespace is not needed.<br/><br/>[Create a Service Bus Namespace](http://msdn.microsoft.com/library/azure/hh690931.aspx)
On-Premises Hybrid Connection Manager | When you're connecting to an on-premises system, you install the Hybrid Connection Manager on the on-premises system. When you create the on-premises connector, the download is available in the connector properties or settings. 



## Create a Connector

A connector can be created using the Azure portal or using REST APIs. 

### Create a Connector using REST APIs
http://go.microsoft.com/fwlink/p/?LinkId=529766


### Create a Connector in the Azure Portal
In the Azure portal, you can create an enterprise connector when creating Logic Apps, Web Apps, or Mobile Apps. Or, you can create one using its own blade. Both ways are easy so it depends on your needs or preferences. Some users prefer to create all the connectors with their specific properties first. Then, create the Logic, Web, or Mobile Apps, and add the connector you created.  

The following steps create an enterprise connector using the connector blade:

1. In the Azure portal Startboard (the Home page), select **Marketplace**. **API Apps** lists all the existing connectors. You can also **Search** for a specific connector.
2. Select the connector. In the new blade, select **Create**. 
3. Enter the properties: 

	Property | Description
--- | ---
Name | Enter any name for your connector. For example, you can name it *SAPConnector*,  *SalesForceGetAccounts*, or *QuickBooksGetItems*.
Package Settings | Enter the enterprise system settings, like *SAP User Name* or *SugarCRM Server URL*. See [Add the Connector](#AddConnector) in this topic for the enterprise-specific properties. 
App Service Plan | Lists your payment plan. You can change it if you need more or less resources.
Pricing Tier | Read-only property that lists the pricing category within your Azure subscription. 
Resource Group | Create a new one or use an existing group. [Using resource groups](../azure-preview-portal-using-resource-groups) explains this property. 
Subscription | Read-only property that lists your current subscription.
Location | The Geographic location that hosts your Azure service. 
Add to Startboard | Select this to add the connector to your Starboard (the home page).

4. Select **Create**.


### Install the On-Premises Hybrid Connection Manager
After you create the enterprise connector that connects to an on-premises system, like SAP, install the Hybrid Conenction Manager: 

1. On the on-premises enterprise system, open the Azure Management portal, and select your enterprise connector. 
2. In the blade, select **Hybrid Connection**. 
3. Copy the **Primary Gateway Configuration String** value. 
4. Select **Download and Configure**. During the installation, paste the Gateway Configuration String you copied and continue with the installation. 
5. To confirm connectivity, open your enterprise connector blade. The status should **Connected**. 

[Integrate with an on-premises SAP server](app-service-logic-integrate-with-an-on-premise-SAP-server.md) provides an example. 


## <a name="AddConnector"></a>Add the Connector to your application 
Microsoft Azure App Service (or App Service for short) exposes different application types that can use these connectors. You can create a new or add your existing connectors to Logic Apps, Mobile Apps, or a Web Apps.  

Within your App, simply selecting your connector from the Gallery automatically adds it to your App. Add the properties, and its ready to be used.

The following steps add an enterprise connector to Logic Apps, Mobile Apps, or Web Apps: 

1. In the Azure portal Startboard (home page), go to the **Marketplace**, and search for your  Logic, Mobile, or Web Apps.

	If you are creating a new App, search for Logic Apps, Mobile Apps, or Web Apps. Select the App and in the new blade, select **Create**. [Create a Logic App](app-service-logic-create-a-logic-app.md) lists the steps. 

2. Open your App and select **Triggers and Actions**. 

3. From the **Gallery**, add the connectors you created, which automatically adds it to your App. 
4. Enter the following properties: 

	> [AZURE.IMPORTANT] Every connector has properties that are specific to that enterprise system. When connecting to SAP, you enter SAP-specific properties. When connecting to Salesforce, you enter Salesforce-specific properties, and so on.	The following table lists the required enterprise system properties. 

	Enterprise System | Required Properties
--- | ---
Marketo | <ul><li>Endpoint</li><li>Provider Name</li></ul>
MongoDB| <ul><li>Connection String</li><li>Host</li><li>Port</li><li>User Name</li><li>Password</li><li>Database</li><li>Use SSL Encryption</li><li>On-Premises: Enter False if it is cloud-based. If the MongoDb system is on-premises, enter True and also enter the following properties:<ul><li>Shared Access Key Name</li><li>Service Bus Namespace</li><li>Relay Path</li><li>Send Key</li></ul></li></ul>
QuickBooks | <ul><li>Company ID</li><li>Provider name</li></ul>
SAP | <ul><li>Host Name</li><li>Language</li><li>User Name</li><li>Password</li><li>System Number</li><li>Service Bus Connection String</li><li>RFC Names</li><li>TRFC Names</li><li>BAPI Names</li><li>IDOC Name</li></ul>
Salesforce | <ul><li>Provider name</li><li>Instance</li><li>Version</li><li>Entities (comma-separated values)</li></ul>
SugarCRM | <ul><li>Server URL</li><li>Provider name</li><li>Module names</li></ul>

5. Select **OK** to save your changes. 


## Security
Some of the enterprise connectors us OAuth security. OAuth security authorizes the connector (like QuickBooks) with 3rd parties (like QuickBooks). When you add the connector to your App, you **Authorize** the connector by connecting to the enterprise system with your sign-in account and agree to the terms. When you do this, your App and the connector use the sign-in account to authenticate with the system. 


## More Enterprise Connector resources

[Integrate with an on-premises SAP server](app-service-logic-integrate-with-an-on-premise-SAP-server.md)
[What are Connectors and BizTalk API Apps](app-service-logic-what-are-biztalk-api-apps.md)


## Read about Logic Apps and Web Apps
[What are Logic Apps?](app-service-logic-what-are-logic-apps.md) 
[Websites and Web Apps in Azure App Service](app-service-web-app-azure-portal.md)



## More Connectors

[BizTalk Integration connectors](app-service-logic-integration-connectors.md)
[Business-to-Business connectors](app-service-logic-b2b-connectors.md)
[Social connectors](app-service-logic-social-connectors.md)
[Protocol connectors](app-service-logic-protocol-connectors.md)
[App and Data Services connectors](app-service-logic-data-connectors)
[Connectors and API Apps List](app-service-logic-connectors-list.md)