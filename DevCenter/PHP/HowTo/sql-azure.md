#How to Access SQL Azure from PHP

This guide will show you how to perform common tasks using the SQL Azure database service. The samples are written in PHP and use the Windows Azure SDK for PHP. The scenarios covered include creating a server, creating a database, and connecting to a database. For more information, see the [Next Steps](#NextSteps) section.

##What is SQL Azure

	(TODO: Reference reusable content "chunk".)

##Table of Contents

* [Concepts](#Concepts)
* [How to Setup Your Environment](#Setup)
* [How to Create a SQL Azure Server](#CreateServer)
* [How to Create a SQL Azure Database](#CreateDatabase)
* [How to Connect to a SQL Azure Database](#Connect)
* [Next Steps](#NextSteps)

<h2 id="Concepts">Concepts</h2>
Because SQL Azure is built on SQL Server technologies, accessing SQL Azure from PHP is very similar to accessing SQL Server from PHP. You can develop an application locally (using SQL Server) and then connect to SQL Azure by changing only the connection string. However, there are some differences between SQL Azure and SQL Server that could affect your application. For more information, see [Guidelines and Limitations (SQL Azure Database)][limitations].

For accessing SQL Azure from Windows, the recommended approach is to use the [Microsoft Drivers for PHP for SQL Server][download-drivers]. (The examples in this article will use these drivers.) For accessing SQL Azure from Linux and other non-Windows platforms, using an ODBC driver for SQL Server and the [ODBC extension for PHP][odbc-php] is recommended. For more information, see the [Next Steps](#NextSteps) section.

<h2 id="Setup">How to Setup Your Environment</h2>

	(Use Web PI...content TBD.)

Alternatively, you can manually configure IIS and install PHP by following the instructions here: [http://php.net/manual/en/install.windows.iis7.php][manual-config]. After configuring IIS and installing PHP, download and install the Microsoft Drivers for PHP for SQL Server by following the directions that begin here: [http://php.net/manual/en/sqlsrv.requirements.php][install-drivers].

<h2 id="CreateServer">How to Create a SQL Azure Server</h2>

	(Content TBD)

<h2 id="CreateDatabase">How to Create a SQL Azure Database</h2>

	(Content TBD)

<h2 id="Connect">How to Connect to a SQL Azure Database</h2>

The following examples show how to use the **SQLSRV** and **PDO_SQLSRV** extensions to connect to an existing SQL Azure database called `TestDB`. Replace `SERVER_ID` with your 10-digit server ID, and assign the correct values (your user name and password) to the `$user` and `$pwd` variables.

#####SQLSRV

	$server = "tcp:SERVER_ID.database.windows.net, 1433";
	$user = "user";
	$pwd = "password";
	$db = "TestDB";

	$conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

	if($conn === false){
		die(print_r(sqlsrv_errors()));
	}

#####PDO_SQLSRV

	$server = "tcp:SERVER_ID.database.windows.net, 1433";
	$user = "user";
	$pwd = "password";
	$db = "TestDB";

	try{
		$conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
		$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
	}
	catch(Exception $e){
		die(print_r($e));
	}



<h2 id="NextSteps">Next Steps</h2>
As mentioned earlier, using SQL Azure is very similar to using SQL Server. Once you have established a connection to a SQL Azure database (as shown above), you can then use the **SQLSRV** or **PDO_SQLSRV** APIs for inserting, retrieving, updating, and deleting data. For information about the **SQLSRV** and **PDO_SQLSRV** APIs, see the [Microsoft Drivers for PHP for SQL Server documentation][driver-docs]. There are, however, some differences between SQL Azure and SQL Server that could affect your application. For more information, see [Guidelines and Limitations (SQL Azure Database)][limitations]. 

For accessing SQL Azure from Linux and other non-Windows platforms, using an ODBC driver for SQL Server and the [ODBC extension for PHP][odbc-php] is recommended. For more information, see [Installing and Using the Microsoft SQL Server ODBC Driver for Linux][install-linux-driver], [Download the Microsoft SQL Server ODBC Driver for Linux][download-linux-driver], and [Accessing SQL Azure from PHP][access-php-odbc].


[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/en-us/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/en-us/library/dd638075(SQL.10).aspx
[install-linux-driver]: http://strangenut.com/blogs/dacrowlah/archive/2012/04/13/installing-and-using-the-microsoft-sql-server-odbc-driver-for-linux.aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[download-linux-driver]: http://www.microsoft.com/download/en/details.aspx?id=28160