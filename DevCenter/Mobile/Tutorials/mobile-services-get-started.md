<properties linkid="mobile-get-started" urldisplayname="Mobile Services" headerexpose="" pagetitle="Get started with Mobile Services in Windows Azure" metakeywords="Get started Windows Azure Mobile Services, mobile devices, Windows Azure, mobile, Windows 8, WinRT app" footerexpose="" metadescription="Get started using Windows Azure Mobile Services in your Windows Store apps." umbraconavihide="0" disquscomments="1"></properties>

# <a name="getting-started"> </a>Get started with Mobile Services
This tutorial shows you how to add a cloud-based backend service to a Windows 8 app using Windows Azure Mobile Services. In this tutorial, you will create both a new mobile service and a simple Windows 8 _To do list_ app that stores app data in the new mobile service. 

A screenshot from the completed app is below:

![][0]

Completing this guide is a prerequisite for all other Mobile Services tutorials. 

<div chunk="../../Shared/Chunks/create-account-and-mobile-note.md" />
<div class="dev-callout"><strong>Note</strong>
<p>To complete this tutorial, you need a Windows Azure account that has the Windows Azure Mobile Services feature enabled. You can create a free trial account and enable preview features in just a couple of minutes. For details, see <a href="../create-a-windows-azure-account/" target="_blank">Create a Windows Azure account and enable preview features</a>. </p>
</div>
<br />

### Create a new mobile service
Follow these steps to create a new mobile service.

1.	Log into the [Preview Management Portal][Management Portal preview]. 
2.	At the bottom of the navigation pane, click **+NEW**.

	![][1]

3.	Expand **Compute** and **Mobile Service**, then click **Create**.

	![][2]

    This displays the **New Mobile Service** dialog.

4.	In the **Create a mobile service** page, type a subdomain name for the new mobile service in the **URL** textbox and wait for name verification. 

    <div class="dev-callout"> 
	<b>Note</b> 
	<p>URL values in Mobile Services must be globally unique. When the name that you specify is available, a green check icon is displayed; otherwise, a warning is displayed and you are asked to choose a different name.</p> 
	</div>

    Once name verification completes, click the right arrow button to go to the next page.	

	![][3]

    This displays the **Specify database settings** page.

    <div class="dev-callout"> 
	<b>Note</b> 
	<p>This tutorial assumes that you do not already have a SQL Database instance. As part of this tutorial, you create a new SQL Database instance and server. Make sure that you provide a meaningful name for the new database. You can reuse this new database and administer it as you would any other SQL Database instance. If you already have a database in the same region as the the new mobile service, you can instead choose <b>Use existing Database</b> and then select that database.</p></div>	

6.	In **Name**, type the name of the new database, then type **Login name**, which is the administrator login name for the new SQL Database server, type and confirm the password, and click the check button to complete the process.

	![][4]

    <div class="dev-callout"> 
	<b>Note</b> 
	<p>When the password that you supply does not meet the minimum requirements or when there is a mismatch, a warning is displayed. <br/>We recommend that you make a note of the administrator login name and password that you specify; you will need this information to reuse the SQL Database instance or the server in the future.</p> 
	</div>

You have now created a new mobile service that can be used by your mobile apps.

### Create new Windows 8 app

Once you have created your mobile service, you can follow an easy quickstart in the Preview Management Portal to either create a new Windows 8 app or modify an existing Windows 8 app to connect to your mobile service. 

In this section you will create a new Windows 8 app that is connected to your mobile service.

1.  In the Preview Management Portal, click **Mobile Services**, and then click the mobile service that you just created.

   ![][5]

2. In the quickstart tab, expand **Create a new Windows 8 application**.

   ![][6]

   This displays the three easy steps to create an Windows 8 app connected to your mobile service.

3. If you haven't already done so, download and install [Visual Studio 2012 Express for Windows 8] and the [Mobile Services SDK] on your local computer or virtual machine. 

   ![][7]

   Download links for both are provided on the quickstart page.

4. Click **Create TodoItems table** to create a table to store app data.

   ![][8]

5. Under **Download and run application**, select a language for your app, then click **Download**. 

   ![][9]

  This downloads the project for the sample _To do list_ application that is connected to your mobile service. Save the compressed project file to your local computer, and make a note of where you save it.

### Build and test your app

The final stage of this tutorial is to build and run your new Windows 8 app.

1. Browse to the location where you saved the compressed project files, expand the files on your computer, and open the solution file in Visual Studio 2012 Express for Windows 8. 

2. Press the **F5** key to rebuild the project and start the app.

3. In the app, enter text in **Insert a TodoItem** and then click **Save**.

   ![][10]

   This inserts the text in the TodoItem table in the mobile service. Text stored in the table is returned by the service and displayed in the second column.

4. Back in the Preview Management Portal, click the **Data** tab, then click the **TodoItems** table.

   ![][11]

   This lets you browse the data inserted by the app into the table.

   ![][12]

### <a name="next-steps"> </a>Next Steps
Now that you have completed the quickstart, learn how to perform additional important tasks in Mobile Services: 

* [Get started with data]
  <br/>Learn more about the "magic" behind the quickstart project.

* [Get started with users]
  <br/>Learn how to authenticate users of your app with Windows Account.

* [Get started with push notifications] 
  <br/>Learn how to send a very basic push notification to your app.

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ../Media/mobile-quickstart-completed.png
[1]: ../../Shared/Media/plus-new.png
[2]: ../Media/mobile-create.png
[3]: ../Media/mobile-create-page1.png
[4]: ../Media/mobile-create-page2.png
[5]: ../Media/mobile-services-selection.png
[6]: ../Media/mobile-portal-quickstart.png
[7]: ../Media/mobile-quickstart-get-tools.png
[8]: ../Media/mobile-quickstart-create-table.png
[9]: ../Media/mobile-quickstart-download-app.png
[10]: ../Media/mobile-quickstart-startup.png
[11]: ../Media/mobile-data-tab.png
[12]: ../Media/mobile-data-browse.png

<!-- URLs. -->
[Get started with data]: ./mobile-services-get-started-with-data-dotnet/

[Get started with users]: ./mobile-services-get-started-with-users-dotnet/
[Get started with push notifications]: ./mobile-services-get-started-with-push-dotnet/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[WindowsAzure.com]: http://www.windowsazure.com/
[Management Portal preview]: https://manage.windowsazure.com/