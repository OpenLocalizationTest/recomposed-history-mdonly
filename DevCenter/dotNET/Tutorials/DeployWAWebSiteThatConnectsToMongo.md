#Create a Windows Azure Web Site that Connects to MongoDB Running in a Virtual Machine on Windows Azure

Using Git, you can easily deploy an ASP.NET application to a Windows Azure website. In this tutorial, you will build a simple front-end ASP.NET MVC task list application that connects to a MongoDB database running in a virtual machine in Windows Azure.  [MongoDB][MongoDB] is a popular open source, high performance NoSQL database. After running and testing the ASP.NET application on your development computer, you will upload the application to a Windows Azure website using Git.

The ASP.NET application you'll build will look like this:
![My Task List Application][Image0]

## Overview

In this tutorial you will:

*  Set up a virtual machine running Linux or Windows and install MongoDB
*  Create and run the Task List ASP.NET application on your development computer
*  Create a Windows Azure website
*  Deploy the Task List ASP.NET application to the Windows Azure website using Git

## Background Knowledge

Knowledge of the following is useful for this tutorial, though not required:

* The C# driver for MongoDB. For more information on developing C# applications against MongoDB, see [CSharp Language Center][MongoC#LangCenter]. 
* The ASP .NET web application framework. You can learn all about it at the [ASP.net website][ASP.NET].
* The ASP .NET MVC 3.0 web application framework. You can learn all about it at the [ASP.NET MVC 3 website][MVC3].
* The Windows Azure Platform. You can get started reading at [Windows Azure][WindowsAzure].

## Preparation

In this section you will learn how to create a virtual machine in Windows Azure and install MongoDB, set up your development environment, and install the MongoDB C# driver.

###Create a Virtual Machine and Install MongoDB

This tutorial assumes you have created a virtual machine in Windows Azure running Windows Server 2008 R2 or Linux. After createing the virtual machine you need to install MongoDB on the virtual machine.

To create a Windows Server 2008 R2 SP virtual machine and install MongoDB, see [Create a Virtual Machine Running Windows Server 2008 R2 and Install MongoDB][InstallMongoWinVM].

To create a Linux virtual machine and install MongoDB, see [Create a Virtual Machine Running Linux and Install MongoDB][InstallMongoOnCentOSLinuxVM].

After you have created the virtual machine in Windows Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.

### Set Up the Development Environment

This tutorial uses Microsoft Visual Studio 2010.  You can also use Microsoft Visual Web Developer 2010 Express Service Pack 1, which is a free version of Microsoft Visual Studio. Before you start, make sure you've installed the prerequisites listed below on your local development computer. You can install all of them by clicking the following link: [Web Platform Installer] [WebPlatformInstaller]. Alternatively, you can individually install the prerequisites using the following links:

* [Visual Studio 2010 prerequisites] [VsPreReqs]
* [ASP.NET MVC 3 Tools Update] [MVCPrereqs]

If you're using Visual Web Developer 2010 instead of Visual Studio 2010, install the prerequisites by clicking the following link: [Visual Studio Web Developer Express SP1 prerequisites] [VsWebExpressPreReqs] 

###Install the MongoDB C# driver

MongoDB offers client-side support for C# applications through a driver, which you need to install on your local development computer. The C# driver is hosted at github.com. You can download the most recent binary builds in .msi format from: [Downloads][MongoCSharpDriverDownload].

Download the .msi file and double-click it to run the setup program, which will install the C# driver DLLs to the `C:\Program Files (x86)\MongoDB\CSharp Driver 1.x` directory (the exact path may vary on your system).

## Creating the Task List ASP.NET application and running it  

In this section you will create the Task List ASP.NET application using Visual Studio.  You will also run the application locally against the MongoDB instance you created in the virtual machine hosted on Windows Azure.

###Creating the Application
Start by running Visual Studio and select **New Project** from the **Start** page.

Select **Visual C#** and then **Web** on the left and then select **ASP.NET MVC 3 Web Application** from the list of templates. Name your project "MyTaskListApp" and then click **OK**.

![New Project Screen][Image00]

In the **New ASP.NET MVC 3 Project** dialog box, select **Internet Application**. Check **Use HTML5 markup** and leave **Razor** as the default view engine.

