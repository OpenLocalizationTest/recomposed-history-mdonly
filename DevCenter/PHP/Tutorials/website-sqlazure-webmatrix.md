#Create and Deploy a PHP-SQL Azure Windows Azure Website using WebMatrix

This tutorial shows you how to use WebMatrix to develop and deploy a PHP-SQL Azure application to a Windows Azure Website. WebMatrix is a free web development tool from Microsoft that includes everything you need for website development. WebMatrix supports PHP and includes intellisense for PHP development. 

This tutorial assumes you have [SQL Server Express][install-SQLExpress] installed on your computer so that you can test an application locally. However, you can complete the tutorial without having SQL Server Express installed. Instead, you can deploy your application directly to Windows Azure Websites.

Upon completing this guide, you will have a PHP-SQL Azure website running in Windows Azure.
 
You will learn:

* How to create a Windows Azure Website and a SQL Azure database using the Windows Azure Developer Portal. Because PHP is enabled in Windows Azure Websites by default, nothing special is requried to run your PHP code.
* How to develop a PHP application using WebMatrix.
* How to publish and re-publish your application to Windows Azure using WebMatrix.
 
By following this tutorial, you will build a simple Tasklist web application in PHP. The application will be hosted in a Windows Azure Website. A screenshot of the running application is below:

![Windows Azure PHP Website][running-app]

##Prerequisites

