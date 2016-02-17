<properties 
   pageTitle="How to manage reverse DNS records for your services using the Azure CLI in Resource Manager | Microsoft Azure"
   description="How to manage reverse DNS records for your services  using the Azure CLI in Resource Manager"
   services="DNS"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/16/2016"
   ms.author="joaoma" />

# How to manage reverse DNS records for your services using the Azure CLI

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]

[AZURE.INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Add a reverse DNS record to an existing Public IP address
You can add reverse DNS to an existing Public IP address use the azure network public-ip set.

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

## Create a new Public IP Address with a reverse DNS record
You can add a new Public IP Address with the reverse DNS property specified use the azure network public-ip create.

	azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com. "
 
## View a reverse DNS record for an existing Public IP Address
You can view the configured value for an existing Public IP Address use the azure network public-ip show.

	azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS 

## Remove reverse DNS from existing Public IP Addresses
You can remove a reverse DNS property from an existing Public IP Address azure network public-ip set. This is done by setting the ReverseFqdn property value to blank.

	azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “” 

[[AZURE.INCLUDE [azure-FAQ](../../includes/dns-reverse-dns-record-operations-faq-include.md)]
