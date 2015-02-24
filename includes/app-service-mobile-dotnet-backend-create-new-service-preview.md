

Follow these steps to create a new mobile app.

1.	Log into the [Azure Portal]. In the bottom left of the window, click **+NEW**. Scroll until you see the **Mobile App** item.

 ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/new-mobile-app.png)

  This displays the **New Mobile App** blade.

2. Type a name for your Mobile App. It must be at least 8 characters long and lowercase a to z.  

  > [AZURE.NOTE] As part of this tutorial, you create a new SQL Database instance and server. You can reuse this new database and administer it as you would any other SQL Database instance. If you already have a database in the same region as the new mobile app backend, you can instead choose **Use existing Database** and then select that database. The use of a database in a different region is not recommended because of additional bandwidth costs and higher latencies.

>[AZURE.NOTE] It is sometimes useful to use an existing hosting plan. In this case, 1) Pick a name, 2) Pick subscription, 3) Pick existing web hosting plan (this determines the resource group and pricing tier), 4) Create new database or pick existing
s

6. Select your subscription.

5. Create a new resource group with the same name as your mobile app.

3.	In **Package Settings**, select **USERDATABASE**, you can choose an existing database or create a new one. For creating a new database, type the name of the new **database**, create a new **server**, type the name of that server, then choose a **login name**, which is the administrator login name for the new SQL Database server, type and confirm the password, and click the ok button to complete the process. If selecting an existing database, you will need to provide a **Server Administrator Password**.
	![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/dotnet-backend-create-db.png)

4. Create a new webhosting plan with the same name as your mobile app.

7. Select a region. In this tutorial, we use **East US**.

8. Select a Pricing Tier. In this tutorial, we use **Standard 1**.

 Your new mobile app settings page will now look something like this:

   ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/dotnet-backend-create.png)

9. Click the **Create** button at the bottom of the blade and you should see it starting deployment in the notifications window.





You have now created a new mobile app backend that can be used by your mobile apps.

>[AZURE.NOTE] After your mobile app is created, navigate in the portal to the sql server you just created (be sure to select the server and not the azure sql db). From there, click the settings part, expand the firewall part, and change the "Allow access to Azure services". If you don't do this, your application won't work.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
