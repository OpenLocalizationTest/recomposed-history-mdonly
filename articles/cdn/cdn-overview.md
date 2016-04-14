<properties
	pageTitle="Azure CDN Overview"
	description="Learn what the Azure Content Delivery Network (CDN) is and how to use it to deliver high-bandwidth content by caching blobs and static content."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/15/2016" 
	ms.author="casoper"/>

# Overview of the Azure Content Delivery Network (CDN)

The Azure Content Delivery Network (CDN) caches Azure blobs and static content used by web sites at strategically placed locations to provide maximum bandwidth for delivering content to users.

The CDN offers developers a global solution for delivering high-bandwidth content by caching the content at physical nodes across the world. For a current list of CDN node locations, see [Azure Content Delivery Network (CDN) POP Locations](cdn-pop-locations.md).

The benefits of using the CDN to cache Azure data include:

- Better performance and user experience for end users who are far from a content source, and are using applications where many "internet trips" are required to load content
- Large distributed scale to better handle instantaneous high load, like at the start of a product launch event.


## How it works

![CDN Overview](./media/cdn-overview/cdn-overview.png)

1. A user (Alice) requests a file (also called an asset) using a URL with a special domain name, such as `<endpointname>.azureedge.net`.  DNS routes the request to the best performing Point-of-Presence (POP) location.  Usually this is the POP that is geographically closest to the user.
2. If the edge servers in the POP do not have the file cached locally, the edge server requests the file from the origin.  The origin can be an Azure Web App, Azure Cloud Service, Azure Storage account, or any publicly accessible web server.
3. The origin returns the file to the edge server, including optional HTTP headers describing the file's Time-to-Live (TTL).
4. The edge server caches the file locally and returns the file to the original requestor (Alice).  The file will remain cached on the edge server until the TTL expires.
5. Additional users (Bob) may then request the same file using that same URL, and may also be directed to that same POP.
6. If the TTL for the file has not expired, the edge server returns the file from the cache. 

## Standard features

The Standard CDN tier includes these features:

- Easy integration with Azure services such as [Storage](cdn-create-a-storage-account-with-cdn.md), Web Apps, and Media Services
- [Query string caching](cdn-query-string.md)
- [Custom domain name support](cdn-map-content-to-custom-domain.md)
- [Country filtering](cdn-restrict-access-by-country.md)
- [Core analytics](cdn-analyze-usage-patterns.md)
- [Custom content origins](cdn-how-to-use-cdn.md#caching-content-from-custom-origins)
- [HTTPS support](cdn-how-to-use-cdn.md#accessing-cached-content-over-https)
- Load balancing
- DDOS protection
- [Fast purge](cdn-purge-endpoint.md)
- [Asset pre-loading](cdn-preload-endpoint.md)
- [Management via REST API](https://msdn.microsoft.com/library/mt634456.aspx)


## Premium features

The Premium CDN tier includes all of the features of the Standard tier, plus these additional features:

- [Customizable, rule-based content delivery engine](cdn-rules-engine.md)
- [Advanced HTTP reports](cdn-advanced-http-reports.md)
- [Real-time stats](cdn-real-time-stats.md)

## Next steps

To get started with CDN, see [Using Azure CDN](./cdn-how-to-use-cdn.md).

If you are an existing CDN customer, you can now manage your CDN endpoints through the [Microsoft Azure Portal](https://portal.azure.com).
