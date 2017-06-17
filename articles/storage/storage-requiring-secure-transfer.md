﻿---
title: Requiring Secure Transfer in Azure Storage | Microsoft Docs
description: Learn about the Requiring Secure Transfer feature for Azure Storage, and how to enable the feature.
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft

ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/07/2017
ms.author: fryu

---
# Requiring Secure Transfer

The Secure Transfer option enhances the security of your storage account by only allowing requests to the storage account in secure connection. For example, when calling REST APIs to access your storage accounts, you must connect using HTTPs. Any requests using HTTP is rejected when Require Secure Transfer is enabled. When you are using the Azure Files service, connection without encryption fails, including scenarios using SMB 2.1, SMB 3.0 without encryption, and some flavors of the Linux SMB client. 

Because Azure Storage doesn’t support HTTPs for custom domain names, this option is not applied when using a custom domain name.

By default, this option is disabled.

## Enable Secure transfer required in the Azure portal

1. When you create a storage account, you can find the "Secure transfer required" option in the blade.

![screenshot](./media/storage-requiring-secure-transfer/secure_transfer_field_in_portal_en_1.png)

2. If you look for the option for an existing storage account, you can find the "Secure transfer required" option in Configuration under Settings.

![screenshot](./media/storage-requiring-secure-transfer/secure_transfer_field_in_portal_en_2.png)

3. Click **Enabled** to enable this option.

## Next steps
Azure Storage provides a comprehensive set of security capabilities, which together enable developers to build secure applications. For more details, visit the [Storage Security Guide](storage-security-guide.md).
