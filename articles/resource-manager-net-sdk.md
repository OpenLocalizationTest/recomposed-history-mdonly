<properties
   pageTitle="Resource Manager SDK for .NET| Microsoft Azure"
   description="An overview of the Resource Manager .NET SDK authentication and usage examples"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="navale;tomfitz;"/>

# Azure Resource Manager SDK for .NET  
Azure Resource Manager SDKs are available for multiple languages and platforms. Each of these language implementations is available through its ecosystem package manager and GitHub.

The code in each of these SDKs is generated from [Azure RESTful API specifications](https://github.com/azure/azure-rest-api-specs). These specifications are open source and based on the Swagger v2 specification. The SDK code is generated via an open-source project called [AutoRest](https://github.com/azure/autorest). AutoRest transforms these RESTful API specifications into client libraries in multiple languages. If you want to improve any aspects of the generated code in the SDKs, the entire set of tools to create the SDKs are open, freely available, and based on a widely adopted API specification format.

[Azure SDK for .NET](https://azure.microsoft.com/downloads/) is a set of NuGet packages that helps you call most of the APIs exposed by Azure Resource Manager. If the SDK doesn't provide the required functionality, you can easily combine the SDK with regular calls to the Resource Manager REST API behind the scenes.

This article is not intended to describe all aspects of Azure SDK for .NET, Azure Resource Manager APIs, or Visual Studio. It's provided as a fast way for you to get started.

All the following code snippets have been take from a [downloadable sample project](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net).

## Install NuGet packages

The examples in this topic require two NuGet packages (in addition to the Azure SDK for .NET).

1. In Visual Studio, right-click your project and select **Manage NuGet packages**.
2. Search for **Microsoft.IdentityModel.Clients.ActiveDirectory** and install the latest stable version of the package.
3. Search for **Microsoft.Azure.Management.ResourceManager** and select **Include prerelease**. Install the latest preview version (such as 1.1.2-preview).

## Authentication
Azure Active Directory (Azure AD) handles authentication for Resource Manager. To connect to any API, you first need to authenticate with Azure AD to receive an access token that you can pass on to every request. To get this token, you first need to create
what is called an Azure AD Application and a Service Principal that will be used for signing in. For step-by-step instructions, follow one of these articles:

- [Use Azure PowerShell to create an Active Directory application to access resources](resource-group-authenticate-service-principal.md)
- [Use Azure CLI to create an Active Directory application to access resources](resource-group-authenticate-service-principal-cli.md)
- [Use the Azure portal to create Active Directory application that can access resources](resource-group-create-service-principal-portal.md).

After you create the service principal, you should have:

- Client ID or application ID (GUID)
- Client secret or password (string)
- Tenant ID (GUID) or domain name (string)

### Receiving the AccessToken from code
You can acquire the access token by using the following lines of code, passing in only your Azure AD tenant ID, your Azure AD
application client ID, and the Azure AD application client secret. Save the token for several requests, because by default, it's valid for one hour.

```csharp
private static async Task<AuthenticationResult> GetAccessTokenAsync(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
            + $"{tenantId}" /* Tenant ID */);

    var credential = new ClientCredential(clientId, clientSecret);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

Instead of using the tenant ID for signing in, you can use the Active Directory domain, as shown in the following code. Using this approach would require that you change the method signature to include the domain name instead of the tenant ID.

```csharp
AuthenticationContext authContext = new AuthenticationContext
    ("https://login.windows.net/" /* AAD URI */
    + $"{domain}.onmicrosoft.com");
```

You can get the access token for an Active Directory app that uses a certificate for authentication by using this code:

```csharp
private static async Task<AuthenticationResult> GetAccessTokenFromCertAsync(string tenantId, string clientId, string certName)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
        + $"{tenantId}" /* Tenant ID or AAD domain */);

    X509Certificate2 cert = null;
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);

    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false);
        cert = certs[0];
    }
    finally
    {
        store.Close();
    }

    var certCredential = new ClientAssertionCertificate(clientId, cert);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", certCredential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Querying Azure subscriptions attached to the authenticated application
One of the first things you might want to do is query the Azure subscriptions that are associated with the just-authenticated application. The subscription ID for your targeted subscription will be mandatory to pass to each API call that you make from now on.

The following sample code queries Azure APIs directly by using the REST API. That is, it doesn't use any features in Azure SDK for .NET.

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

