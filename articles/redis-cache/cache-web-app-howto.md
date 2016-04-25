<properties 
	pageTitle="How to create a Web App with Redis Cache | Microsoft Azure" 
	description="Learn how to create a Web App with Redis Cache" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2016" 
	ms.author="sdanie"/>

# How to create a Web App with Redis Cache

Procedural steps only for tech review - TODO write an introduction

## Prerequisites

[AZURE.INCLUDE [prereqs](../../includes/app-service-web-dotnet-get-started-prereqs.md)]

## Create the Visual Studio project



1. Open Visual Studio and click **File**, **New**, **Project**.

2. Expand the **Visual C#** node in the **Templates** list, select **Cloud**, and click **ASP.NET Web Application**. Type **ContosoTeamStats** into the **Name** textbox and click **OK**.
 
    ![Create project][cache-create-project]

3. Select **MVC** as the project type. Clear the **Host in the cloud** checkbox. We will provision the Azure resources and publish the application to Azure later in the tutorial. 

    ![Select project template][cache-select-template]

4. Click **OK** to create the project.

## Add the model

1. Right-click **Models** in **Solution Explorer**, and choose **Add**, **Class**. 

    ![Add model][cache-model-add-class]

2. Enter `Team` for the class name and click **Add**.

    ![Add model class][cache-model-add-class-dialog]

3. Replace the `using` statements at the top of the `Team.cs` file with the following using statements.


		using System;
		using System.Collections.Generic;
		using System.Data.Entity;
		using System.Data.Entity.SqlServer;


4. Replace the definition of the `Team` class with the following code snippet that contains an updated `Team` class definition as well as some other Entity Framework helper classes. For more information on the code first approach to Entity Framework that's used in this tutorial, see [Code first to a new database](https://msdn.microsoft.com/data/jj193542).


		public class Team
		{
		    public int ID { get; set; }
		    public string Name { get; set; }
		    public int Wins { get; set; }
		    public int Losses { get; set; }
		    public int Ties { get; set; }
		
		    static public void PlayGames(IEnumerable<Team> teams)
		    {
		        // Simple random generation of statistics.
		        Random r = new Random();
		
		        foreach (var t in teams)
		        {
		            t.Wins = r.Next(33);
		            t.Losses = r.Next(33);
		            t.Ties = r.Next(0, 5);
		        }
		    }
		}
		
		public class TeamContext : DbContext
		{
		    public TeamContext()
		        : base("TeamContext")
		    {
		    }
		
		    public DbSet<Team> Teams { get; set; }
		}
		
		public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
		{
		    protected override void Seed(TeamContext context)
		    {
		        var teams = new List<Team>
		        {
		            new Team{Name="Adventure Works Cycles"},
		            new Team{Name="Alpine Ski House"},
		            new Team{Name="Blue Yonder Airlines"},
		            new Team{Name="Coho Vineyard"},
		            new Team{Name="Contoso, Ltd."},
		            new Team{Name="Fabrikam, Inc."},
		            new Team{Name="Lucerne Publishing"},
		            new Team{Name="Northwind Traders"},
		            new Team{Name="Consolidated Messenger"},
		            new Team{Name="Fourth Coffee"},
		            new Team{Name="Graphic Design Institute"},
		            new Team{Name="Nod Publishers"}
		        };
		
		        Team.PlayGames(teams);
		
		        teams.ForEach(t => context.Teams.Add(t));
		        context.SaveChanges();
		    }
		}
		
		public class TeamConfiguration : DbConfiguration
		{
		    public TeamConfiguration()
		    {
		        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
		    }
		}


2. In **Solution Explorer** double-click **web.config** to open it.

    ![Web.config][cache-web-config]

3.  Add the following connection string to the `connectionStrings` section. The name of the connection string must match the name of the Entity Framework database context class which is `TeamContext`.

		<add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />


    After adding this, the `connectionStrings` section should look like the following example.


		<connectionStrings>
			<add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
				providerName="System.Data.SqlClient" />
			<add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" 	providerName="System.Data.SqlClient" />
		</connectionStrings>

