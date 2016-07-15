<properties
pageTitle="Twitter | Microsoft Azure"
description="Create Logic apps with Azure App service. Twitter Connection Provider lets you post tweets and get tweets, timeline, friends and followers from your Twitter account."
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
ms.date="04/29/2016"
ms.author="deonhe"/>

# Get started with the Twitter connector

Twitter Connection Provider lets you post tweets and get tweets, timeline, friends and followers from your Twitter account.You can get started by creating a Logic app now, see [Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connect to Twitter

Before your Logic app can access any service, you first need to create a *connection* to the service. A connection provides connectivity between a Logic app and another service. For example, in order to connect to Dropbox, you first need a Dropbox *connection*. To create a connection, you would need to provide the credentials you normally use to access the service you wish to connect to. So, in the Dropbox example, you would need the credentials to your Dropbox account in order to create the connection to Dropbox. [Learn more about connections]()

### Create a connection to Twitter

>[AZURE.INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-Twitter.md)]

## Use an Twitter trigger

A trigger is an event that can be used to start the workflow defined in a Logic app. [Learn more about triggers]().

In this example, I will show you how to use the **When a new tweet is posted**  trigger to search for #Seattle and, if #Seattle is found, update a file in Dropbox with the text from the tweet. In an enterprise example, you could search for the name of your company and update a SQL database with the text from the tweet.

1. Enter the *twitter* in the search box on the Logic apps designer then select **Twitter - When a new tweet is posted**  trigger   
![](./media/connectors-create-api-twitter/trigger-1.png)  
- Enter *#Seattle* in the **Search Text** control 
![](./media/connectors-create-api-twitter/trigger-2.png)  
- Select **+ New step** to add the action you would like to take when #Seattle is found in a new tweet  
![](./media/connectors-create-api-twitter/trigger-3.png)  
- Select **Add an action** link. This opens the search box where you can search for any action you would like to take.  
![](./media/connectors-create-api-twitter/trigger-4.png)  
- Enter *dropbox* to search for action related to Dropbox.
- Select **Dropbox - Update file**  as the action to take when #Seattle is found in a new tweet. The update file control opens. **Note**: you will be prompted to authorize your Logic app to access your Twitter account if you have not done so previously.    
![](./media/connectors-create-api-twitter/trigger-5.png)  
- Select **...** and find the file you would like to update. 
- Select the file you would like to update    
![](./media/connectors-create-api-twitter/trigger-6.png)  
- Select the **File content control**. Here, you can use any of the various pieces of data that are associated with the tweet found with the #Seattle value.
- Select the **Tweet text** option. This will insert the tweet text into the **File content** control.  
![](./media/connectors-create-api-twitter/trigger-7.png)  
-  The control is updated, indicating that the tweet text will be used to update the file in Dropbox.    
![](./media/connectors-create-api-twitter/trigger-8.png)  
- Save your work and send a tweet with the #Seattle hashtag to activate your workflow. 


## Use an Twitter action

An action is an operation carried out by the workflow defined in a Logic app. [Learn more about actions]().

## Technical Details

Here are the details about the triggers, actions and responses that this connection supports:

## Twitter triggers

Twitter has the following trigger(s):  

