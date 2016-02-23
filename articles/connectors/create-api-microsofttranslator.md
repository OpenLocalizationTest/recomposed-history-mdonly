<properties
	pageTitle="Add the Microsoft Translator in PowerApps Enterprise or logic apps| Microsoft Azure"
	description="Overview of the Microsoft Translator API with REST API parameters"
	services=""
    suite=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/11/2016"
   ms.author="mandia"/>

# Get started with the Microsoft Translator API
Connect to Microsoft Translator to translate text, detect a language, and more. 

The Microsoft Translator can be be used from PowerApps Enterprise and logic apps. 

With Microsoft Translator, you can: 

- Build your business flow based on the data you get from Microsoft Translator. 
- Use actions to translate text, detect a language, and more. These actions get a response, and then make the output available for other actions. For example, when a new file is created in Dropbox, you can translate the text in the file to another language using Microsoft Translator.
- Add the Microsoft Translator API to PowerApps Enterprise. Then, your users can use this API within their apps. 

For information on how to add an API in PowerApps Enterprise, go to [Register an API in PowerApps](../power-apps/powerapps-register-from-available-apis.md). 

To add an operation in logic apps, see [Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Triggers and actions
Microsoft Translator includes the following actions. There are no triggers.

Triggers | Actions
--- | ---
None | <ul><li>Detect language</li><li>Text to speech</li><li>Translate text</li><li>Get languages</li><li>Get speech languages</li></ul>

All APIs support data in JSON and XML formats.

## Create the connection to Microsoft Translator

### Add additional configuration in PowerApps
When you add Microsoft Translator to PowerApps Enterprise, you enter the **Client Id ** and **Client Secret** values of your Microsoft Translator application. If you don't have a Translator application, you can can create one: 

1. Go to [Azure Data Market developer's page][5] and sign in with your Microsoft Account. 

2. Select **Register your application**:  

	1. Enter a value for **Client Id**.
	2. Enter the **name** of your application.
	3. Enter a dummy value for **redirect url**. For example, enter *https://contosoredirecturl*.
	4. Enter a **description**.
	5. Select **Create**.  

	![Register your application][6]

Now copy/paste the **Client Id** and **Client Secret** values in your Translator configuration in the Azure portal. 


## Swagger REST API reference

### Detect language    
Detects source language of given text. 
```GET: /Detect```

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|none |Text whose language will be identified|

#### Response
|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|


### Text to speech    
Converts a given text into speech as an audio stream in wave format. 
```GET: /Speak```

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|none |Text to convert|
|language|string|yes|query|none |Language code to generate speech (example: 'en-us')|

#### Response
|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|


### Translate text    
Translates text to a specified language using Microsoft Translator. 
```GET: /Translate```

| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|none |Text to translate|
|languageTo|string|yes|query| none|Target language code (example: 'fr')|
|languageFrom|string|no|query|none |Source language; if not provided, Microsoft Translator will try to auto-detect. (example: en)|
|category|string|no|query|general |Translation category (default: 'general')|

#### Response
|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|


### Get languages    
Retrieves all languages that Microsoft Translator supports. 
```GET: /TranslatableLanguages```

There are no parameters for this call. 

#### Response
|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|


### Get speech languages    
Retrieves the languages available for speech synthesis. 
```GET: /SpeakLanguages``` 

There are no parameters for this call.

#### Response
|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|

## Object definitions

#### Language: language model for Microsoft Translator translatable languages

| Name | Data Type | Required|
|---|---|---|
|Code|string|no|
|Name|string|no|


## Next steps
After you add the Microsoft Translator API to PowerApps Enterprise, [give users permissions](../power-apps/powerapps-manage-api-connection-user-access.md) to use the API in their apps.

[Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/create-api-microsofttranslator/register-your-application.png