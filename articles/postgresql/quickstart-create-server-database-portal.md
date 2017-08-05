---
title: 'Azure portal: Create an Azure Database for PostgreSQL | Microsoft Docs'
description: Quick start guide to create and manage Azure Database for PostgreSQL server using Azure portal user interface.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/04/2017
---

# Create an Azure Database for PostgreSQL in the Azure portal

Azure Database for PostgreSQL is a managed service that enables you to run, manage, and scale highly available PostgreSQL databases in the cloud. This quickstart shows you how to create an Azure Database for PostgreSQL server using the Azure portal.

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

## Log in to the Azure portal

Log in to the [Azure portal](https://portal.azure.com).

## Create an Azure Database for PostgreSQL

An Azure Database for PostgreSQL server is created with a defined set of [compute and storage resources](./concepts-compute-unit-and-storage.md). The server is created within an [Azure resource group](../azure-resource-manager/resource-group-overview.md).

Follow these steps to create an Azure Database for PostgreSQL server:
1.	Click the **New** button found on the upper left-hand corner of the Azure portal.
2.	Select **Databases** from the **New** page, and select **Azure Database for PostgreSQL** from the **Databases** page.
 ![Azure Database for PostgreSQL - Create the database](./media/quickstart-create-database-portal/1-create-database.png)

3.	Fill out the new server details form with the following information, as shown on the preceding image:

    Setting|Suggested value|Description
    ---|---|---
    Server name |*mypgserver-20170401*|Choose a unique name that identifies your Azure Database for PostgreSQL server. The domain name *postgres.database.azure.com* is appended to the server name you provide for  applications to connect to. The server name can contain only lowercase letters, numbers, and the hyphen (-) character, and it must contain from 3 through 63 characters.
    Subscription|*Your subscription*|The Azure subscription that you want to use for your server. If you have multiple subscriptions, choose the appropriate subscription in which the resource is billed for.
    Resource Group|*myresourcegroup*| You may make a new resource group name, or use an existing one from your subscription.
    Server admin login |*mylogin*| Make your own login account that to be used when connecting to the server. The admin login name cannot be 'azure_superuser', 'azure_pg_admin', 'admin', 'administrator', 'root', 'guest', or 'public', and cannot start with 'pg_'.
    Password |*Your choice* | Create a new password for the server admin account. Must contain from 8 to 128 characters. Your password must contain characters from three of the following categories – English uppercase letters, English lowercase letters, numbers (0-9), and non-alphanumeric characters (!, $, #, %, etc.).
    Location|*The region closest to your users*| Choose the location that's closest to your users.
    PostgreSQL Version|*Choose the latest version*| Choose the latest version unless you have specific requirements.
    Pricing Tier | **Basic**, **50 Compute Units** **50 GB** | Click **Pricing tier** to specify the service tier and performance level for your new database. Choose Basic tier in the tab at the top. Click the left end of the Compute Units slider to adjust the value to the least amount available for this quickstart. Click **Ok** to save the pricing tier selection. See the following screenshot.
    | Pin to dashboard | Check | Check the **Pin to dashboard** option to allow easy tracking of your server on the front dashboard page of your Azure portal.

  > [!IMPORTANT]
  > The server admin login and password that you specify here are required to log in to the server and its databases later in this quick start. Remember or record this information for later use.

    ![Azure Database for PostgreSQL - pick the pricing tier](./media/quickstart-create-database-portal/2-service-tier.png)

4.	Click **Create** to provision the server. Provisioning takes a few minutes, up to 20 minutes maximum.

5.	On the toolbar, click **Notifications** to monitor the deployment process.
 ![Azure Database for PostgreSQL - See notifications](./media/quickstart-create-database-portal/3-notifications.png)
   
  By default, **postgres** database gets created under your server. The [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) database is a default database meant for use by users, utilities, and third-party applications. 

## Configure a server-level firewall rule

The Azure Database for PostgreSQL service creates a firewall at the server-level. This firewall prevents external applications and tools from connecting to the server and any databases on the server unless a firewall rule is created to open the firewall for specific IP addresses. 

1.	Locate your server after the deployment completes. If needed you can search for it. For example, click **All Resources** from the left-hand menu and type in the server name (such as the example mypgserver-20170401) to search for your newly created server. Click on your server name listed in the search result. The Overview page for your server opens and provides options for further configuration.
 
    ![Azure Database for PostgreSQL - Search for server ](./media/quickstart-create-database-portal/4-locate.png)

2.	On the server page, select **Connection Security**. 
    ![Azure Database for PostgreSQL - Create Firewall Rule](./media/quickstart-create-database-portal/5-firewall-2.png)

3.	Under the **Firewall Rules** heading, click in the blank text box in the **Rule Name** column to begin creating the firewall rule. 

    For this quick start, let's allow all IP addresses into the server by filling in the text box in each column with the following values:

    Rule Name | Start IP | End IP 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. On the upper toolbar of the Connection Security page, click **Save**. Then click the **X** to close the Connection Security page.

    > [!NOTE]
    > Azure PostgreSQL server communicates over port 5432. If you are trying to connect from within a corporate network, outbound traffic over port 5432 may not be allowed by your network's firewall. If so, you will not be able to connect to your Azure SQL Database server unless your IT department opens port 5432.
  >

## Get the connection information

When we created our Azure Database for PostgreSQL server, a default database named **postgres** gets created. To connect to your database server, you need to recall the full server name and admin login credentials. You may have noted those values earlier in the quick start article. In case you did not, easily find the server name and login information from the server Overview page in the Azure portal.

1. Open your server's **Overview** page. Make a note of the **Server name** and **Server admin login name**.
    Hover your cursor over each field, and the copy icon appears to the right of the text. Click the copy icon as needed to copy the values.

 ![Azure Database for PostgreSQL - Server Admin Login](./media/quickstart-create-database-portal/6-server-name.png)

## Connect to PostgreSQL database using psql in Cloud Shell

There are a number of applications you can use to connect to your Azure Database for PostgreSQL server. Let's first use the psql command-line utility to illustrate how to connect to the server.  You can use a web browser and the Azure Cloud Shell as described here without the need to install any additional software. If you have the psql utility installed locally on your own machine, you can connect from there as well.

1. Launch the Azure Cloud Shell via the terminal icon on the top navigation pane.

   ![Azure Database for PostgreSQL - Azure Cloud Shell terminal icon](./media/quickstart-create-database-portal/7-cloud-console.png)

2. The Azure Cloud Shell opens in your browser, enabling you to type bash shell commands.

   ![Azure Database for PostgreSQL - Azure Shell Bash Prompt](./media/quickstart-create-database-portal/8-bash.png)

3. At the Cloud Shell prompt, connect to a database in your Azure Database for PostgreSQL server by typing the psql command line at the green prompt.

    The following format is used to connect to an Azure Database for PostgreSQL server with the [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) utility:
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    For example, the following command connects to an example server:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    psql parameter |Suggested value|Description
    ---|---|---
    --host | *server name* | Specify the server name value that was used when you created the Azure Database for PostgreSQL earlier. Our example server shown is mypgserver-20170401.postgres.database.azure.com. Use the fully qualified domain name (\*.postgres.database.azure.com) as shown in the example. Follow the previous section to get the connection information if you do not remember your server name. 
    --port | **5432** | Always use port 5432 when connecting to Azure Database for PostgreSQL. 
    --username | *server admin login name* |Type in the  server admin login username supplied when you created the Azure Database for PostgreSQL earlier. Follow the previous section to get the connection information if you do not remember the username.  The format is *username@servername*.
    --dbname | **postgres** | Use the default system generated database name *postgres* for the first connection. Later you create your own database.

    After running the psql command, with your own parameter values, you will be prompted to type the server admin password. This is the same password that you provided when you created the server. 

    psql parameter |Suggested value|Description
    ---|---|---
    password | *your admin password* | Note, the typed password characters will not be shown on the bash prompt. Press enter after you have typed all the characters to authenticate and connect.

4.  Once you're connected to the server, create a blank database at the prompt by typing the following command:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

5.  At the prompt, execute the following command to switch connection to the newly created database **mypgsqldb**.
    ```bash
    \c mypgsqldb
    ```

6.  Type \q and then press ENTER to quit psql. You can close the Azure Cloud Shell.

Now you have connected to the Azure Database for PostgreSQL and created a blank user database. Continue to the next section to connect using another common tool pgAdmin.

## Connect to PostgreSQL database using pgAdmin

To connect to Azure PostgreSQL server using the GUI tool _pgAdmin_
1.	Launch the _pgAdmin_ application on your client computer. You can install _pgAdmin_ from http://www.pgadmin.org/.
2.	Choose **Add New Server** from the **Quick Links** menu.
3.	In the **Create - Server** dialog box **General** tab, enter a unique friendly Name for the server, such as **Azure PostgreSQL Server**.
![pgAdmin tool - create - server](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.	In the **Create - Server** dialog box, **Connection** tab, use the settings as specified and click **Save**.
   ![pgAdmin - Create - Server](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)
    - **Host Name/Address**: mypgserver-20170401.postgres.database.azure.com 
        - Fully qualified server name.
    - **Port:**  5432
        - Port number used by this database server is 5432.
    - **Maintenance Database**: postgres 
        - Default system generated database name.
    - **User Name:** mylogin@mypgserver-20170401 
        - The Server admin login (user@mypgserver) obtained earlier in this quickstart.
    - **Password**: The password you chose when you created the server earlier in this quickstart.
    - **SSL Mode**: Require
        - By default, all Azure PostgreSQL servers are created with SSL enforcing turned ON. To turn OFF SSL enforcing, see details in [Enforcing SSL](./concepts-ssl-connection-security.md).
5.	Click **Save**.
6.	In the Browser left pane, expand the **Server Groups**. Choose your server **Azure PostgreSQL Server**.
7.  Choose the **Server** you connected to, and then choose **Databases** under it. 
8.	Right-click on **Databases** to Create a Database.
9.	Choose a database name **mypgsqldb** and the owner for it as server admin login **mylogin**.
10. Click **Save** to create a blank database.
11. In the **Browser**, expand the **Server**. Expand the server you created, and see the database **mypgsqldb** under it.
 ![pgAdmin - Create - Database](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## Clean up resources
Clean up all resources you created in the quickstart by deleting the [Azure resource group](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Other quickstarts in this collection build upon this quick start. If you plan to continue on to work with subsequent quickstarts, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart in the Azure portal.

1.	From the left-hand menu in the Azure portal, click **Resource groups** and then click **myresourcegroup**.
2.	On your resource group page, click **Delete**, type **myresourcegroup** in the text box, and then click Delete.

If you just would like to delete the newly created server:
1.	From the left-hand menu in Azure portal, click PostgreSQL servers and then search for the server you just created
2.	On the Overview page, click the Delete button on the top pane
![Azure Database for PostgreSQL - Delete server](./media/quickstart-create-database-portal/12-delete.png)
3.	Confirm the server name you want to delete, and show the databases under it that is affected. Type **mypgserver-20170401** in the text box, and then click Delete.

## Next steps
> [!div class="nextstepaction"]
> [Migrate your database using Export and Import](./howto-migrate-using-export-and-import.md)
