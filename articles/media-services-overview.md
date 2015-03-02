<properties 
	pageTitle="Azure Media Services Overview" 
	description="This topic gives an overview of Azure Media Services" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="juliako"/>

#Azure Media Services Overview

Microsoft Azure Media Services is an extensible cloud-based platform that enables developers to build scalable media management and delivery applications. Media Services is based on REST APIs that enable you to securely upload, store, encode and package video or audio content for both on-demand and live streaming delivery to various clients (for example, TV, PC, and mobile devices).

You can build end-to-end workflows using entirely Media Services. You can also choose to use third-party components for some parts of your workflow. For example, encode using a third-party encoder. Then, upload, protect, package, deliver using Media Services.

To build Media Services solutions, you can use the [Media Services REST API](https://msdn.microsoft.com/en-us/library/azure/hh973617.aspx) or one of the available client SDKs: [Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java), [Azure Media Services for Node.js](https://github.com/fritzy/node-azure-media), [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php). You can also use tools: [Azure Management Portal](http://manage.windowsazure.com/) or [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).

**Service Level Agreement (SLA)**: Media Services guarantees 99.9% availability of REST API transactions for Media Services Encoding. On-Demand Streaming will successfully service requests with a 99.9% availability guarantee for existing media content when at least one Streaming Reserved Unit is purchased. Availability is calculated over a monthly billing cycle. For more information, download the [SLA document](https://www.microsoft.com/en-us/download/details.aspx?id=39302).

The following poster depicts Azure Media Services workflows, from media creation through consumption. You can download the poster from here: [Azure Media Services poster](http://www.microsoft.com/en-us/download/details.aspx?id=38195).

![Overview][overview]

##Choosing Your Media Experience

One of the first steps in sharing video content is deciding what type of experience you want your clients to have. How will your customers be viewing the video content? Will they be connected to the internet? Will they be viewing your content on a computer or a hand-held device? Will your customers expect the video to be in HD? Questions such as these can help you give your customers the best possible experience.

###Accessing video
 
There are four basic ways customers can access videos:

- Offline Viewing 
- Progressive Download
- Streaming\Adaptive Bitrate Streaming

####Offline viewing

To view a video offline a customer will download the entire video onto their computer or device. Videos tend to be quite large so it may take some time for the download to complete. The benefit of offline viewing is you do not need a network connection to view the video once it has been downloaded to your device. 

####Progressive download

Progressive download requires that a customer is connected to the internet and allows them to start viewing the video before the entire video has been downloaded. Both offline viewing and progressive download approaches require that the device the customer is using to view the video has enough storage space to hold the entire video.

####Streaming

Streaming technologies also require an internet connection, but they download a small piece of the video at a time and discard it once it has been displayed. This requires very little storage on the viewing device. The throughput of a network connection can vary, but customers still expect to be able to view videos regardless of network bandwidth. Adaptive bitrate technologies allow video player applications to determine network conditions and select from among several bitrates. When network communication degrades, the client can select a lower bitrate allowing the player to continue to play the video at a lower video quality. As network conditions improve the client can switch to a higher bitrate with improved video quality. Azure Media Services supports two adaptive bitrate technologies. HTTP Live Streaming (HLS) is an adaptive bitrate technology created by Apple. Smooth Streaming is an adaptive bitrate technology created by Microsoft.

###On what Devices

Another decision that needs to be made is what type of devices your customer will be using to view your videos. Media Services provides support for web browsers, smart phones, tablets, XBOX, set-top boxes, and connected TVs.

####Web Browsers

Web browsers can be run on Windows PCs, Macintosh PCs, and Smart Phones. When running on PCs or Macintosh PCs you can take advantage of the large size screen and the large storage capacity. This allows you to stream higher quality videos. Windows PCs or Macintosh PCs can view videos delivered by Media Services by using a native application or an HTML-compatible web browser. Native applications can support Smooth Streaming, Apple HLS, Progressive Download, or offline viewing. HTML5 web pages support Progressive Download.


####Smart Phones

Smart phones have small screens and smaller storage capacities. Streaming is the best choice for these devices. iPhones, Windows Phones, and Android phones are supported. iPhones and Android phones support Smooth Streaming and HLS. Windows Phones support Smooth Streaming.

###Tablets

Tablets have larger screens than smart phones, but till typically have smaller storage capacity. Streaming is the best choice for tablets. Tablets with larger storage capacities can also take advantage of offline viewing as well as Progressive Download.

####XBox

XBox consoles have the benefit of large screens and larger storage capacity, which makes offline, progressive download, and streaming a good fit.
Set-top Boxes and Connected TVs
These devices also have large screen but minimal storage capacity, streaming is the best fit.

###Supported Technologies by Device

The following table shows each type of device and the client technologies supported by  Media Services:
 
<table border="1">
<tr><th>Device</th><th>Technologies</th></tr>
<tr><td>iOS</td><td>Smooth Streaming, Apple HLS, Progressive Download</td></tr>
<tr><td>Windows Phone 8</td><td>Smooth Streaming</td></tr>
<tr><td>Windows RT</td><td>Smooth Streaming</td></tr>
<tr><td>Windows</td><td>Smooth Streaming, Progressive Download</td></tr>
<tr><td>Android Phones</td><td>Smooth Streaming and Apple HLS</td></tr>
<tr><td>XBox</td><td>Smooth Streaming</td></tr>
<tr><td>Macintosh</td><td>Apple HLS, Progressive Download</td></tr>
<tr><td>Set-top Box, Connected TV</td><td>Smooth Streaming, Apple HLS, Progressive Download</td></tr>
</table>


##Common development workflows: Video-on-Demand and Live Streaming

###Video-on-Demand: Deliver streaming content 

1. Upload a high-quality mezzanine file into an asset.
1. Encode to adaptive bitrate MP4 set.
1. Configure asset delivery policy (used by dynamic packaging).
1. (Optionally) Configure content protection and key authorization policy (used by dynamic encryption).
1. Publish the asset.
1. Stream published content. 

###Video-on-Demand: Index content

1. Upload a high-quality mezzanine file into an Asset.
1. Index content.
1. Consume indexed content.

###Video-on-Demand: Deliver progressive download 

1. Upload a high-quality mezzanine file into an asset.
1. Encode to adaptive bitrate MP4 set or a single MP4.
1. Publish the asset.
1. Progressively download content.

[Media Services Video-on-Demand](../media-services-video-on-demand-workflow) contains links to topics that show how to perform tasks mentioned above.

###Live Streaming

1. Create and start a channel.
1. Retrieve the channel ingest URL.
1. Start and configure the live transcoder of your choice.
1. Retrieve the channel’s Preview endpoint and verify that your channel is properly receiving the live stream.
1. Create a program and asset. 
2. Configure delivery policy for the asset (used by dynamic packaging).
3. (Optionally)  Configure content protection and key authorization policy (used by dynamic encryption).
1. To make the stream available to users, create a streaming locator needed to access the asset associate with the program.
1. Start the program when you are ready to start streaming and archiving.
1. Stop the program whenever you want to stop streaming and archiving the event.
1. Delete the Program (and optionally delete the asset).

[Media Services Live Streaming](../media-services-live-streaming-wrokflow) contains links to topics that show how to perform tasks mentioned above.

##Clients

Azure Media Services provides the tools you need to create rich, dynamic client player applications for most platforms including: iOS Devices, Android Devices, Windows, Windows Phone, Xbox, and Set-top boxes.

- [Smooth Streaming Client SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Microsoft Media Platform: Player Framework](http://playerframework.codeplex.com/) 
- [HTML5 Player Framework Documentation](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Microsoft Smooth Streaming Plugin for OSMF](https://www.microsoft.com/en-us/download/details.aspx?id=36057) 
- [Media Player Framework for iOS](https://github.com/Azure/azure-media-player-framework) 
- [Licensing Microsoft® Smooth Streaming Client Porting Kit](https://www.microsoft.com/en-us/mediaplatform/sspk.aspx) 
- Building Video Applications on Windows 8 
- [XBOX Video Application Development](http://xbox.create.msdn.com/en-US/) 

For more information, see [Developing Video Player Applications](../media-services-develop-video-players)

##Patterns & practices guidance

[Patterns and practices guidance](https://wamsg.codeplex.com/)
[Online documentation](https://msdn.microsoft.com/en-us/library/dn735912.aspx)
[Downloadable eBook](https://www.microsoft.com/en-us/download/details.aspx?id=42629)

##Support

[Azure Support](http://azure.microsoft.com/en-us/support/options/) provides support options for Azure, including Media Services.



<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
