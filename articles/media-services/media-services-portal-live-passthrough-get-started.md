<properties 
	pageTitle="How to perform live streaming with on-premise encoders using the Azure portal | Microsoft Azure" 
	description="This tutorial walks you through the steps of creating a Channel that is configured for a pass-through delivery." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article"
 	ms.date="05/31/2016" 
	ms.author="juliako"/>


#How to perform live streaming with on-premise encoders using the Azure portal

This tutorial walks you through the steps of creating a **Channel** that is configured for a pass-through delivery. 


##Prerequisites

The following are required to complete the tutorial:

- An Azure account. For details, see [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/). 
- A Media Services account.	To create a Media Services account, see [How to Create a Media Services Account](media-services-create-account.md).
- A webcam. For example, [Telestream Wirecast encoder](http://www.telestream.net/wirecast/overview.htm).

It is highly recommended to review the following articles:

- [Azure Media Services RTMP Support and Live Encoders](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Overview of Live Steaming using Azure Media Services](media-services-manage-channels-overview.md)
- [Live streaming with on-premise encoders that create multi-bitrate streams](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Common live streaming scenario

The following steps describe tasks involved in creating common live streaming applications that use channels that are configured for pass-through delivery. This tutorial shows how to create and manage a pass-through channel and live events.

1. Connect a video camera to a computer. Launch and configure an on-premises live encoder that outputs a multi-bitrate RTMP or Fragmented MP4 stream. For more information, see [Azure Media Services RTMP Support and Live Encoders](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	This step could also be performed after you create your Channel.

1. Create and start a pass-through Channel.
1. Retrieve the Channel ingest URL. 

	The ingest URL is used by the live encoder to send the stream to the Channel.
1. Retrieve the Channel preview URL. 

	Use this URL to verify that your channel is properly receiving the live stream.

3. Create a live event/program. 

	When using the Azure portal, creating a live event also creates an asset. 
	  
	>[AZURE.NOTE]Make sure to have at least one streaming reserved unit on the streaming endpoint from which you want to stream content.
1. Start the event/program when you are ready to start streaming and archiving.
2. Optionally, the live encoder can be signaled to start an advertisement. The advertisement is inserted in the output stream.
1. Stop the event/program whenever you want to stop streaming and archiving the event.
1. Delete the event/program (and optionally delete the asset).     

>[AZURE.IMPORTANT] Please review [Live streaming with on-premise encoders that create multi-bitrate streams](media-services-live-streaming-with-onprem-encoders.md) to learn about concepts and considerations related to live streaming with on-premise encoders and pass-through channels.

##To view notifications and errors

If you want to view notifications and errors produced by the Azure portal, click on the Notification icon.

![Notifications](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##Configure streaming endpoints 

Media Services provides dynamic packaging which allows you to deliver your multi-bitrate MP4s in the following streaming formats: MPEG DASH, HLS, Smooth Streaming, or HDS, without you having to re-package into these streaming formats. With dynamic packaging you only need to store and pay for the files in single storage format and Media Services will build and serve the appropriate response based on requests from a client.

To take advantage of dynamic packaging, you need to get at least one streaming unit for the streaming endpoint from which you plan to delivery your content.  

To create and change the number of streaming reserved units, do the following:

1. In the **Settings** window, click **Streaming endpoints**. 

2. Click on the default streaming endpoint. 

	The **DEFAULT STREAMING ENDPOINT DETAILS** window appears.

3. To specify the number of streaming units, slide the **Streaming units** slider.

	![Streaming units](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Click the **Save** button to save your changes.

	>[AZURE.NOTE]The allocation of any new units can take up to 20 minutes to complete.
	
##Create and start a pass-through channel

This sections shows how to use the **Quick Create** option to create a pass-through channel.

For more details about pass-through channels, see [Live streaming with on-premise encoders that create multi-bitrate streams](media-services-live-streaming-with-onprem-encoders.md).

1. In the **Settings** window, click **Live streaming**. 

	![Getting started](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)

2. In the **CREATE A NEW CHANNEL** window, click **Quick Create**. 

	This will create a pass-through channel with the RTMP ingest protocol.

	![Created](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

	The channel also adds, starts and publishes a default live event/program. This event is configured to have 8 hours of archive window. 
3. To watch the event, click **Watch** in the Azure portal or copy the streaming URL and use a player of your choice. 
 
	![Created](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

	Live event will automatically convert to on-demand content when stopped.

##Stop and delete events and channels

For more details about pass-through channels, see [Live streaming with on-premise encoders that create multi-bitrate streams](media-services-live-streaming-with-onprem-encoders.md).

- A channel can be stopped only when all events/programs on the channel have been stopped.
- A channel can be deleted only when all programs on the channel have been deleted.

1. Select an event and press **Stop** and then **Delete**.

	Do it for all events in this channel.  
2. Stop and delete the channel.

##Media Services learning paths

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Provide feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
