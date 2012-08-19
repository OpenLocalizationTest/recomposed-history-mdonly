<properties linkid="mobile-services-authorize-users-dotnet" urldisplayname="Mobile Services" headerexpose="" pagetitle="Authorize users with Mobile Services" metakeywords="Authorize users with Mobile Services, mobile devices, Windows Azure, mobile, Windows 8, WinRT app, authorization" footerexpose="" metadescription="Authorize users with Windows Azure Mobile Services in your Windows Store apps." umbraconavihide="0" disquscomments="1"></properties>

# Use scripts to authorize users in Mobile Services
Language: **C# and XAML**  

This topic shows you how to use server scripts to authorize authenticated users for accessing data in Windows Azure Mobile Services from a Windows Store app.  In this tutorial, you register scripts with Mobile Services to filter queries based on the userId of an authenticated user. This makes sure that users can only access stored data with a matching userId value or where userId is null.

This tutorial is based on the Mobile Services quickstart and builds on the previous tutorial [Get started with users]. Before you start this tutorial, you must first complete [Get started with users].  

### <a name="register-scripts"></a>Register server scripts
Because the quickstart app reads and inserts data, you need to register scripts for these operations against the TodoItem table.

1. Log into the [Windows Azure Management Portal], click **Mobile Services**, and then click your app. 

   ![][0]

2. Click the **Data** tab, then click the **TodoItem** table.

   ![][1]

3. Click **Script**, then select the **Insert** operation.

   ![][2]

4. Replace the existing script with the following function, and then click **Save**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    This script adds a userId value to the item, which is the user ID of the authenticated user, before it is inserted into the TodoItem table. 

    <div class="dev-callout"><b>Note</b>
	<p>Dynamic schema must be enabled the first time that this insert script runs. With dynamic schema enabled, Mobile Services automatically adds the userId column to the TodoItem table on the first execution. Dynamic schema is enabled by default for a new mobile service, and it should be disabled before the service is published.</p>
    </div>


5. Repeat steps 3 and 4 to replace the existing **Read** operation with the following function:

        function read(query, user, context) {
           query.where({ userId: user.userId });    
           context.execute();
        }

   This script filters the returned TodoItem objects so that each user only receives the items that they inserted.

### Run the app to test the new behavior

1. In Visual Studio 2012 Express for Windows 8, open the project that you modified when you completed the tutorial [Get started with users].

2. Press the F5 key to run the app and sign into Live Connect with your Microsoft Account. 

   Notice that this time, although there are items already in the TodoItem table from preview tutorials, no items are returned. This happens because previous items were inserted without the userId column and now have null values.

3. In the app, enter text in **Insert a TodoItem** and then click **Save**.

   ![][3]

   This inserts both the text and the userId in the TodoItem table in the mobile service. Because the new item has the correct userId value, it is returned by the mobile service and displayed in the second column.

5. In the Browse to the TodoItem data in the portal and you will see that each item has an associated userId.

6. (Optional) If you have an additional Microsoft Account, you can verify that users can only see their own data by closing the app (Alt+F4) and then running it again. When the login credentials dialog is displayed, enter a different Microsoft account and then verify that the items entered under the previous account are not displayed. 

### Additional reading

This concludes the tutorials that demonstrate the basics of working with users. Consider finding out more about the following Mobile Services topics:

* [Get started with data]
  <br/>Learn more about storing and querying data using Mobile Services.

* [Get started with push notifications] 
  <br/>Learn how to send a very basic push notification to your app.

* [Mobile Services server script reference]
  <br/>Learn more about registering and using server scripts.

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ../Media/mobile-services-selection.png
[1]: ../Media/mobile-portal-data-tables.png
[2]: ../Media/mobile-insert-script-users.png
[3]: ../Media/mobile-quickstart-startup.png

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: ./mobile-services-get-started#create-new-service/
[Get started with data]: ./mobile-services-get-started-with-data-dotnet/
[Get started with users]: ./mobile-services-get-started-with-users-dotnet/
[Get started with push notifications]: ./mobile-services-get-started-with-push-dotnet/
[JavaScript and HTML]: mobile-services-win8-javascript/
[WindowsAzure.com]: http://www.windowsazure.com/
[Windows Azure Management Portal]: https://manage.windowsazure.com/