## Add the controller

1. Press **F6** to build the project. 
2. In **Solution Explorer**, right-click the **Controllers** folder and choose **Add**, **Controller**.

    ![Add controller][cache-add-controller]

3. Choose **MVC 5 Controller with views, using Entity Framework**, and click **Add**. If you get an error after clicking **Add**, ensure that you have built the project first.

    ![Add controller class][cache-add-controller-class]

5. Select **Team (ContosoTeamStats.Models)** from the **Model class** drop-down list. Select **TeamContext (ContosoTeamStats.Models)** from the **Data context class** drop-down list. Type `TeamsController` in the **Controller** name textbox (if it is not automatically populated). Click **Add** to create the controller class and add the default views.

    ![Configure controller][cache-configure-controller]

4. In **Solution Explorer**, expand **Global.asax** and double-click **Global.asax.cs** to open it.

    ![Global.asax.cs][cache-global-asax]

5. Add the following two using statements at the top of the file under the other using statements.


        using System.Data.Entity;
		using ContosoTeamStats.Models;


6. Add the following line of code at the end of the `Application_Start` method.


	    Database.SetInitializer<TeamContext>(new TeamInitializer());


7. In **Solution Explorer**, expand `App_Start` and double-click `RouteConfig.cs`.

    ![RouteConfig.cs][cache-RouteConfig-cs]

8. Replace `controller = "Home"` in the following code in the `RegisterRoutes` method with `controller = "Teams"` as shown in the following example.


	    routes.MapRoute(
	        name: "Default",
	        url: "{controller}/{action}/{id}",
	        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
	    );


## Configure the views

1. In **Solution Explorer**, expand the **Views** folder and then the **Shared** folder, and double-click **_Layout.cshtml**. 

    ![_Layout.cshtml][cache-layout-cshtml]

2. Change the contents of the `title` element and replace `My ASP.NET Application` with `Contoso Team Stats` as shown in the following example.


	    <title>@ViewBag.Title - Contoso Team Stats</title>


3. In the `body` section, update the first `Html.ActionLink` statement and replace `Application name` with `Contoso Team Stats` and replace `Home` with `Teams`.
	-	Before: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
	-	After: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

    ![Code changes][cache-layout-cshtml-code]

4. Press **Ctrl+F5** to build and run the application. This version of the application reads the results directly from the database. In the next section we'll add Redis Cache to the application.

![Starter application][cache-starter-application]

## Configure the application to use Redis Cache

In this step of the tutorial, we'll configure the sample application to store and retrieve Contoso team statistics from a Redis Cache using the [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) cache client.

1. To configure a client application in Visual Studio using the StackExchange.Redis NuGet package, right-click the project in **Solution Explorer** and choose **Manage NuGet Packages**. 

    ![Manage NuGet packages][redis-cache-manage-nuget-menu]

2. Type **StackExchange.Redis** into the search text box, select the desired version from the results, and click **Install**.

    ![StackExchange.Redis NuGet package][redis-cache-stack-exchange-nuget]

    The NuGet package downloads and adds the required assembly references for your client application to access Azure Redis Cache with the StackExchange.Redis cache client. If you prefer to use a strong-named version of the **StackExchange.Redis** client library, choose **StackExchange.Redis.StrongName**; otherwise choose **StackExchange.Redis**.

3. In **Solution Explorer**, expand the **Controllers** folder and double-click **TeamsController.cs** to open it.

    ![Teams controller][cache-teamscontroller]

4. Add the following two using statements to **TeamsController.cs**.

	    using System.Configuration;
        using StackExchange.Redis;

5. Add the following two properties to the `TeamsController` class.

	    // Redis Connection string info
	    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	    {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
	    });
	
	    public static ConnectionMultiplexer Connection
	    {
	        get
	        {
	            return lazyConnection.Value;
	        }
	    }
  
