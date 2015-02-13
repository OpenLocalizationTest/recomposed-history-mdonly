<properties
	pageTitle="Service-side Authorization of Users in Mobile Services with .NET Backend | Mobile Dev Center"
	description="Learn how to authorize users in the .NET backend of Azure Mobile Services"
	services="mobile-services"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.topic="article"
	ms.date="2/11/2015"
	ms.author="krisragh"/>

# Service-side Authorization of Users in Mobile Services


This topic shows you how to use server-side scripts to authorize authenticated users for accessing data.  In this tutorial, you modify data access methods in Azure Mobile Services .NET controllers, filter queries based on user IDs, and give users access to only their own data.

This tutorial is based on the Mobile Services Quick Start and builds on the [Add Authentication to Your Mobile Services App] tutorial. Please complete [Add Authentication to Your Mobile Services App] first.

## <a name="register-scripts"></a>Modify the data access methods

Now that authentication is required to access data in the TodoItem table, you can use the UserId value assigned by Mobile Services to filter returned data.

1. In Visual Studio, open your Mobile Services project, expand the DataObjects folder, and open TodoItem.cs. The TodoItem class defines the data object, and you need to add a UserId property to use for filtering. Add the following new UserId property to the **TodoItem** class:

		public string UserId { get; set; }

	>[AZURE.NOTE] To make this data model change and maintain existing data in the database, you must use [Code First Migrations](/en-us/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).

2. Locate the **PostTodoItem** method and add the following code at the beginning of the method:

			// Get the logged-in user.
			var currentUser = User as ServiceUser;

			// Set the user ID on the item.
			item.UserId = currentUser.Id;

		This code adds a UserId value to the item, which is the user ID of the authenticated user, before it is inserted into the TodoItem table.


3. Locate the **GetAllTodoItems** method and replace the existing **return** statement with the following line of code:

				// Get the logged-in user.
				var currentUser = User as ServiceUser;

				return Query().Where(todo => todo.UserId == currentUser.Id);

		This query filters the returned TodoItem objects so that each user only receives the items that they inserted.

4. Republish the mobile service project to Azure.


## <a name="test-app"></a>Test the app

1. Notice that when you now run your client-side app, although there are items already in the database from previous tutorials, no items are returned. This happens because previous items were inserted without the UserId column and now have null values.

2. If you have additional login accounts, verify that users can only see their own data by closing and deleting the app and running it again. When the login credentials dialog is displayed, enter a different login and verify that the items entered under the previous login are not displayed.



<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Add Authentication to Your Mobile Services App]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
