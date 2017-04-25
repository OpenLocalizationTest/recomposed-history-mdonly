---
title: postgresql-howto-configure-server-logs-using-cli | Microsoft Docs
description: Describes how to conifgure PostgreSQL service logs.
keywords: azure cloud postgresql postgres
services: postgresql
author:
ms.author:
manager: jhubbard
editor: jasonh

ms.assetid:
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: hero - article
ms.date: 04/30/2017
---
# Configure and access server logs using Azure CLI
You can list and download Azure PostgreSQL server error logs using the Command Line Interface (Azure CLI). However, access to transaction logs is not supported. For more information, see [Azure PostgreSQL Server logs](server-logs/update.me).

## Prerequisites
To step through this how-to guide, you need:
- An [Azure Database for PostgreSQL server](file:///D:/Orcas/Documentation/postgresql-server/update.me)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) command line utility installed

## Configure logging for Azure PostgreSQL server
You can configure the server to access query logs and error logs. Error logs can contain auto-vacuum, connection and checkpoints information.
(needs to update once CLI is live)
1. Turn on logging
2. Update log\_statement and log\_min\_duration\_statement to enable query logging
3. Update retention period

See [customizing server configuration parameters](parameters/update.me) for more information.

## List logs for Azure PostgreSQL server
To list the available log files for your server, run the following command. See [az postgres server-logs](logs-cli/update.me).
```azurecli
az postgres server-logs list --resource-group <resource group
name> --server <server name> \[--file-last-written
--filename-contains --max-file-size\]
```
For example, you can list the log files for Azure PostgreSQL server **mypgserver.postgres.database.azure.com** under Resource Group **myresourcegroup**, and direct it to a text file called **log\_files\_list.txt. **
```azurecli
az postgres server-logs list --resource-group **myresourcegroup**
--server **mypgserver** > log\_files\_list.txt
```
## Download logs locally from the server
You can also download individual log files for your Azure PostgreSQL server. See [az postgres server-logs](logs-cli/update.me).
```azurecli
az postgres server-logs download –name <log file name>
--resource-group <resource group name> --server <server
name>
```
This example downloads the specific log file for Azure PostgreSQL server **mypgserver.postgres.database.azure.com** under Resource Group **myresourcegroup** to your local environment.
```azurecli
az postgres server-logs download --name
20170414-mypgserver-postgresql.log --resource-group **myresourcegroup**
--server **mypgserver**
```
## Next steps
- To learn more about server logs, see [Azure PostgreSQL Server logs](server-logs/update.me).
- For more information on server parameters, see [customizing server configuration parameters](parameters/update.me).

