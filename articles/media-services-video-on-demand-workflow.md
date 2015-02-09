<properties 
	pageTitle="Media Services Video-on-Demand Workflow" 
	description="This topic describes steps of a typical Media Services Video-on-Demand workflow." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="juliako"/>


#Media Services Video-on-Demand Workflow

This topic describes steps of a typical Azure Media Services (AMS) Video-on-Demand workflow. Each step links to relevant topics. For tasks that can be achieved using different technologies, there are buttons that link to technology of your choice (for example, .NET or REST).   

Note that you can integrate Media Services with your existing tools and processes. For example, encode content on-site then upload to Media Services for transcoding into multiple formats and deliver through Azure CDN, or a third-party CDN. 

The following diagram shows the major parts of the Media Services platform that are involved in the Video on Demand Workflow.
![VoD workflow][vod-overview]

##Creating a Media Services account

Use **Azure Management Portal** to [Create Azure Media Services Account](../media-services-create-account/).

##Setting up development environment  

Choose **.NET** or **REST API** for your development environment.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##Connecting programmatically  

Choose **.NET** or **REST API** to programmatically connect to Azure Media Services.  

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##Uploading media 

Upload your files using **Azure Management Portal**, **.NET** or **REST API**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

##Processing media

###Get Media Processor

[Get Media Processor using .NET](../media-services-get-media-processor/)

###Indexing

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

###Encoding 

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-encode.md)]

For more information, see [Encoding and Packaging](https://msdn.microsoft.com/en-us/library/azure/dn621224.aspx).

###Monitoring job progress

[Monitoring job progress using .NET](../media-services-check-job-progress/)

##Configuring Content Protection 

For more information, see [Securing Media](https://msdn.microsoft.com/en-us/library/azure/dn282272.aspx).

##Configuring asset delivery policy

Configure asset delivery policy using **.NET** or **REST API**.

[AZURE.INCLUDE [media-services-selector-configure_asset_delivery_policy](../media-services-selector-configure_asset_delivery_policy.md)]

##Publishing assets

Publish assets using **Azure Management Portal**, **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]






[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png