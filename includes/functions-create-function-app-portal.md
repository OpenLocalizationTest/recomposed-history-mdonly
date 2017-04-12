
1. Click the **New** button found on the upper left-hand corner of the Azure portal.

2. Click **Compute** > **Function App**, select your **Subscription**, and enter values for the following required settings:

    | Setting      | Description                                        |
    | ------------ | -------------------------------------------------- |
    | **App name** | A name that uniquely identifies your function app. |
    | **[Resource Group](../articles/azure-resource-manager/resource-group-overview.md)** | Select an existing resource group or **Create new** and enter a name for the new resource group. |
    | **[Hosting plan](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)** | Choose one of the following plans: |
    | | **Consumption plan**: The default plan type for Azure Functions, where resources are added to your function app as needed. When you choose a consumption plan, you must also choose the **Location**.  |
    | |  **App Service plan**: An App Service plan requires you to create an **App Service plan/location** or select an existing one. These settings determine the [location, features, cost, and compute resources](https://azure.microsoft.com/pricing/details/app-service/) associated with your app. You must scale your function app as needed or configure auto scale. |
    | **Storage account** | Each function app requires a storage account. You can either choose an existing storage account or [create a storage account](../articles/storage/storage-create-storage-account.md#create-a-storage-account).| 
         
    ![Create function app in the Azure portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

3. Click **Create** to provision and deploy the new function app.  