1. Create a file on your computer named `WebAppPlusCacheAppSecrets.config` and place it in a location that won't be checked in with the source code of your sample application, should you decide to check it in somewhere. In this example the `AppSettingsSecrets.config` file is located at `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.

2. Edit the `WebAppPlusCacheAppSecrets.config` file and add the following contents. Later in the tutorial we'll provision an Azure Redis Cache instance and update the cache name and password.


		<appSettings>
		  <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
		</appSettings>


2. In **Solution Explorer** double-click **web.config** to open it.

    ![Web.config][cache-web-config]

3. Add the following `file` attribute to the `appSettings` element. If you used a different file name or location, substitute those values for the ones shown in the example.
	-	Before: `<appSettings>`
	-	After: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    The ASP.NET runtime merges the contents of the external file with the markup in the `<appSettings>` element. The runtime ignores the file attribute if the specified file cannot be found. Your secrets (the connection string to your cache) are not included as part of the source code for the application. When you deploy your web app to Azure, the `WebAppPlusCacheAppSecrests.config` file won't be deployed (that's what you want). There are several ways to specify these secrets in Azure, and in this tutorial they are configured automatically for you when you use the **Deploy to Azure** button in a subsequent tutorial step. For more information about working with secrets in Azure, see [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).


### Update the TeamsController class to return results from the cache or the database

In this sample, team statistics can be retrieved from the database or from the cache. Team statistics are stored in the cache as a serialized `List<Team>`, and also as a sorted set using Redis data types. When retrieving items from a sorted set, you can retrieve some, all, or query for certain items. In this sample we'll query the sorted set for the top 5 teams ranked by number of wins.

1. Add the following using statements to the `TeamsController.cs` file at the top with the other using statements.

		using System.Diagnostics;
		using Newtonsoft.Json;

2. Replace the current `public ActionResult Index()` method with the following implementation.


		// GET: Teams
		public ActionResult Index(string actionType, string resultType)
		{
		    List<Team> teams = null;
		
		    switch(actionType)
		    {
		        case "playGames": // Play a new season of games.
		            PlayGames();
		            break;
		
		        case "clearCache": // Clear the results from the cache.
		            ClearCachedTeams();
		            break;
		
		        case "rebuildDB": // Rebuild the database with sample data.
		            RebuildDB();
		            break;
		    }
		
		    // Measure the time it takes to retrieve the results.
		    Stopwatch sw = Stopwatch.StartNew();
		
		    switch(resultType)
		    {
		        case "teamsSortedSet": // Retrieve teams from sorted set.
		            teams = GetFromSortedSet();
		            break;
		
		        case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
		            teams = GetFromSortedSetTop5();
		            break;
		
		        case "teamsList": // Retrieve teams froms erialized List<Team>.
		            teams = GetFromList();
		            break;
		
		        case "fromDB": // Retrieve results from the database.
		        default:
		            teams = GetFromDB();
		            break;
		    }
		
		    sw.Stop();
		    double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));
		    ViewBag.msg += " MS: " + ms.ToString();
		
		    return View(teams);
		}


3. Add the following three methods to the `TeamsController` class to implement the `playGames`, `clearCache`, and `rebuildDB` action types from the switch statement added in the previous code snippet.

    The `PlayGames` method updates the team statistics by simulating a season of games.


	    void PlayGames()
	    {
	        ViewBag.msg += "Updating team statistics. ";
	        // Play a "season" of games.
	        var teams = from t in db.Teams
	                    select t;
	
	        Team.PlayGames(teams);
	
	        db.SaveChanges();
	
	        // Clear any cached results
	        ClearCachedTeams();
	    }


    The `RebuildDB` method reinitializes the database with the default set of teams and generates statistics for them.
	
        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);
        }


    The `ClearCachedTeams` method removes any cached team statistics from the cache.

	
	    void ClearCachedTeams()
	    {
	        IDatabase cache = Connection.GetDatabase();
	        cache.KeyDelete("teamsList");
	        cache.KeyDelete("teamsSortedSet");
	        ViewBag.msg += "Team data removed from cache. ";
	    } 