Click **OK**.

![New Internet Application][Image1]

###Add References to the C# MongoDB drivers
In **Solution Explorer**, right-click the **References** folder and select **Add Reference...**. Click **Browse** and then navigate to `C:\Program Files (x86)\MongoDB\CSharp Driver 1.x` (or the path that the drivers were installed to). Select `MongoDB.Driver.dll` and `MongoDB.Bson.dll` and click **OK**.

![Add a reference to the C# MongoDB drivers][Image2]

In **Solution Explorer**, right-click the *References/MongoDB.Bson* file and select **Properties**.  In the **Properties** frame, set **Copy Local** to **True**.

![Set MongoDB.Bson Copy Local to True][Image2.1]

In **Solution Explorer**, right-click the *References/MongoDB.Driver* file and select **Properties**.  In the **Properties** frame, set **Copy Local** to **True**.

![Set MongoDB.Driver Copy Local to True][Image2.2]

###Adding a Model
In **Solution Explorer**, right-click the *Models* folder and **Add** a new **Class** *TaskModel.cs*.  In *TaskModel.cs*, replace the existing code with the following code:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MongoDB.Bson.Serialization.Attributes;
	using MongoDB.Bson.Serialization.IdGenerators;
	using MongoDB.Bson;
	
	namespace MyTaskListApp.Models
	{
	    public class Task
	    {
	        [BsonId(IdGenerator = typeof(CombGuidGenerator))]
	        public Guid Id { get; set; }
	
	        [BsonElement("Name")]
	        public string Name { get; set; }
	
	        [BsonElement("Category")]
	        public string Category { get; set; }
	
	        [BsonElement("Date")]
	        public DateTime Date { get; set; }
	
	        [BsonElement("CreatedDate")]
	        public DateTime CreatedDate { get; set; }
	
	    }
	}

###Adding the Data Access Layer
In **Solution Explorer**, right-click the *MyTaskListApp* project and **Add** a **New Folder** named *DAL*.  Right-click the *DAL* folder and **Add** a new class file named *Dal.cs*.  In *Dal.cs*, replace the existing code with the following code:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
	using System.Configuration;
	
	namespace MyTaskListApp
	{
	    public class Dal : IDisposable
	    {
	        private MongoServer mongoServer = null;
	        private MongoDatabase database = null;
	        private bool disposed = false;
	
	        // To do: update the connection string with the DNS name
			// or IP address of your server. 
			//For example, "mongodb://testlinux.cloudapp.net"
	        private string connectionString = "mongodb://<vm-dns-name>";
	
	        // This sample uses a database named "Tasks" and a 
			//collection named "TasksList".  The database and collection 
			//will be automatically created if they don't already exist.
	        private string dbName = "Tasks";
	        private string collectionName = "TasksList";
	
	        // Default constructor.        
	        public Dal()
	        {
	        }        
	
	        // Gets all Task items from the MongoDB server.        
	        public List<Task> GetAllTasks()
	        {
	            try
	            {
	                MongoCollection<Task> collection = GetTasksCollection();
	                return collection.FindAll().ToList<Task>();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<Task>();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(Task task)
	        {
	            MongoCollection<Task> collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.Insert(task, SafeMode.True);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private MongoCollection<Task> GetTasksCollection()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<Task> todoTaskCollection = database.GetCollection<Task>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private MongoCollection<Task> GetTasksCollectionForEdit()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<Task> todoTaskCollection = database.GetCollection<Task>(collectionName);
	            return todoTaskCollection;
	        }
	
	        # region IDisposable
	
	        public void Dispose()
	        {
	            this.Dispose(true);
	            GC.SuppressFinalize(this);
	        }
	
	        protected virtual void Dispose(bool disposing)
	        {
	            if (!this.disposed)
	            {
	                if (disposing)
	                {
	                    if (mongoServer != null)
	                    {
	                        this.mongoServer.Disconnect();
	                    }
	                }
	            }
	
	            this.disposed = true;
	        }
	
	        # endregion
	    }
	}

###Adding a Controller
Open the *Controllers\HomeController.cs* file in **Solution Explorer** and replace the existing code with the following:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;
	using MyTaskListApp.Models;
	using System.Configuration;
	
	namespace MyTaskListApp.Controllers
	{
	    public class HomeController : Controller, IDisposable
	    {
	        private Dal dal = new Dal();
	        private bool disposed = false;
	        //
	        // GET: /Task/
	
	        public ActionResult Index()
	        {
	            return View(dal.GetAllTasks());
	        }
	
	        //
	        // GET: /Task/Create
	
	        public ActionResult Create()
	        {
	            return View();
	        }
	
	        //
	        // POST: /Task/Create
	
	        [HttpPost]
	        public ActionResult Create(Task task)
	        {
	            try
	            {
	                dal.CreateTask(task);
	                return RedirectToAction("Index");
	            }
	            catch
	            {
	                return View();
	            }
	        }
	
	        public ActionResult About()
	        {
	            return View();
	        }
	
	        # region IDisposable
	
	        new protected void Dispose()
	        {
	            this.Dispose(true);
	            GC.SuppressFinalize(this);
	        }
	
	        new protected virtual void Dispose(bool disposing)
	        {
	            if (!this.disposed)
	            {
	                if (disposing)
	                {
	                    this.dal.Dispose();
	                }
	            }
	
	            this.disposed = true;
	        }
	
	        # endregion
	
	    }
	}

###Setting Up the Site Style
To change the title at the top of the page, open the *Views\Shared\\_Layout.cshtml* file in **Solution Explorer** and replace the existing **h1** heading text with the following:

	<h1>My Task List Application</h1>
	
In order to set up the Task List menu, open the *\Views\Home\Index.cshtml* file and replace the existing code with the following code:
	
	@model IEnumerable<MyTaskListApp.Models.Task>
	
	@{
	    ViewBag.Title = "My Task List";
	}
	
	<h2>My Task List</h2>
	
	<table border="1">
	    <tr>
	        <th>Task</th>
	        <th>Category</th>
	        <th>Date</th>
	        
	    </tr>
	
	@foreach (var item in Model) {
	    <tr>
	        <td>
	            @Html.DisplayFor(modelItem => item.Name)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Category)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Date)
	        </td>
	        
	    </tr>
	}
	
	</table>
	<div>  @Html.Partial("Create", new MyTaskListApp.Models.Task())</div>


To add the ability to create a new task, right-click the *Views\Home\\* folder and **Add** a **View**.  Name the view *Create*. Replace the code with the following:

	@model MyTaskListApp.Models.Task
	
	<script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
	
	@using (Html.BeginForm("Create", "Home")) {
	    @Html.ValidationSummary(true)
	    <fieldset>
	        <legend>New Task</legend>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Name)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Name)
	            @Html.ValidationMessageFor(model => model.Name)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Category)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Category)
	            @Html.ValidationMessageFor(model => model.Category)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Date)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Date)
	            @Html.ValidationMessageFor(model => model.Date)
	        </div>
	
	        <p>
	            <input type="submit" value="Create" />
	        </p>
	    </fieldset>
	}

