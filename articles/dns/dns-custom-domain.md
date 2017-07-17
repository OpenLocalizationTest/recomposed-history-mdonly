---
title: Integrate Azure DNS with your Azure resources | Microsoft Docs
description: Learn how to use Azure DNS along to provide DNS for your Azure resources.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt

ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/07/2017
ms.author: gwallace
---

# Use Azure DNS to provide custom domain settings for an Azure service

You can use Azure DNS to provide DNS for a custom domain for any of your Azure resources that support custom domains or has a fully qualified domain name (FQDN). For example, you are creating an Azure web app and you want your users to access it by either using contoso.com, or www.contoso.com as an FQDN.

## Prerequisites

In order to use Azure DNS for your custom domain, you must first delegate your domain to Azure DNS. Visit [Delegate a domain to Azure DNS](./dns-delegate-domain-azure-dns.md) for instructions on how to configure your name servers for delegation. Once your domain is delegated to your Azure DNS zone, you are able to configure the DNS records needed.

The following are resources that you can configure a vanity or custom domain for using Azure DNS:

|Service  | Configuration  |
|---------|---------|
|[Azure Function App](#azure-function-app)    | A CNAME record added as a hostname for the custom domain         |
|[Azure IoT](#azure-iot)    | A CNAME record to IoT host name        |
|[Public IP address](#public-ip-address)    | An A record to public IP address        |
|[App Service (Web Apps)](#app-service-web-apps)     | A CNAME record added as a hostname for the custom domain          |
|[Blob storage](#blob-storage)    | Use indirect validation with CNAME records.         |
|[Azure CDN](#azure-cdn)    | Use indirect validation with CNAME records. 

## Azure Function App

Navigate to **Other** > **Function App** and select your Function App. Click **Platform features** and under **NETWORKING** click **Custom domains**.

![function app blade](./media/dns-custom-domain/functionapp.png)

Note the current url on the **Custom domains** blade, this address is used as the alias for the DNS record created.

![custom domain blade](./media/dns-custom-domain/functionshostname.png)

Navigate to your DNS Zone and click **+ Record set**. Fill out the following information on the **Add record set** blade and click **OK** to create it.

|Property  |Value  |Description  |
|---------|---------|---------|
|Name     | myfunctionapp        | This value along with the domain name label is the FQDN for the custom domain name.        |
|Type     | CNAME        | Use a CNAME record is using an alias.        |
|TTL     | 1        | 1 is used for 1 hour        |
|TTL unit     | Hours        | Hours are used as the time measurement         |
|Alias     | adatumfunction.azurewebsites.net        | The DNS name you are creating the alias for, in this example it is the adatumfunction.azurewebsites.net DNS name provided by default to the function app.        |

Navigate back to your function app, click **Platform features**, and under **NETWORKING** click **Custom domains**, then under **Hostnames** click **+ Add hostname**.

On the **Add hostname** blade, enter the CNAME record in the **hostname** text field and click **Validate**. If the record was able to be found the **Add hostname** button appears. Click **Add hostname** to add the alias.

![function apps add host name blade](./media/dns-custom-domain/functionaddhostname.png)


## Azure IoT

Navigate to **Internet of Things** > **IoT Hub** and select your IoT hub. On the **Overview** blade, note the FQDN of the IoT hub.

![iot hub blade](./media/dns-custom-domain/iot.png)

Next, navigate to your DNS Zone and click **+ Record set**. Fill out the following information on the **Add record set** blade and click **OK** to create it.


|Property  |Value  |Description  |
|---------|---------|---------|
|Name     | myiothub        | This along with the domain name label is the FQDN for the IoT hub..        |
|Type     | CNAME        | Use a CNAME record is using an alias.
|TTL     | 1        | 1 is used for 1 hour        |
|TTL unit     | Hours        | Hours are used as the time measurement         |
|Alias     | adatumIOT.azure-devices.net        | The DNS name you are creating the alias for, in this example it is the adatumIOT.azure-devices.net host name provided by the IoT hub.

Once the record is created, test name resolution with the CNAME record using `nslookup`

## Public IP address

Navigate to **Networking** > **Public IP address**, select the Public IP resource and click **Configuration**. Notate the IP address shown.

![public ip blade](./media/dns-custom-domain/publicip.png)

Navigate to your DNS Zone and click **+ Record set**. Fill out the following information on the **Add record set** blade and click **OK** to create it.


|Property  |Value  |Description  |
|---------|---------|---------|
|Name     | mywebserver        | This along with the domain name label is the FQDN for the custom domain name.        |
|Type     | A        | Use an A record as the resource is an IP address.        |
|TTL     | 1        | 1 is used for 1 hour        |
|TTL unit     | Hours        | Hours are used as the time measurement         |
|IP Address     | <your ip address>       | The public IP address.|

![create an a record](./media/dns-custom-domain/arecord.png)

Once the A record is created, run `nslookup` to validate the record resolves.

![public ip dns lookup](./media/dns-custom-domain/publicipnslookup.png)

## App Service (Web Apps)

The following steps take you through configuring a custom domain for an app service web app.

Navigate to **Web & Mobile** > **App Service** and select the resource you are configuring a custom domain name for and click **Custom domains**.

Note the current url on the **Custom domains** blade, this address is used as the alias for the DNS record created.

![custom domains blade](./media/dns-custom-domain/url.png)

Navigate to your DNS Zone and click **+ Record set**. Fill out the following information on the **Add record set** blade and click **OK** to create it.


|Property  |Value  |Description  |
|---------|---------|---------|
|Name     | mywebserver        | This along with the domain name label is the FQDN for the custom domain name.        |
|Type     | CNAME        | Use a CNAME record is using an alias. An A record would be used if the resource used an IP address.        |
|TTL     | 1        | 1 is used for 1 hour        |
|TTL unit     | Hours        | Hours are used as the time measurement         |
|Alias     | webserver.azurewebsites.net        | The DNS name you are creating the alias for, in this example it is the webserver.azurewebsites.net DNS name provided by default to the web app.        |


![create cname record](./media/dns-custom-domain/createcnamerecord.png)

Navigate back to your app service that is configured for the custom domain name and click **Custom domains**, then click **Hostnames**. To add the CNAME record you created, click **+ Add hostname**.

![figure 1](./media/dns-custom-domain/figure1.png)

Once the process is complete, run **nslookup** to validate name resolution is working.

![figure 1](./media/dns-custom-domain/finalnslookup.png)

## Blob storage

The following steps take you through configuring a CNAME record for a blob storage account using the asverify method. This method ensures there is no downtime.

Navigate to **Storage** > **Storage Accounts**, select your storage account, and click **Custom domain**. Notate the FQDN under step 2, this is used to create the first CNAME record

![blob storage custom domain](./media/dns-custom-domain/blobcustomdomain.png)

Navigate to your DNS Zone and click **+ Record set**. Fill out the following information on the **Add record set** blade and click **OK** to create it.


|Property  |Value  |Description  |
|---------|---------|---------|
|Name     | asverify.mystorageaccount        | This along with the domain name label is the FQDN for the custom domain name.        |
|Type     | CNAME        | Use a CNAME record is using an alias.        |
|TTL     | 1        | 1 is used for 1 hour        |
|TTL unit     | Hours        | Hours are used as the time measurement         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | The DNS name you are creating the alias for, in this example it is the asverify.adatumfunctiona9ed.blob.core.windows.net DNS name provided by default to the storage account.        |

Navigate back to your storage account by clicking **Storage** > **Storage Accounts**, select your storage account and click **Custom domain**. Type in the alias you created without the asverify prefix in the text box, check **Use indirect CNAME validation, and click **Save**. Once this step is complete return to your DNS zone and create a CNAME record without the asverify prefix.  After that point you are safe to delete the CNAME record with the cdnverify prefix.

![blob storage custom domain](./media/dns-custom-domain/indirectvalidate.png)

Validate DNS resolution by running `nslookup`

## Azure CDN

The following steps take you through configuring a CNAME record for a CDN endpoint using the cdnverify method. This method ensures there is no downtime.

Navigate to **Networking** > **CDN Profiles**, select your CDN profile, and click **Endpoints** under **General**.

Select the endpoint you are working with and click **+ Custom domain**. Note the **Endpoint hostname** as this is the record to point the CNAME record to.

![CDN custom domain](./media/dns-custom-domain/endpointcustomdomain.png)

Navigate to your DNS Zone and click **+ Record set**. Fill out the following information on the **Add record set** blade and click **OK** to create it.


|Property  |Value  |Description  |
|---------|---------|---------|
|Name     | cdnverify.mycdnendpoint        | This along with the domain name label is the FQDN for the custom domain name.        |
|Type     | CNAME        | Use a CNAME record is using an alias.        |
|TTL     | 1        | 1 is used for 1 hour        |
|TTL unit     | Hours        | Hours are used as the time measurement         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | The DNS name you are creating the alias for, in this example it is the cdnverify.adatumcdnendpoint.azureedge.net DNS name provided by default to the storage account.        |

Navigate back to your CDN endpoint by clicking **Networking** > **CDN Profiles**, and select your CDN profile. Click **+ Custom domain** and enter your CNAME record alias without the cdnverify prefix and click **Add**.

Once this step is complete, return to your DNS zone and create a CNAME record without the asverify prefix.  After that point, you are safe to delete the CNAME record with the cdnverify prefix.

## Next steps

Learn how to [configure reverse DNS for services hosted in Azure](dns-reverse-dns-for-azure-services.md).