4. Add the following four methods to the `TeamsController` class to implement the various ways of retrieving the team statistics from the cache and the database.

    The `GetFromDB` method reads the team statistics from the database.

	    List<Team> GetFromDB()
	    {
	        ViewBag.msg += "Results read from DB. ";
	        var results = from t in db.Teams
	            orderby t.Wins descending
	            select t; 
	
	        return results.ToList<Team>();
	    }


    The `GetFromList` method reads the team statistics from cache as a serialized `List<Team>`. If there is a cache miss, the team statistics are read from the database and then stored in the cache for next time. In this sample we're using JSON.NET serialization.

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    The `GetFromSortedSet` method reads the team statistics from a cached sorted set. If there is a cache miss, the team statistics are read from the database and stored in the cache as a sorted set.


	    List<Team> GetFromSortedSet()
	    {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
	        else
	        {
	            ViewBag.msg += "Teams sorted set cache miss. ";
	
	            // Read from DB
	            teams = GetFromDB();
	
	            ViewBag.msg += "Storing results to cache. ";
	            foreach (var t in teams)
	            {
	                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
	                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
	            }
	        }
	        return teams;
	    }


    The `GetFromSortedSetTop5` method reads the top 5 teams from the cached sorted set. It starts by checking the cache for the existence of the `teamsSortedSet` key. If this key is not present, the `GetFromSortedSet` method is called to read the team statistics and store them in the cache. Next the cached sorted set is queried for the top 5 teams which are returned.


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### Update the Create, Edit, and Delete methods to work with the cache

The scaffolding code that was generated as part of this sample includes methods to add, edit, and delete teams. Anytime a team is added, edited, or removed, the data in the cache becomes outdated. In this section we'll modify these three methods to clear the cached teams so that the cache won't be out of sync with the database.

1. Browse to the `Create(Team team)` method in the `TeamsController` class. Add a call to the `ClearCachedTeams` method, as shown in the following example.


	    // POST: Teams/Create
	    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
	    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
	    [HttpPost]
	    [ValidateAntiForgeryToken]
	    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
	    {
	        if (ModelState.IsValid)
	        {
	            db.Teams.Add(team);
	            db.SaveChanges();
	            // When a team is added, the cache is out of date.
	            // Clear the cached teams.
	            ClearCachedTeams();
	            return RedirectToAction("Index");
	        }
	
	        return View(team);
	    }


2. Browse to the `Edit(Team team)` method in the `TeamsController` class. Add a call to the `ClearCachedTeams` method, as shown in the following example.


	    // POST: Teams/Edit/5
	    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
	    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
	    [HttpPost]
	    [ValidateAntiForgeryToken]
	    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
	    {
	        if (ModelState.IsValid)
	        {
	            db.Entry(team).State = EntityState.Modified;
	            db.SaveChanges();
	            // When a team is edited, the cache is out of date.
	            // Clear the cached teams.
	            ClearCachedTeams();
	            return RedirectToAction("Index");
	        }
	        return View(team);
		}


3. Browse to the `DeleteConfirmed(int id)` method in the `TeamsController` class. Add a call to the `ClearCachedTeams` method, as shown in the following example.


	    // POST: Teams/Delete/5
	    [HttpPost, ActionName("Delete")]
	    [ValidateAntiForgeryToken]
	    public ActionResult DeleteConfirmed(int id)
	    {
	        Team team = db.Teams.Find(id);
	        db.Teams.Remove(team);
	        db.SaveChanges();
	        // When a team is deleted, the cache is out of date.
	        // Clear the cached teams.
	        ClearCachedTeams();
	        return RedirectToAction("Index");
	    }


## Update the Teams Index view

1. In **Solution Explorer**, expand the **Views** folder, then the **Teams** folder, and double-click **Index.cshtml**.

    ![Index.cshtml][cache-views-teams-index-cshtml]