**Solution Explorer** should look like this:

![Solution Explorer][Image3]

###Setting the MongoDB Connection String
In **Solution Explorer**, open the *DAL/Dal.cs* file. Find the following line of code:

	private string connectionString = "mongodb://<vm-dns-name>";

Replace `<vm-dns-name>` with the DNS name of the virtual machine running MongoDB you created in the **Create a Virtual Machine and Install MongoDB** step of this tutorial.  To find the DNS name of your virtual machine, go to the Windows Azure management portal, select **Virtual Machines**, and find **DNS Name**.

If the DNS name of the virtual machine is "testlinuxvm.cloudapp.net" and MongoDB is listening on the default port 27017, the connection string line of code will look like:

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

If the virtual machine endpoint specifies a different external port for MongoDB, you can specifiy the port in the connection string:

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

For more information on MongoDB connection strings, see [Connections][MongoConnectionStrings].

###Testing the local deployment

To run your application on your development computer, select **Start Debugging** from the **Debug** menu or hit **F5**. A development web server starts and a browser opens and launches the application's home page.  You can add a new task, which will be added to the MongoDB database running on your virtual machine in Windows Azure.

![My Task List Application][Image4]

## Deploy the ASP.NET application to a Windows Azure Website

In this section you will create a Windows Azure website and deploy your Task List ASP.NET application using Git.