1. Download the Tasklist application files from here: [https://github.com/brian-swan/tasklist-sqlazure][tasklist-sqlazure-download]. The Tasklist application is a simple PHP application that allows you to add, mark complete, and delete items from a task list. Task list items are stored in a SQL Azure database (SQL Server Express for local testing). The application consists of these files:

* **index.php**: Displays tasks and provides a form for adding an item to the list.
* **additem.php**: Adds an item to the list.
* **getitems.php**: Gets all items in the database.
* **markitemcomplete.php**: Changes the status of an item to complete.
* **deleteitem.php**: Deletes an item.
* **taskmodel.php**: Contains functions that add, get, update, and delete items from the database.
* **createtable.php**: Creates the SQL Azure table for the application. This file will only be called once.

2. Create a SQL Server database called `tasklist`. You can do this from the `sqlcmd` command prompt with these commands:

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	This step is only necessary if you want to test your application locally.

<h2 id="CreateWebsite">Create a Windows Azure Website and SQL Azure Database</h2>
Follow these steps to create a Windows Azure Website and a SQL Azure database:

1. Login to the Windows Azure portal. **TODO: provide link**
2. Click the **+ New** icon on the bottom left of the portal.

![Create New Windows Azure Website][new-website]

3. Click **Web Site**, then **Custom Create**. Enter a value for **URL**, select **Create a New SQL Azure Database** from the **DATABASE** dropdown,  and select the data center for your website in the **REGION** dropdown. Click the arrow at the bottom of the dialog.

![Custom Create a new Website][custom-create]

![Fill in Website details][website-details-sqlazure]

4. Enter a value for the **NAME** of your database, select the **EDITION** (WEB or BUSINESS), select the **MAX SIZE** for your database, choose the **COLLATION**, and select **NEW SQL Azure server**. Click the arrow at the bottom of the dialog.

	![Fill in SQL Azure database settings][database-settings]

5. Enter an administrator name and password (and confirm the password), choose the region in which your new SQL Azure server will be created, and check the `Allow Windows Azure Services to access this server` box.

	![Create new SQL Azure server][create-server]

	When the website has been created you will see the text **Creation of Web Site ‘[SITENAME]’ completed successfuly**. Next, you will get the database connection information.

1. Click the the website's name, then click **LINKED RESOURCES**, then the database's name.

	![Linked Resources][linked-resources]

2. Click **Connection String**.

	![Connection string][connection-string]
	
From the **PHP** section of the resulting dialog, make note of the values for `UID`, `PWD`, `Database`, and `$serverName`. You will use this information later.

	**TODO: Insert screenshot?**

##Install WebMatrix

You can install WebMatrix from the Windows Azure Portal. From your website's dashboard, click **QUICKSTART** near the top of the page, then click **Install WebMatrix**.

![Install WebMatrix][install-webmatrix]

Run the downloaded .exe file. This will install the Microsoft Web Platform Installer, launch it, and select WebMatrix for installation. Follow the prompts to complete the installation.

##Develop Your Application

In the next few steps you will develop the Tasklist application by adding the files you downloaded earlier and making a few modifications. You could, however, add your own existing files or create new files.

1. Launch WebMatrix by clicking the Windows **Start** button, then click **All Programs>Microsoft Web Matrix>Microsoft WebMatrix**:

	![Launch Webmatrix][launch-webmatrix]

2. Create a new project by clicking **Templates**, then **PHP** (in the left pane), and finally **Empty Site**. Fill in name of site (tasklist), click **Next**.

	**TODO: Insert 3 screenshots, one each for Templates, PHP, and Empty Site**

3. In the lower left corner, click **Files**, then delete `index.php` from the project.

	**TODO: Insert 2 screenshots, one for Files and one for deleting index.php**

4. To import the files you downloaded earlier, click **Add Existing**, navigate to the directory where you saved the Tasklist application files, select them, and add them to the project.

	**TODO: Insert screenshot**

5. Next, you need to add your local SQL Express database connection information to the `taskmodel.php` file. Open the  `taskmodel.php` file by double clicking it, and update the database connection information in the `connect` function. (**Note**: Jump [Publish Your Application](#Publish) if you do not want to test your application locally and want to instead publish directly to Windows Azure Websites.)

		// DB connection info
		$host = ".\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Save the `taskmodel.php` file.

6. For the application to run, the `items` table needs to be created. Right click the `createtable.php` file and select **Launch in browser**. This will launch `createtable.php` in your browser and execute code that creates the `items` table in the `tasklist` database.

	**TODO: Insert screenshot**

7. Now you can test the application locally. Right click the `index.php` file and select **Launch in browser**. Test the application by adding items, marking them complete, and deleting them.  


<h2 id="Publish">Publish Your Application</h2>

Before publishing your application to Windows Azure Websites, the database connection information in `taskmodel.php` needs to be updated with the connection information you obtained earlier (in the [Create a Windows Azure Website and SQL Azure Database](#CreateWebsite) section).

1. Open the `taskmodel.php` file by double clicking it, and update the database connection information in the `connect` function.

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the website";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Return to the Windows Azure Portal and navigate to your website's **DASHBOARD**. Click the **Download publish profile** link at the bottom right corner of the page:

	![Download publish profile][download-publish-profile]

	Make note of where you save this file.

3. In WebMatrix, click the **Publish** icon.

	**TODO: Insert screenshot**

4. In the dialog box that opens, click **Import publish settings**.

	**TODO: Insert screenshot**

	Navigate the the `.publishsettings` file that you saved in the previous step, import it, and click **Save**. You will be asked to allow WebMatrix to upload files to your site for compatibility testing. Choose to allow WebMatrix to do this.

5. Click **Continue** on the **Publish Compatibility** dialog.

	**TODO: Insert screenshot**

6. Click **Continue** on the **Publish Preveiw** dialog.

	**TODO: Insert screenshot**

	When the publishing is complete, you will see **Publishing - Complete** at the bottom of the WebMatirx screen.

7. Navigate to http://[your website name].windows.net/createtable.php to create the `items` table.

8. Lastly, navigate to http://[your website name].windows.net/index.php to being using the running application.
	
##Modify and Republish Your Application

You can easily modify and republish your application. Here, you will make a simple change to the heading in in the `index.php` file, and republish the application.

1. Open the `index.php` file by double-clicking it.

2. Change 'My ToDo List` to `My Task List` in the `h1` tag and save the file.

3. Click the **Publish** icon, the click **Continue** in the **Publish Preview** dialog.

4. When publishing has completed, navigate to http://[your website name].windows.net/index.php to see the published changes.

[install-SQLExpress]: http://www.microsoft.com/en-us/download/details.aspx?id=29062
[running-app]: ../../Shared/Media/running_app.jpg
[tasklist-sqlazure-download]: https://github.com/brian-swan/tasklist-sqlazure
[install-webmatrix]: ../../Shared/Media/install_webmatrix_from_site_dashboard.jpg
[launch-webmatrix]: ../../Shared/Media/launch_webmatrix.jpg
[download-publish-profile]: ../../Shared/Media/download_publish_profile.jpg
[new-website]: ../../Shared/Media/new_website.jpg
[custom-create]: ../../Shared/Media/custom_create.jpg
[website-details-sqlazure]: ./Media/website_details_sqlazure.jpg
[database-settings]: ./Media/database_settings.jpg
[create-server]: ./Media/create_server.jpg
[linked-resources]: ./Media/linked_resources.jpg
[connection-string]: ./Media/connection_string.jpg
[install-webmatrix]: ../../Shared/Media/install_webmatrix_from_site_dashboard.jpg
[launch-webmatrix]: ../../Shared/Media/launch_webmatrix.jpg
[download-publish-profile]: ../../Shared/Media/download_publish_profile.jpg