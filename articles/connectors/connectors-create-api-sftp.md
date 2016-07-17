<properties
pageTitle="SFTP | Microsoft Azure"
description="Create Logic apps with Azure App service. Connect to SFTP API to send and receive files. You can perform various operations such as create, update, get or delete files."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>

# Get started with the SFTP connector

Use the SFTP connector to access an SFTP account to send and receive files. You can perform various operations such as create, update, get or delete files.  

To use [any connector](./apis-list.md), you first need to create a Logic app. You can get started by [creating a Logic app now](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connect to SFTP

Before your Logic app can access any service, you first need to create a *connection* to the service. A [connection](./connectors-overview.md) provides connectivity between a Logic app and another service.  

### Create a connection to SFTP

>[AZURE.INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]

## Use an SFTP trigger

A trigger is an event that can be used to start the workflow defined in a Logic app. [Learn more about triggers](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

In this example, I will show you how to use the **FTP - When a file is added or modified** trigger to initiate a Logic app workflow when a file is added to, or modified on, an SFTP server. In an enterprise example, you could use this trigger to monitor an SFTP folder for new files that represent orders from customers.  You could then use an SFTP connector action such as **Get file content** to get the contents of the order for further processing and storage in your orders database.

1. Enter *ftp* in the search box on the Logic apps designer then select the **FTP - When a file is added or modified**  trigger   
![](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
The **When a file is added or modified** control opens up  
![](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
- Select the **...** located on the right side of the control. This opens the folder picker control  
![](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
- Select the **>** (right arrow) and browse to find the folder that you want to monitor for new or modified files. Select the folder and notice the folder is now displayed in the **Folder** control.  
![](./media/connectors-create-api-ftp/ftp-trigger-4.png)   


At this point, your Logic app has been configured with a trigger that will begin a run of the other triggers and actions in the workflow when a file is either modified or created in the specific SFTP folder. 

>[AZURE.NOTE]For a Logic app to be functional, it must contain at least one trigger and one action. Follow the steps in the next section to add an action.  

## Use an SFTP action

An action is an operation carried out by the workflow defined in a Logic app. [Learn more about actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

## Technical Details

Here are the details about the triggers, actions and responses that this connection supports:

## SFTP triggers

SFTP has the following trigger(s):  

|Trigger | Description|
|--- | ---|
|[When a file is added or modified](connectors-create-api-sftp.md#when-a-file-is-added-or-modified)|This operation triggers a flow when a file is added or modified in a folder.|


## SFTP actions

SFTP has the following actions:


|Action|Description|
|--- | ---|
|[Get file metadata](connectors-create-api-sftp.md#get-file-metadata)|This operation gets file metadata using the file id.|
|[Update file](connectors-create-api-sftp.md#update-file)|This operation updates the file content.|
|[Delete file](connectors-create-api-sftp.md#delete-file)|This operation deletes a file.|
|[Get file metadata using path](connectors-create-api-sftp.md#get-file-metadata-using-path)|This operation gets file metadata using the file path.|
|[Get file content using path](connectors-create-api-sftp.md#get-file-content-using-path)|This operation gets file contents using the file path.|
|[Get file content](connectors-create-api-sftp.md#get-file-content)|This operation gets file contents using the file id.|
|[Create file](connectors-create-api-sftp.md#create-file)|This operation uploads a file to an SFTP server.|
|[Copy file](connectors-create-api-sftp.md#copy-file)|This operation copies a file to an SFTP server.|
|[List files in folder](connectors-create-api-sftp.md#list-files-in-folder)|This operation gets files contained in a folder.|
|[List files in root folder](connectors-create-api-sftp.md#list-files-in-root-folder)|This operation gets the files in the root folder.|
|[Extract folder](connectors-create-api-sftp.md#extract-folder)|This operation extracts an archive file into a folder (example: .zip).|
### Action details

Here are the details for the actions and triggers for this connector, along with their responses:



### Get file metadata
This operation gets file metadata using the file id. 


|Property Name| Display Name|Description|
| ---|---|---|
|id*|File|Specify the file|

An * indicates that a property is required

#### Output Details

BlobMetadata: undefined


| Property Name | Data Type | Description |
|---|---|---|
|Id|string|undefined|
|Name|string|undefined|
|DisplayName|string|undefined|
|Path|string|undefined|
|LastModified|string|undefined|
|Size|integer|undefined|
|MediaType|string|undefined|
|IsFolder|boolean|undefined|
|ETag|string|undefined|
|FileLocator|string|undefined|




### Update file
This operation updates the file content. 


|Property Name| Display Name|Description|
| ---|---|---|
|id*|File|Specify the file|
|body*|File content|Content of the file to update|

An * indicates that a property is required

#### Output Details

BlobMetadata: undefined


| Property Name | Data Type | Description |
|---|---|---|
|Id|string|undefined|
|Name|string|undefined|
|DisplayName|string|undefined|
|Path|string|undefined|
|LastModified|string|undefined|
|Size|integer|undefined|
|MediaType|string|undefined|
|IsFolder|boolean|undefined|
|ETag|string|undefined|
|FileLocator|string|undefined|




### Delete file
This operation deletes a file. 


|Property Name| Display Name|Description|
| ---|---|---|
|id*|File|Specify the file|

An * indicates that a property is required




### Get file metadata using path
This operation gets file metadata using the file path. 


|Property Name| Display Name|Description|
| ---|---|---|
|path*|File path|Unique path of the file|

An * indicates that a property is required

#### Output Details

BlobMetadata: undefined


| Property Name | Data Type | Description |
|---|---|---|
|Id|string|undefined|
|Name|string|undefined|
|DisplayName|string|undefined|
|Path|string|undefined|
|LastModified|string|undefined|
|Size|integer|undefined|
|MediaType|string|undefined|
|IsFolder|boolean|undefined|
|ETag|string|undefined|
|FileLocator|string|undefined|




### Get file content using path
This operation gets file contents using the file path. 


|Property Name| Display Name|Description|
| ---|---|---|
|path*|File path|Unique path of the file|

An * indicates that a property is required




### Get file content
This operation gets file contents using the file id. 


|Property Name| Display Name|Description|
| ---|---|---|
|id*|File|Specify the file|

An * indicates that a property is required




### Create file
This operation uploads a file to an SFTP server. 


|Property Name| Display Name|Description|
| ---|---|---|
|folderPath*|Folder path|Unique path of the folder|
|name*|File name|Name of the file|
|body*|File content|Content of the file to create|

An * indicates that a property is required

#### Output Details

BlobMetadata: undefined


| Property Name | Data Type | Description |
|---|---|---|
|Id|string|undefined|
|Name|string|undefined|
|DisplayName|string|undefined|
|Path|string|undefined|
|LastModified|string|undefined|
|Size|integer|undefined|
|MediaType|string|undefined|
|IsFolder|boolean|undefined|
|ETag|string|undefined|
|FileLocator|string|undefined|




### Copy file
This operation copies a file to an SFTP server. 


|Property Name| Display Name|Description|
| ---|---|---|
|source*|Source file path|Path to the source file|
|destination*|Destination file path|Path to the destination file, including file name|
|overwrite|Overwrite?|Overwrites the destination file if set to 'true'|

An * indicates that a property is required

#### Output Details

BlobMetadata: undefined


| Property Name | Data Type | Description |
|---|---|---|
|Id|string|undefined|
|Name|string|undefined|
|DisplayName|string|undefined|
|Path|string|undefined|
|LastModified|string|undefined|
|Size|integer|undefined|
|MediaType|string|undefined|
|IsFolder|boolean|undefined|
|ETag|string|undefined|
|FileLocator|string|undefined|




### When a file is added or modified
This operation triggers a flow when a file is added or modified in a folder. 


|Property Name| Display Name|Description|
| ---|---|---|
|folderId*|Folder|Specify a folder|

An * indicates that a property is required




### List files in folder
This operation gets files contained in a folder. 


|Property Name| Display Name|Description|
| ---|---|---|
|id*|Folder|Specify the folder|

An * indicates that a property is required



#### Output Details

BlobMetadata: undefined


| Property Name | Data Type | Description |
|---|---|---|
|Id|string|undefined|
|Name|string|undefined|
|DisplayName|string|undefined|
|Path|string|undefined|
|LastModified|string|undefined|
|Size|integer|undefined|
|MediaType|string|undefined|
|IsFolder|boolean|undefined|
|ETag|string|undefined|
|FileLocator|string|undefined|




### List files in root folder
This operation gets the files in the root folder. 


There are no parameters for this call

#### Output Details

BlobMetadata: undefined


| Property Name | Data Type | Description |
|---|---|---|
|Id|string|undefined|
|Name|string|undefined|
|DisplayName|string|undefined|
|Path|string|undefined|
|LastModified|string|undefined|
|Size|integer|undefined|
|MediaType|string|undefined|
|IsFolder|boolean|undefined|
|ETag|string|undefined|
|FileLocator|string|undefined|




### Extract folder
This operation extracts an archive file into a folder (example: .zip). 


|Property Name| Display Name|Description|
| ---|---|---|
|source*|Source archive file path|Path to the archive file|
|destination*|Destination folder path|Path to the destination folder|
|overwrite|Overwrite?|Overwrites the destination files if set to 'true'|

An * indicates that a property is required



#### Output Details

BlobMetadata: undefined


| Property Name | Data Type | Description |
|---|---|---|
|Id|string|undefined|
|Name|string|undefined|
|DisplayName|string|undefined|
|Path|string|undefined|
|LastModified|string|undefined|
|Size|integer|undefined|
|MediaType|string|undefined|
|IsFolder|boolean|undefined|
|ETag|string|undefined|
|FileLocator|string|undefined|



## HTTP responses

The actions and triggers above can return one or more of the following HTTP status codes: 

|Name|Description|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|Unauthorized|
|403|Forbidden|
|404|Not Found|
|500|Internal Server Error. Unknown error occurred|
|default|Operation Failed.|







## Next Steps
[Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md)