### Create the Windows Azure website
In this section you will create a Windows Azure Website.

1. Open a web browser and browse to the [Windows Azure (Preview) Management Portal][AzurePreviewPortal]. Sign in with your Windows Live ID and password. If you don't have a Windows Azure account, you can get started with a free account by clicking **Free trial** in the upper right corner. 
2. At the bottom of the page, click **+New**, then **Web Site**, and finally **Quick Create**.
3. Enter a unique prefix for the application's URL.
4. Select a region.
5. Click **Create Web Site**.
![Create a new website][Image7]
6. Your web site will be created quickly.
7. Click the **Name** of your site, then **Dashboard**.
![Dashboard][Image8]

### Deploy the ASP.NET Application using Git
In this section you will deploy the Task List application using Git.

1. Click **Set up Git publishing** at the bottom of the *Dashboard* page for the *mytasklistapp* site. 
2. Enter a user name and password. Make note of the instructions on the resulting page as they will be used in the next section.
3. The Git repository should be created quickly.

	![Git Repository is Ready][Image9]
4. Select **Push my local files to Windows Azure** to display instructions on pushing your code to Windows Azure. The instructions will look similar to the following:

	![Push local files to Azure][Image10]
5. If you do not have Git installed, install it using the **Get it here** link in step 1.
6. Following these instructions in step 2, commit your local files.
7. Add the remote Azure repository and push your files to the Azure Web Site by following the instructions in step 3.
8. When the deployment has completed you will see the following confirmation:
	![Deployment Complete][Image11]
9. Your Windows Azure Web Site is now available.  Check the **Dashboard** page for your site and the **Site URL** field to find the URL for your site. Following the procedures in this tutorial, your site would be available at this URL: <a href="http://mytasklistapp.cloudapp.net">http://mytasklistapp.cloudapp.net</a>.

##Summary

You have now successfully deployed your ASP.NET application to a Windows Azure web site.  To view the site, click the link in the **Site URL** field of the **Dashboard** page. For more information on developing C# applications against MongoDB, see [CSharp Language Center][MongoC#LangCenter]. 

[AzurePreviewPortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[ASP.NET]: http://www.asp.net
[MVC3]: http://www.asp.net/mvc
[MVCPrereqs]: http://www.microsoft.com/web/gallery/install.aspx?appsxml=&appid=MVC3
[VsPreReqs]: http://www.microsoft.com/web/gallery/install.aspx?appsxml=&appid=VS2010SP1Pack
[VsWebExpressPreReqs]: http://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[WebPlatformInstaller]: http://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack
[MongoC#LangCenter]: http://www.mongodb.org/display/DOCS/CSharp+Language+Center
[MongoDB]: http://www.mongodb.org
[MongoCSharpDriverDownload]: http://github.com/mongodb/mongo-csharp-driver/downloads
[InstallMongoWinVM]: ../../../Shared/Tutorials/InstallMongoDbOnWin2k8VM
[InstallMongoOnCentOSLinuxVM]: ../../../Shared/Tutorials/InstallMongoOnCentOSLinuxVM


[Image0]: ../../../DevCenter/dotNET/Media/TaskListAppFull.png
[Image00]: ../../../DevCenter/dotNET/Media/NewProject.png
[Image1]: ../../../DevCenter/dotNET/Media/NewProject2.png
[Image2]: ../../../DevCenter/dotNET/Media/AddReference.png
[Image2.1]: ../../../DevCenter/dotNET/Media/MongoDbBsonCopyLocal.png
[Image2.2]: ../../../DevCenter/dotNET/Media/MongoDbDriverCopyLocal.png
[Image3]: ../../../DevCenter/dotNET/Media/SolnExplorer.png
[Image4]: ../../../DevCenter/dotNET/Media/TaskListAppBlank.png
[Image7]: ../../../DevCenter/dotNET/Media/NewWebSite.png
[Image8]: ../../../DevCenter/dotNET/Media/Dashboard.png
[Image9]: ../../../DevCenter/dotNET/Media/RepoReady.png
[Image10]: ../../../DevCenter/dotNET/Media/GitInstructions.png
[Image11]: ../../../DevCenter/dotNET/Media/GitDeploymentComplete.png