

By default, all requests to mobile service resources are restricted to clients that present the application key, which does not strictly secure access to resources. To secure your resources, you need to restrict access to authenticated clients only.

1. In Visual Studio, open the project that contains your mobile service. 

2. In Solution Explorer, expand the Controllers folder and open the TodoItemController.cs project file.

	The **TodoItemController** class implements data access for the TodoItem table. 

3. Add the following `using` statement at the top of the code page:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

4. To each method in the **TodoItemController** class, apply the following RequiresAuthorization attribute:

		[RequiresAuthorization(AuthorizationLevel.User)]

	This will ensure that all operations against the **TodoItem** table require an authenticated user. 

