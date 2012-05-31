1. After you've connected to the virtual machine using Remote Desktop, open Internet Explorer from the **Start** menu.
2. Select the **Tools** button in the upper right corner.  In **Internet Options**, select the **Security** tab, and then select the **Trusted Sites** icon, and finally click the **Sites** button. Add *http://*.mongodb.org* to the list of trusted sites.
3. Go to [Downloads- MongoDB] [MongoDownloads].
4. Find the most recent release in the **Production Release (Recommended)** section and click the ***2008+** link in the Windows 64-bit column.  Click **Save As** and save the zip file to the desktop.
5. Right-click on the zip file and select **Extract All...**  Specify "C:\" and click **Extract**.  After the files have been extracted, you may wish to rename the install folder to something simpler.  "MongoDB", for example.
6. Create a data directory.  By default, MongoDB stores data in the `\data\db` directory but you must manually create the directory.  From **Start**, select **Command Prompt** to open a command prompt window.  Enter:

	`C:\> cd /`

	`C:\> mkdir \data`

	`C:\> mkdir \data\db`
7. To run the database, run: 

	`C:\> cd \MongoDB\bin`

	`C:\my_mongo_dir\bin> mongod`

	You will see log messages displayed in this window as mongod.exe server starts and preallocates journal files. It may take several minutes to preallocate the journal files.
8. To start the MongoDB administrative shell, open another command window from **Start** and enter the following:

	`C:\> cd \my_mongo_dir\bin`  
	`C:\my_mongo_dir\bin> mongo`  
	`>db`  
	`test`  	  
	`> db.foo.insert( { a : 1 } )`  
	`> db.foo.find()`  
	`{ _id : ..., a : 1 }`  
	`> show dbs`  
	`...`  
	`> show collections`  
	`...`  
	`> help`  

	The database is created by the insert.
9. (Optional) mongod.exe has support for installing and running as a Windows service. To install mongod.exe as a service, run the following from the command prompt:

	C:\mongodb\bin>mongod --logpath "c:\mongodb\logs\logfile.log" --logappend --dbpath "c:\data" --install 

	This creates a service named "Mongo DB" with a description of "Mongo DB". The --logpath option must be used to specify a log file, since the running service will not have a command window to display output.  The --logappend option specifies that a restart of the service will cause output to append to the existing log file.  The --dbpath option specifies the location of the data directory. For more service-related command line options, see [Service-related command line options] [MongoWindowsSvcOptions].
10. Now that MongoDB is installed and running you must open a port in Windows Firewall in order to remotely connect to MongoDB.  From the **Start** menu, select **Administrator Tools** and then **Windows Firewall with Advanced Security**. 
11. In the left pane, select **Inbound Rules**.  In the **Actions** pane on the right, select **New Rule...**.
	![Windows Firewall][Image1]

	In the **New Inbound Rule Wizard**, select **Port** and then click **Next**.
	![Windows Firewall][Image2]

	Select **TCP** and then **Specific local ports**.  Specify a port of "27107" (the port MongoDB listens on) and click **Next**.
	![Windows Firewall][Image3]

	Select **Allow the connection** and click **Next**.
	![Windows Firewall][Image4]

	Click **Next** again.
	![Windows Firewall][Image5]

	Specify a name for the rule, such as "MongoPort", and click **Finish**.
	![Windows Firewall][Image6]
	
12. Once MongoDB is installed you must configure an endpoint so that MongoDB can be accessed remotely. In the Management Portal, click **Virtual Machines**, then click the name of your new virtual machine, then click **Endpoints**.
![Endpoints][Image7]
13. Click **Add Endpoint** at the bottom of the page. Select **Add Endpoint** and click **Next**.
	![Endpoints][Image8]

14. Add an endpoint with name "Mongo", protocol **TCP**, and both **Public** and **Private** ports set to "27017". This will allow MongoDB to be accessed remotely.
![Endpoints][Image9]

[MongoDownloads]: http://www.mongodb.org/downloads
[MongoWindowsSvc]: http://www.mongodb.org/display/DOCS/Windows
[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service
[MongoDocs]: http://www.mongodb.org/display/DOCS/Home

[Image1]: ../../Shared/Media/WinFirewall1.png
[Image2]: ../../Shared/Media/WinFirewall2.png
[Image3]: ../../Shared/Media/WinFirewall3.png
[Image4]: ../../Shared/Media/WinFirewall4.png
[Image5]: ../../Shared/Media/\WinFirewall5.png
[Image6]: ../../Shared/Media/WinFirewall6.png
[Image7]: ../../Shared/Media/WinVmAddEndpoint.png
[Image8]: ../../Shared/Media/WinVmAddEndpoint2.png
[Image9]: ../../Shared/Media/WinVmAddEndpoint3.png
