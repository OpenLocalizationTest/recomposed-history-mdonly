
1. Go to the [Azure portal](https://portal.azure.com) and sign-in with your Azure account.

2. Click **+New** > **Compute** > **Function App**, select your **Subscription**, type a unique **App name** that identifies your function app, then specify the following settings:
   
   * **[Resource Group](../articles/azure-resource-manager/resource-group-overview.md)**: Select **Create new** and enter a name for your new resource group. 
   * **[Hosting plan](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**, which can be one of these plans: 
     * **Consumption plan**: The default plan type for Azure Functions. When you choose a consumption plan, you must also choose the **Location** and set the **Memory Allocation** (in MB). For information about how memory allocation affects costs, see [Azure Functions pricing](https://azure.microsoft.com/pricing/details/functions/). 
     * **App Service plan**: An App Service plan requires you to create an **App Service plan/location** or select an existing one. These settings determine the [location, features, cost, and compute resources](https://azure.microsoft.com/pricing/details/app-service/) associated with your app.  
   * **Storage account**: Each function app requires a storage account. You can either choose an existing storage account or create one. 
     
    ![Create function app in the Azure portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

	You must enter a valid **App name**, which can contain only letters, numbers, and hyphens. Underscore (**_**) is not an allowed character. For more information, see [Create a function app from the Azure portal](functions-create-function-app-portal.md).

3. Click **Create** to provision and deploy the new function app.  