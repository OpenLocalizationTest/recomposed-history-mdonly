<properties linkid="web-sites-restore" urlDisplayName="Restore a Windows Azure web site" pageTitle="Restore a Windows Azure web site" metaKeywords="Windows Azure Web Sites, Restore, restoring" description="Learn how to restore your Windows Azure web sites from backup." metaCanonical="" services="web-sites" documentationCenter="" title="Restore a Windows Azure web site" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

#Restore a Windows Azure web site

This article shows you how to restore a web site that you have previously backed up by using the Windows Azure Web Sites Backup feature. For more information, see [Windows Azure Web Sites Backups](http://www.windowsazure.com/en-us/documentation/articles/web-sites-backup/). 

The Windows Azure Web Sites Restore feature lets restore your web site on-demand to a previous state, or create a new web site based on one of your original site's backups. Creating a new web site that runs in parallel to the latest version can be useful for A/B testing.

The Restore feature, available on the Backups tab in the Windows Azure Web Sites portal, is available only in Standard mode.

##In this article
- [To Restore a Windows Azure web site from a previously made backup](#PreviousBackup)
- [To Restore a Windows Azure web site directly from a storage account](#StorageAccount)
- [Choose Your Web Site Restore Settings and Start the Restore Operation](#RestoreSettings)
- [View the Operation Logs](#OperationLogs)


<a name="PreviousBackup"></a>
##To Restore a Windows Azure web site from a previously made backup

1. On the **Backups** tab, click **Restore Now** in the command bar at the bottom of the portal page. The **Restore Now** dialog box appears.
	
	![Choose backup source][ChooseBackupSource]
	
2. Under **Choose backup source**, select **Previous Backup for this Web Site**.
3. Select the date of the backup that you want to restore, and then click the right arrow to continue.
4. Follow the steps in the [Choose Your Web Site Restore Settings](#RestoreSettings) section later in this article.

<a name="StorageAccount"></a>
##To Restore a Windows Azure web site directly from a storage account

1. On the **Backups** tab, click **Restore Now** in the command bar at the bottom of the portal page. The **Restore Now** dialog box appears.
	
	![Choose backup source][ChooseBackupSource]
	
2. Under **Choose backup source**, select **Storage Account File**. Here you can directly specify the URL for the storage account file, or click the folder icon to navigate to blob storage and specify the backup file. This example chooses the folder icon.
	
	![Storage Account File][StorageAccountFile]
	
3. Click the folder icon to open the **Browse Cloud Storage** dialog box.
	
	![Browse Cloud Storage][BrowseCloudStorage]
	

4. Expand the name of the storage account that you want to use, and then select **websitebackups**, which contains your backups.
5. Select the zip file containing the backup that you want to restore, and then click **Open**.
6. The Storage account file has been selected and shows in the storage account box. Click the right arrow to continue.
	
	![Storage Account File Selected][StorageAccountFileSelected]
	
7. Continue with the section that follows, [Choose Your Web Site Restore Settings and Start the Restore Operation](#RestoreSettings).

<a name="RestoreSettings"></a>
##Choose Your Web Site Restore Settings and Start the Restore Operation
1. Under **Choose your web site restore settings**, **Restore To**, select either **Current web site** or **New web site instance**.
	
	![Choose your web site restore settings][ChooseRestoreSettings]
	
	If you select **Current web site**, your existing web site will be overwritten by the backup that you selected (destructive restore). All changes you have made to the web site since the time of the chosen backup will be permanently removed, and the restore operation cannot be undone. During the restore operation, your current web site will be temporarily unavailable, and you will be warned to this effect.
	
	If you select **New web site instance**, a new web site will be created in the same region with the name that you specify. (By default, the new name is **restored-***oldWebSiteName*.) 
	
	The site that you restore will contain the same content and configuration that were made in the portal for the original site. It will also include any databases that you choose to include in the next step.
2. If you want to restore a database along with your web site, under **Included Databases**, select the name of the database server that you want to restore the database to by using the dropdown under **Restore To**. You can also choose to create a new database server to restore to, or choose **Don't Restore** to not restore the database, which is the default. 
	
	After you have chosen the server name, specify the name of the target database for the restore in the **Database Name** box.
	
	If your restore includes one or more databases, you can select **Automatically adjust connection strings** to update your connection strings stored in the backup to point to your new database, or database server, as appropriate. You should verify that all functionality related to databases works as expected after the restore completes.
	
	![Choose database server host][ChooseDBServer]
	
	> [WACOM.NOTE] You cannot restore a SQL database with the same name to the same SQL Server. You must choose either a different database name or a different SQL Server host to restore the database to. 
	
	> [WACOM.NOTE] You can restore a MySQL database with the same name to the same server, but be aware that this will clear out the existing content stored in the MySQL database.	
	
3. If you choose to restore an existing database, you will need to provide a user name and password. If you choose to restore to a new database, you will need to provide a new database name:
	
	![Restore to a new SQL database][RestoreToNewSQLDB]
	
	Click the right arrow to continue.	
4. If you chose to create a new database, you will need to provide credentials and other initial configuration information for the database in the next dialog. The example here shows a new SQL database. (The options for a new MySQL database are somewhat different.)
	
	![New SQL database settings][NewSQLDBConfig]
	
5. Click the check mark to start the restore operation. When it completes, the new web site instance (if that is the restore option you chose) will be visible in the list of web sites in the portal.
	
	![Restored Contoso web site][RestoredContosoWebSite]

<a name="OperationLogs"></a>
##View the Operation Logs
	
1. To see details about the success or failure of the web site restore operation, go to the web site's Dashboard tab. In the **Quick Glance** section, under **Management Services**, click **Operation Logs**.
	
	![Dashboard - Operation Logs Link][DashboardOperationLogsLink]
	
2. You are taken to the Management Services portal **Operation Logs** page, where you can see the log for your restore operation in the list of operation logs:
	
	![Management Services Operation Logs page][ManagementServicesOperationLogsList]
	
3. To view details about the operation, select the operation in the list, and then click the **Details** button in the command bar.
	
	![Details Button][DetailsButton]
	
	When you do so, the **Operations Details** window opens and shows you the copiable contents of the log file:
	
	![Operation Details][OperationDetails]
	

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
