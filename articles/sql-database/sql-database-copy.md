---
title: Copy an Azure SQL database | Microsoft Docs
description: Create a copy of an Azure SQL database
services: sql-database
documentationcenter: ''
author: anosov1960
manager: jhubbard
editor: ''

ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# Copy an Azure SQL database

You can create the database copy on either the same server or a different server. The copy of the database is a snapshot of the source database as of the time of the copy request. The service tier and performance level (pricing tier) of the database copy are the same as the source database by default. When using the API, you can select a different performance level within the same service tier (edition). After the copy is complete, the copy becomes a fully functional, independent database. At this point, you can upgrade or downgrade it to any edition. The logins, users, and permissions can be managed independently.  

When you copy a database to the same logical server, the same logins can be used on both databases. The security principal you use to copy the database becomes the database owner (DBO) on the new database. All database users, their permissions, and their security identifiers (SIDs) are copied to the database copy.  

When you copy a database to a different logical server, the security principal on the new server becomes the database owner on the new database. If you use [contained database users](sql-database-manage-logins.md) for data access, ensure that both the primary and secondary databases always have the same user credentials, so after the copy completes you can immediately access it with the same credentials. If you use [Azure Active Directory](../active-directory/active-directory-whatis.md), you can completely eliminate the need for managing credentials in the copy. However, when you copy the database to a new server, the login-based access may not work because the logins do not exist on the new server. See [How to manage Azure SQL database security after disaster recovery](sql-database-geo-replication-security-config.md) to learn about managing logins when copying a database to a different logical server. 

You can copy a SQL database using the [Azure portal](sql-database-copy-portal.md), [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md). or [T-SQL](sql-database-copy-transact-sql.md). 

## Next steps

* See [How to manage Azure SQL database security after disaster recovery](sql-database-geo-replication-security-config.md) to learn about managing users and logins when copying a database to a different logical server.
* For information on logins, see  [Manage logins](sql-database-manage-logins.md)
* To export a datagase, see [Export the database to a BACPAC](sql-database-export.md)
