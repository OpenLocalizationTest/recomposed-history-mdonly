---
title: 'NoSQL database encryption at rest: Azure DocumentDB | Microsoft Docs'
description: Learn how Azure DocumentDB provides default encryption of all NoSQL data
services: documentdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: ''

ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm

---

# DocumentDB NoSQL database encryption at rest

Encryption at rest is a phrase that commonly refers to the encryption of data on nonvolatile storage devices, such as solid-state drives (SSDs) and hard disk drives (HDDs). Azure DocumentDB stores its primary databases on SSDs. Its media attachments and backups are stored in Azure Blob storage, which is generally backed up by HDDs. With the release of encryption at rest for DocumentDB, all your databases, media attachments, and backups are encrypted. Your data is now encrypted in transit (over the network) and at rest (nonvolatile storage), giving you end-to-end encryption.

As a PaaS service, DocumentDB is easy to use. Because all user data stored in DocumentDB is encrypted at rest and in transport, you don't have to take any action. Another way to put this is that encryption at rest is "on" by default. There are no controls to turn it off or on. We have provided this feature while continuing to meet our [availability and performance SLAs](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/).

## Implement encryption at rest

Encryption at rest is implemented by using a number of security technologies, including secure key storage systems, encrypted networks, and cryptographic APIs. Systems that decrypt and process data have to communicate with systems that manage keys. The diagram shows how storage of encrypted data and the management of keys is separated. 

![Design diagram](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

Here is the basic flow of a user request:
- The user database account is made ready, and storage keys are retrieved via a request to the Management Service Resource Provider.
- A user creates a connection to DocumentDB via HTTPS/secure transport. (The SDKs abstract the details.)
- The user sends a JSON document to be stored over the previously created secure connection.
- The JSON document is indexed unless the user has turned off indexing.
- Both the JSON document and index data are written to secure storage.
- Periodically data is read from the secure storage and backed up to the Azure Encrypted Blob Store.

## Frequently asked questions

### Q: How much more does Azure Storage cost if SSE is enabled?
A: There is no additional cost.

### Q: Who manages the encryption keys?
A: The keys are managed by Microsoft.

### Q: How often are encryption keys rotated?
A: Microsoft has a set of internal guidelines for encryption key rotation, which DocumentDB follows. The specific guidelines are not published. Microsoft does publish the [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), which is seen as a subset of internal guidance and has useful best practices for developers.

### Q: Can I use my own encryption keys?
A: DocumentDB is a PaaS service, and we have worked hard to keep the service easy to use. We have noticed this question is often asked as a proxy question for meeting a compliance like PCI-DSS. As part of building this feature, we have worked with compliance auditors to ensure that customers using DocumentDB meet their requirements without needing to manage keys themselves. As a result, we currently do not offer users the option to burden themselves with key management.

### Q: What regions have encryption turned on?
A: All DocumentDB regions have encryption turned on for all user data.

### Q: Does encryption affect the performance latency and throughput SLAs?
A: There is no impact or changes to the performance SLAs now that encryption at rest is enabled for all existing and new accounts. You can read more on the [SLA for DocumentDB](https://azure.microsoft.com/support/legal/sla/documentdb) page to see the latest guarantees.

### Q: Does the local emulator support encryption at rest?
A: The emulator is a standalone dev/test tool and does not use the key management services that the managed DocumentDB service uses. Our recommendation is to enable BitLocker on drives where you are storing sensitive emulator test data. The [emulator supports changing the default data directory](https://docs.microsoft.com/azure/documentdb/documentdb-nosql-local-emulator) as well as using a well-known location.

## Next steps

For an overview of DocumentDB security and the latest improvements, see [DocumentDB NoSQL database security](documentdb-nosql-database-security.md).

For more information about Microsoft certifications, see the [Azure Trust Center](https://azure.microsoft.com/en-us/support/trust-center/).
