<properties
	pageTitle="Install MongoDB on a Windows VM | Microsoft Azure"
	description="Learn how to install MongoDB on an Azure VM running Windows Server 2012 R2 created with the Resource Manager deployment model."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/04/2016"
    ms.author="iainfou"/>

# Install and Configure MongoDB on a Windows VM in Azure
[MongoDB](http://www.mongodb.org) is a popular open-source, high-performance NoSQL database. This article guides you installing and configuring MongoDB on a Windows Server 2012 RV virtual machine (VM) in Azure created using the Resource Manager deployment model.


## Prerequisites

Before you install and configure MongoDB you create a VM and, ideally, add a data disk to it. See the following articles to create a VM and add a data disk:

- [Create a Windows Server VM using the Azure portal](virtual-machines-windows-hero-tutorial.md)
	- Or, [create a Windows Server VM using Azure PowerShell](virtual-machines-windows-ps-create.md)
- [Attach a data disk to a Windows Server VM using the Azure portal](virtual-machines-windows-attach-disk-portal.md)
	- Or, [attach a data disk to a Windows Server VM using Azure PowerShell](https://msdn.microsoft.com/library/mt603673.aspx)

[Log on to your Windows Server VM](virtual-machines-windows-connect-logon.md) using Remote Desktop to begin installing and configuring MongoDB.


## Install and run MongoDB

> [AZURE.IMPORTANT] MongoDB security features, such as authentication and IP address binding, are not enabled by default. Security features should be enabled before deploying MongoDB to a production environment.  For more information, see [Security and Authentication](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. After you've connected to your VM using Remote Desktop, open Internet Explorer from the **Start** menu on the VM.

2. Select 'Use recommended security, privacy, and compatibility settings' when Internet Explorer first opens and click 'OK'.

3. Select the **Tools** button in the upper right corner.  In **Internet Options**, select the **Security** tab, and then select the **Trusted Sites** icon, and finally click the **Sites** button. Add _https://\*.mongodb.org_ to the list of trusted sites, then close the dialog box.

4. Browse to the [MongoDB - Downloads](http://www.mongodb.org/downloads) page (http://www.mongodb.org/downloads).

5. By default, it should select the **Community Server** edition and the latest current stable release for Windows Server 2008 R2 64-bit and later. To download the installer, click the **DOWNLOAD (msi)** button:

	![Download MongoDB installer][./media/virtual-machines-windows-install-mongodb/download-mongodb.png]

	Run the installer once the download is complete.

6. Read and accept the license agreement. When prompted, select **Complete** install.

7. On the final screen, click **Install**

8. The PATH variables are not updated by the MongoDB installer. Right-click the Start menu and select **System**. Click **Advanced System Settings** and then **Environment Variables**. Under **System variables**, select **Path** and then click **Edit**.

	Add the path to your MongoDB `bin` folder. MongoDB is typically installed on C:\Program Files\MongoDB. The following example adds the location to the `PATH` variable:

	```
	;C:\Program Files\MongoDB\Server\3.2\bin
	```

	> [AZURE.NOTE] Be sure to add the leading semicolon (`;`) to indicate that you are adding a location to your `PATH` variable.

9. Create MongoDB data and log directories on your data disk (drive **F:**, for example). From **Start**, select **Command Prompt** to open a command prompt window.  Type the following set of commands:

	```
	mkdir F:\MongoData
	mkdir F:\MongoLogs
	```

10. Start a MongoDB instance with the following command, adjusting the path to your data and log directories accordingly:

	```
	mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
	```

	All log messages are directed to the *F:\MongoLogs\mongolog.log* file as mongod.exe server starts and preallocates journal files. It may take several minutes for MongoDB to preallocate the journal files and start listening for connections. The command prompt stays focused on this task while your MongoDB instance is running.

11. For a more robust MongoDB experience and usage, install mongod.exe as a service. Creating a service means you don't need to leave a command prompt running each time you wish to use MongoDB. Create the service as follows, adjusting the path to your data and log directories accordingly:

	```
	mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
		--logappend  --install
	```

	The preceding command creates a service named MongoDB with a description of "Mongo DB". The `--logpath` option must be used to specify a log file, since the running service does not have a command window to display output. The `--logappend` option specifies that a restart of the service causes output to append to the existing log file. The `--dbpath` option specifies the location of the data directory. For more information about creating the MongoDB service, see [Configure a Windows Service for MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

	To start the MongoDB service, run the following command:

	```
	net start MongoDB
	```

12. With the MongoDB service running as a single instance or installed as a service, you can now start creating and using your databases. To start the MongoDB administrative shell, open another command window from **Start** and enter the following command:

	```
	mongo  
	```

	You can list the database with the `db` command. Insert some data as follows:

	```
	db.foo.insert( { a : 1 } )
	```

	Search for data as follows:

	```
	> db.foo.find()
	```

	The output is similar to the following example:

	```
	{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 } 
	```

	Exit the `mongo` console as follows:

	```
	exit
	```

13. Now that MongoDB is installed and running, open a port in Windows Firewall so you can remotely connect to MongoDB. Open an administrative PowerShell prompt and enter following example to create a new inbound rule to allow TCP port 27017, the default port that MongoDB listens on:

```powerShell
New-NetFirewallRule -DisplayName "Allow MongoDB" -Direction Inbound `
	-Protocol TCP -LocalPort 27017 -Action Allow
```
You can also create the rule using **Windows Firewall with Advanced Security** graphical management tool. Create a new inbound rule to allow TCP port 27017.

14. Create a Network Security Group rule to allow you to access MongoDB from outside of the existing Azure virtual network subnet. You can create the Network Security Group rules using the [Azure portal](virtual-machines-windows-nsg-quickstart-portal.md) or [Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md). As with the Windows Firewall rules, you need to allow TCP port 27017 to the virtual network interface of your MongoDB VM.

> [AZURE.NOTE] The port 27017 is the default port used by MongoDB. You can change this port by using the _--port_ parameter when starting the mongod.exe server. Make sure to then update the Windows Firewall rule and Network Security Group rules in the preceding steps.


## Next steps
In this tutorial, you learned how to install and configure MongoDB on your Windows VM. You can now access MongoDB on your Windows VM, by following the advanced topics in the [MongoDB documentation](https://docs.mongodb.com/manual/).