|Trigger | Description|
|--- | ---|
|[When a new tweet is posted](connectors-create-api-twitter.md#when-a-new-tweet-is-posted)|This operation triggers a flow when a new tweet that matches a given search query is posted.|


## Twitter actions

Twitter has the following actions:


|Action|Description|
|--- | ---|
|[Get user timeline](connectors-create-api-twitter.md#get-user-timeline)|This operation gets a list of the most recent tweets posted by a given user.|
|[Get home timeline](connectors-create-api-twitter.md#get-home-timeline)|This operation gets the most recent tweets and re-tweets posted by me and my followers.|
|[Search tweets](connectors-create-api-twitter.md#search-tweets)|This operation gets a list of relevant tweets matching the search query.|
|[Get followers](connectors-create-api-twitter.md#get-followers)|This operation gets the list of users that follow a given user.|
|[Get my followers](connectors-create-api-twitter.md#get-my-followers)|This operation gets the list of users who are following me.|
|[Get following](connectors-create-api-twitter.md#get-following)|The operation gets the list of people the given user follows.|
|[Get my following](connectors-create-api-twitter.md#get-my-following)|This operation gets the list of users that I am following.|
|[Get user](connectors-create-api-twitter.md#get-user)|This operation gets the profile details for a given user, such as user name, description, followers count, and more.|
|[Post a tweet](connectors-create-api-twitter.md#post-a-tweet)|This operation posts a new tweet.|
### Action details

Here are the details for the actions and triggers for this connector, along with their responses:



## Get user timeline
This operation gets a list of the most recent tweets posted by a given user. 


|Property Name| Data Type|Description|
| ---|---|---|
|userName*|string|Twitter handle for the user|
|maxResults|integer|Maximum number of tweets to return|

An * indicates that a property is required



##### Output Details

TweetModel: Representation of Tweet Object


| Property Name | Data Type | Description |
|---|---|---|
|TweetText|string|Text content of the tweet|
|TweetId|string|Id of the tweet|
|CreatedAt|string|Time at which the tweet was posted|
|RetweetCount|integer|Total number of re-tweets for the tweet|
|TweetedBy|string|Name of the user who has posted the tweet|
|MediaUrls|array|Url of the media posted along with the tweet|
|TweetLanguageCode|string|Language code of the tweet|
|TweetInReplyToUserId|string|User Id of the author of the tweet that the current tweet is a reply to|
|Favorited|boolean|Indicates whether the tweet is marked as favorited or not|
|UserMentions|array|List of users mentioned in the tweet|
|OriginalTweet|not defined|Original tweet from which the current tweet is re-tweeted|
|UserDetails|not defined|Details of the user who tweeted|




## Get home timeline
This operation gets the most recent tweets and re-tweets posted by me and my followers. 


|Property Name| Data Type|Description|
| ---|---|---|
|maxResults|integer|Maximum number of tweets to return|

An * indicates that a property is required



##### Output Details

TweetModel: Representation of Tweet Object


| Property Name | Data Type | Description |
|---|---|---|
|TweetText|string|Text content of the tweet|
|TweetId|string|Id of the tweet|
|CreatedAt|string|Time at which the tweet was posted|
|RetweetCount|integer|Total number of re-tweets for the tweet|
|TweetedBy|string|Name of the user who has posted the tweet|
|MediaUrls|array|Url of the media posted along with the tweet|
|TweetLanguageCode|string|Language code of the tweet|
|TweetInReplyToUserId|string|User Id of the author of the tweet that the current tweet is a reply to|
|Favorited|boolean|Indicates whether the tweet is marked as favorited or not|
|UserMentions|array|List of users mentioned in the tweet|
|OriginalTweet|not defined|Original tweet from which the current tweet is re-tweeted|
|UserDetails|not defined|Details of the user who tweeted|




## Search tweets
This operation gets a list of relevant tweets matching the search query. 


|Property Name| Data Type|Description|
| ---|---|---|
|searchQuery*|string|Search term like "happy hour", #haiku, love OR hate|
|maxResults|integer|Maximum number of tweets to return|

An * indicates that a property is required



##### Output Details

TweetModel: Representation of Tweet Object


| Property Name | Data Type | Description |
|---|---|---|
|TweetText|string|Text content of the tweet|
|TweetId|string|Id of the tweet|
|CreatedAt|string|Time at which the tweet was posted|
|RetweetCount|integer|Total number of re-tweets for the tweet|
|TweetedBy|string|Name of the user who has posted the tweet|
|MediaUrls|array|Url of the media posted along with the tweet|
|TweetLanguageCode|string|Language code of the tweet|
|TweetInReplyToUserId|string|User Id of the author of the tweet that the current tweet is a reply to|
|Favorited|boolean|Indicates whether the tweet is marked as favorited or not|
|UserMentions|array|List of users mentioned in the tweet|
|OriginalTweet|not defined|Original tweet from which the current tweet is re-tweeted|
|UserDetails|not defined|Details of the user who tweeted|




## Get followers
This operation gets the list of users that follow a given user. 


|Property Name| Data Type|Description|
| ---|---|---|
|userName*|string|Twitter handle for the user|
|maxResults|integer|Maximum number of users to return|

An * indicates that a property is required



##### Output Details

UserDetailsModel: Twitter user details


| Property Name | Data Type | Description |
|---|---|---|
|FullName|string|Name of the user|
|Location|string|Location of the user|
|Id|integer|Twitter Id of the user|
|UserName|string|Screen name of the user|
|FollowersCount|integer|Number of followers|
|Description|string|User description|
|StatusesCount|integer|User status count|
|FriendsCount|integer|Number of friends|
|FavouritesCount|integer|Number of tweets that the user has favorited|
|ProfileImageUrl|string|Url of the profile image|




## Get my followers
This operation gets the list of users who are following me. 


|Property Name| Data Type|Description|
| ---|---|---|
|maxResults|integer|Maximum number of users to get|

An * indicates that a property is required



##### Output Details

UserDetailsModel: Twitter user details


| Property Name | Data Type | Description |
|---|---|---|
|FullName|string|Name of the user|
|Location|string|Location of the user|
|Id|integer|Twitter Id of the user|
|UserName|string|Screen name of the user|
|FollowersCount|integer|Number of followers|
|Description|string|User description|
|StatusesCount|integer|User status count|
|FriendsCount|integer|Number of friends|
|FavouritesCount|integer|Number of tweets that the user has favorited|
|ProfileImageUrl|string|Url of the profile image|




## Get following
The operation gets the list of people the given user follows. 


|Property Name| Data Type|Description|
| ---|---|---|
|userName*|string|Twitter handle for the user|
|maxResults|integer|Maximum number of users to return|

An * indicates that a property is required



##### Output Details

UserDetailsModel: Twitter user details


| Property Name | Data Type | Description |
|---|---|---|
|FullName|string|Name of the user|
|Location|string|Location of the user|
|Id|integer|Twitter Id of the user|
|UserName|string|Screen name of the user|
|FollowersCount|integer|Number of followers|
|Description|string|User description|
|StatusesCount|integer|User status count|
|FriendsCount|integer|Number of friends|
|FavouritesCount|integer|Number of tweets that the user has favorited|
|ProfileImageUrl|string|Url of the profile image|




## Get my following
This operation gets the list of users that I am following. 


|Property Name| Data Type|Description|
| ---|---|---|
|maxResults|integer|Maximum number of users to return|

An * indicates that a property is required



##### Output Details

UserDetailsModel: Twitter user details


| Property Name | Data Type | Description |
|---|---|---|
|FullName|string|Name of the user|
|Location|string|Location of the user|
|Id|integer|Twitter Id of the user|
|UserName|string|Screen name of the user|
|FollowersCount|integer|Number of followers|
|Description|string|User description|
|StatusesCount|integer|User status count|
|FriendsCount|integer|Number of friends|
|FavouritesCount|integer|Number of tweets that the user has favorited|
|ProfileImageUrl|string|Url of the profile image|




## Get user
This operation gets the profile details for a given user, such as user name, description, followers count, and more. 


|Property Name| Data Type|Description|
| ---|---|---|
|userName*|string|Twitter handle for the user|

An * indicates that a property is required

##### Output Details

UserDetailsModel: Twitter user details


| Property Name | Data Type | Description |
|---|---|---|
|FullName|string|Name of the user|
|Location|string|Location of the user|
|Id|integer|Twitter Id of the user|
|UserName|string|Screen name of the user|
|FollowersCount|integer|Number of followers|
|Description|string|User description|
|StatusesCount|integer|User status count|
|FriendsCount|integer|Number of friends|
|FavouritesCount|integer|Number of tweets that the user has favorited|
|ProfileImageUrl|string|Url of the profile image|




## Post a tweet
This operation posts a new tweet. 


|Property Name| Data Type|Description|
| ---|---|---|
|tweetText|string|Text to be posted|
|body|string|Media to be posted|

An * indicates that a property is required

##### Output Details

TweetResponseModel: Model representing Posted Tweet


| Property Name | Data Type | Description |
|---|---|---|
|TweetId|string|ID of the retrieved tweet|




## When a new tweet is posted
This operation triggers a flow when a new tweet that matches a given search query is posted. 


|Property Name| Data Type|Description|
| ---|---|---|
|searchQuery*|string|Search term like "happy hour", #haiku, love OR hate|

An * indicates that a property is required

##### Output Details

TriggerBatchResponse[TweetModel]: undefined


| Property Name | Data Type | Description |
|---|---|---|
|value|array|undefined|



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