2. Near the top of the file, look for the following paragraph element.

    ![Action table][cache-teams-index-table]

    This is the link to create a new team. Replace the paragraph element with the following table. This table has action links for creating a new team, playing a new season of games, clearing the cache, retrieving the teams from the cache in several formats, retrieving the teams from the database, and reloading the database with fresh sample data.


		<table class="table">
		    <tr>
		        <td>
		            @Html.ActionLink("Create New", "Create")
		        </td>
		        <td>
		            @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
		        </td>
		        <td>
		            @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
		        </td>
		        <td>
		            @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
		        </td>
		        <td>
		            @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
		        </td>
		        <td>
		            @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
		        </td>
		        <td>
		            @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
		        </td>
		        <td>
		            @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
		        </td>
		    </tr>    
		</table>


3. Scroll to the bottom of the **Index.cshtml** file and add the following `tr` element so that it is the last row in the last table in the file.

        <tr><td colspan="5">@ViewBag.Msg</td></tr>

    This row displays the value of `ViewBag.Msg` which contains a status report about the current operation which is set when you click one of the action links from the previous step.   

    ![Status message][cache-status-message]

4. Press **F6** to build the project.

## Provision the Azure resources

To host your application in Azure, you must first provision the Azure services that your application requires. The sample application in this tutorial uses the following Azure services.

-	Azure Redis Cache
-	App Service Web App
-	SQL Database

To deploy these services to a new or existing resource group of your choice, click the following **Deploy to Azure** button.