Notice that you get a JSON response from Azure that you then extract the subscription IDs from, in order to return a list of IDs. All the subsequent calls to Azure Resource Manager APIs in this documentation use a single Azure subscription ID. So if your application is
associated with several subscriptions, just pick the right one of them and pass it as a parameter going forward.

From here, every call that you make against the Azure APIs will use the Azure SDK for .NET, so the code will look a little bit different.

### Wrapping the token as a TokenCredentials object
All of the following API calls will need the token that you received from Azure AD in the format of a "TokenCredentials" object. You can create such an object by passing the raw token as a parameter to the class's constructor.

```csharp
var credentials = new TokenCredentials(token);
```

If you have an earlier version of the Resource Manager NuGet package (named **Microsoft.Azure.Management.Resources**), you need to use the following code:

```csharp
var credentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);
```

## Creating a resource group
Everything in Azure is focused on the resource groups, so let's create one. *ResourceManagementClient* handles general resources and resource groups. For any of the following, more specialized Management Clients that you'll use, you need to
provide your credentials and a subscription ID to identify what subscription you want to work with.

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## Creating resources manually or using templates
There are several ways of interacting with the Azure Resource Manager APIs, but the two main ways are:

* Manually, by calling specific resource providers
* By using an Azure Resource Manager template

Using an Resource Manager template has the following benefits:

* You declaratively specify what you want the end result to look like, rather than how it should be achieved.
* You don't have to manually handle parallel execution of your deployments. Resource Manager will do that for you.
* You don't have to learn C# or any other language to deploy a Resource Manager template, even though you can use any language to start a templated deployment.
* The domain-specific language, DSL, that is used in the templates is built through JSON. It's easy enough to understand by anyone who has worked with JSON.

Even with all the benefits of the templates, we'll start by showing you how to call the APIs manually.

### Creating a virtual machine, piece by piece
So now you have your subscription and your resource group. If you want to deploy a virtual machine (VM), you need to know what it's made of:

* One or many storage accounts, for storing persistent disks
* One or many public IP addresses (PIPs), for being accessible from the Internet (includes a DNS name)
* One or many virtual networks (VNets), for internal communication between your resources
* One or many network interface cards (NICs), to allow the VM to communicate
* One or many virtual machines, to run software

Some of these resources can be created in parallel, but others can't. For example:

* NICs depend on PIPs and VNets.
* VMs depend on NICs and storage accounts.

Don't try to instantiate any resources before the required dependencies have been created. The full [sample](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) provided with this documentation shows how you can efficiently create your resources in parallel while still keeping track of what has been created.

#### Creating the storage account
You need a storage account to store the virtual hard disks for your virtual machine. If you have an existing storage account, you can use it for several VMs. But remember to spread your load across several storage accounts so you don't run into limits. Also remember that the type of storage account and its location can limit the VM size that you can choose, because not all VM sizes are available in all regions or for all storage account types.

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### Creating the public IP address
The public IP address is what makes your resources in Azure accessible from Internet.
Together with the IP address, you'll be assigned a fully qualified domain name (FQDN) that you can use for easier access.

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support Static IP Addresses but could be extended to do so
        });

    return createPipTask;
}
```

#### Creating the virtual network
Every VM created with the Resource Manager APIs needs to be part of a virtual network, even if the VM is alone in it. The virtual network must contain at least one subnet, but you can use multiple subnets to divide and help protect your resources.

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### Creating the network interface card
The NIC is what connects your VM with the virtual network that it resides in.
A VM can have many NICs and hence be associated with several virtual networks. This sample assumes that you're attaching your VMs to only one VNet.

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### Creating the virtual machine
Finally, it's time to create the actual virtual machine. The VM depends (directly or indirectly) on all of the previously created resources, so you need to wait for all of the resources to be ready before you try to provision a VM. Provisioning a VM takes longer than creating the other resources, so expect your application to be waiting a while for this to happen.

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### Using a templated deployment
Please read the [Deploy Azure resources using .NET libraries and a template](./virtual-machines/virtual-machines-windows-csharp-template.md ) article for detailed instructions on how to deploy a template.

In short, deploying a template is much easier than provisioning the resources manually. The following code shows you how to do it by pointing at the URIs where you have the template and a parameters file.

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```
