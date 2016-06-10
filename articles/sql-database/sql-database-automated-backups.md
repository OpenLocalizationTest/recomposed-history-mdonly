<properties
   pageTitle="Cloud business continuity - Builit-in backup - SQL Database | Microsoft Azure"
   description="Learn about SQL Database builit-in backups that enable you to roll back an Azure SQL Database to a previous point in time or copy a database to a new database in an geographic region (up to 35 days)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-features"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Overview: SQL Database automated backups

The Azure SQL Database service protects all databases with an automated backup that is retained for 7 days for Basic, 14 days for Standard, and 35 days for Premium. You can use these automated backup to perform point-in-time restores and to restore a deleted database after accidental data corruption or deletion.

The database backups are taken automatically with no need to opt-in and no additional charges. These automated backups and point-in-time restore provide a zero-cost, zero-admin way to protect databases from accidental corruption or deletion, whatever the cause.

## Understanding automatic backups

All Basic, Standard, and Premium databases are protected by automatic backups. Full backups are taken every week, differential backups every day, and log backups every 5 minutes. The first full backup is scheduled immediately after a database is created. Normally this completes within 30 minutes but it can take longer. If a database is already big, for example if it is created as the result of a database copy or restore from a large database, then the first full backup may take longer to complete. After the first full backup all further backups are scheduled automatically and managed silently in the background. Exact timing of full and differential backups is determined by the system to balance overall load. Backup files are stored in a geo-redundant storage account with read access (RA-GRS) to ensure availability for disaster recovery purposes.

## Additional resources

- [Business Continuity Overview](sql-database-business-continuity.md)
- [Geo-Restore](sql-database-geo-restore.md)
- [Active-Geo-Replication](sql-database-geo-replication-overview.md)
- [Designing applications for cloud disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md)