[![Deploy to Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

The **Deploy to Azure** button uses the [Create a Web App plus Redis Cache plus SQL Database using a template](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) Azure Quickstart template to provision these services and set the connection string for the SQL Database and the application setting for the Azure Redis Cache connection string.

>[AZURE.NOTE] If you don't have an Azure account, you can create a free account in just a couple of minutes. For details, see [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero).

Clicking the **Deploy to Azure** button takes you to the Azure portal and initiates the process of creating the resources described by the template.

![Deploy to Azure][cache-deploy-to-azure-step-1]

1. On the **Custom deployment** blade, select an existing resource group or create a new one and specify its location.
2. On the **Parameters** blade, specify an administrator account name (**ADMINISTRATORLOGIN** - don't use **admin**), administrator login password (**ADMINISTRATORLOGINPASSWORD**), and database name (**DATABASENAME**). The other parameters are configured for a free App Service hosting plan, and lower cost options for the SQL Database and Azure Redis Cache, which don't come with a free tier.
3. Change any of the other settings if desired, or keep the defaults, and click **OK**.
4. Once the settings are configured, click **Review legal terms**.

![Deploy to Azure][cache-deploy-to-azure-step-2]

1. Read the terms on the **Create** blade and click **Create**.
2. To begin provisioning the resources, click **Create** on the **Custom deployment** blade.

To view the progress of your deployment, click the notification icon and click **Deployment started**.

![Deployment started][cache-deployment-started]

You can view the status of your deployment on the **Microsoft.Template** blade.

![Deploy to Azure][cache-deploy-to-azure-step-3]

When provisioning is complete, you can publish your application to Azure from Visual Studio.

>[AZURE.NOTE] Any errors that may occur during the provisioning process are displayed on the **Microsoft.Template** blade. Common errors are too many SQL Servers or too many Free App Service hosting plans per subscription. Resolve any errors and restart the process by clicking the **Deploy to Azure** button.

## Publish the application to Azure

1. Right-click the **ContosoTeamStats** project in Visual Studio and choose **Publish**.

    ![Publish][cache-publish-app]

2. Click **Microsoft Azure App Service**.

    ![Publish][cache-publish-to-app-service]

3. Select the subscription used when creating the Azure resources, expand the resource group containing the resources, select the desired Web App, and click **OK**. If you used the **Deploy to Azure** button your Web App name starts with **website** followed by some additional characters.

    ![Select Web App][cache-select-web-app]

4. Click **Validate Connection** to verify your settings, and then click **Publish**.

    ![Publish][cache-publish]

    After a few moments the publshing process will complete and a browser will be launched with the running sample application.

    ![Cache added][cache-added-to-application]

The following table describes each action link from the sample application.

| Action                  | Description                                                                                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Create New              | Create a new Team.                                                                                                                                               |
| Play Season             | Play a season of games, update the team stats, and clear any outdated team data from the cache.                                                                          |
| Clear Cache             | Clear the team stats from the cache.                                                                                                                             |
| List from Cache         | Retrieve the team stats from the cache. If there is a cache miss, load the stats from the database and save to the cache.                                        |
| Sorted Set from Cache   | Retrieve the team stats from the cache using a sorted set. If there is a cache miss, load the stats from the database and save to the cache using a sorted set.  |
| Top 5 Teams from Cache  | Retrieve the top 5 teams from the cache using a sorted set. If there is a cache miss, load the stats from the database and save to the cache using a sorted set. |
| Load from DB            | Retrieve the team stats from the database.                                                                                                                       |
| Rebuild DB              | Rebuild the database and reload it with sample team data.                                                                                                        |
| Edit / Details / Delete | Edit a team, view details for a team, delete a team.                                                                                                             |


Click some of the actions and experiment with retrieving the data from the different sources.

## Delete the resources when you are finished with the application

When you are finished with the sample application, you can delete the resources used in order to conserve cost and resources. 

1. Sign in to the [Azure portal](https://portal.azure.com) and click **Resource groups**.
2. Type the name of your resource group into the **Filter items...** textbox.
3. Click **...** to the right of your resource group.
4. Click **Delete**.

    ![Delete][cache-delete-resource-group]

5. Type the name of your resource group and click **Delete**.

    ![Confirm delete][cache-delete-confirm]

After a few moments the resource group and all of its contained resources are deleted.

>[AZURE.IMPORTANT] Note that deleting a resource group is irreversible and that the resource group and all the resources in it are permanently deleted. Make sure that you do not accidentally delete the wrong resource group or resources. If you created the resources for hosting this sample inside an existing resource group, you can delete each resource individually from their respective blades.

## Run the sample application on your local machine

To run the application locally on your machine, you need an Azure Redis Cache instance in which to cache your data. 

-	If you have published your application to Azure, you can use the Azure Redis Cache instance that was provisioned during that step.
-	If you have another existing Azure Redis Cache instance, you can use that to run this sample locally.
-	If you need to create an Azure Redis Cache instance, you can follow the steps in [Create a cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Once you have selected the cache to use, browse to the cache in the Azure portal to your cache and retrieve the [host name](cache-configure.md#properties) and [access keys](cache-configure.md#access-keys) for your cache. For instructions, see [Configure Redis cache settings](cache-configure.md#configure-redis-cache-settings).

1. Open the `WebAppPlusCacheAppSecrets.config` file that you created during the [Configure the application to use Redis Cache](#configure-the-application-to-use-redis-cache) step of this tutorial using the editor of your choice.

2. Edit the `value` attribute and replace `MyCache.redis.cache.windows.net` with the [host name](cache-configure.md#properties) of your cache, and specify either the [primary or secondary key](cache-configure.md#access-keys) of your cache as the password.


		<appSettings>
		  <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
		</appSettings>


3. Press **Ctrl+F5** to run the application.

>[AZURE.NOTE] Note that because the application, including the database, is running locally and the Redis Cache is hosted in Azure, the cache may appear to under perform the database. For best performance, the client application and Azure Redis Cache instance should be in the same location.

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png
[]: ./media/cache-web-app-howto/.png
[]: ./media/cache-web-app-howto/.png
[]: ./media/cache-web-app-howto/.png
[]: ./media/cache-web-app-howto/.png
[]: ./media/cache-web-app-howto/.png
