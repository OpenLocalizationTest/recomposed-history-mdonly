<properties linkid="develop-mobile-tutorials-javascript-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

# Accessing Azure Active Directory Graph Information

<div class="dev-center-tutorial-selector sublanding">
    <a href="/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/" title="Windows Store C#" class="current">Windows Store C#</a>
</div>

<div class="dev-center-tutorial-subselector">
<!--	    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/" title=".NET backend">.NET backend</a> |
-->
	<a href="/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/" title="JavaScript backend" class="current">JavaScript backend</a>
</div>


Like the other identity providers offered with Mobile Services, the Azure Active Directory (AAD) provider also supports a rich [Graph API] which can be used for programmatic access to the directory. In this tutorial you update the ToDoList app to personalize the authenticated user’s app experience based on additional user information you retrieve from the directory using the [Graph API].

>[WACOM.NOTE] The intent of this tutorial is to extend your knowledge of authentication with the Azure Active Directory. It is expected that you have completed the [Get Started with Authentication] tutorial using the Azure Active Directory authentication provider. This tutorial continues to update the TodoItem application used in the [Get Started with Authentication] tutorial.



This tutorial walks you through the following steps:


1. [Generate an access key for the App registration in AAD] 
2. [Create a GetUserInfo custom API] 
3. [Update the app to use the custom API]
4. [Test the app]

##Prerequisites 

Before you start this tutorial, you must have already completed these Mobile Services tutorials:

+ [Get started with authentication]<br/>Adds a login requirement to the TodoList sample app.

+ [Custom API Tutorial]<br/>Demonstrates how to call a custom API. 



## <a name="generate-key"></a>Generate an access key for the App registration in AAD


During the [Get Started with Authentication] tutorial, you created a registration for the integrated application when you completed the [Register to use an Azure Active Directory Login] step. In this section you generate a key to be used when reading directory information with that integrated application's client ID. 

1. Click **Applications** tab on your directory page in the [Azure Management Portal].
  
2. Click your integrated application registration.

3. Click **Configure** on the application page and scroll down the the **keys** section of the page. 
4. Click **1 year** duration for a new key. Then click **Save** and the portal will display your new key value.
5. Copy the **Client ID** and **Key** shown after you save. You will use these values later to replace the `<YOUR-CLIENT-ID>` and `<YOUR-SECRET-KEY>` placeholders in the custom API script later. Note that the key value will only be shown to you a single time after you have saved. 

    ![][1]


## <a name="create-api"></a>Create a GetUserInfo custom API

In this section, you will create the GetUserInfo custom API that will use the [Graph REST API] to retrieve additional information about the user from the AAD.

If you've never used custom APIs with Mobile Services, you should consider reviewing the [Custom API Tutorial] before completing this section.

1. In the [Azure Management Portal], create the new GetUserInfo custom API for your mobile service and set permissions for the get method to **Only Authenticated Users**.

    ![][0]

2. Open the script editor for the new GetUserInfo API and that the following variables to the top of the script.

        var tenant_domain = "<YOUR-TENANT-DOMAIN>";
        var clientID = "<YOUR-CLIENT-ID>";
        var key = "<YOUR-SECRET-KEY>";

    Replace `<YOUR-TENANT-DOMAIN>` with the name of your tenant domain. It should be something like *mydomain.onmicrosoft.com*.

    Replace `<YOUR-CLIENT-ID>` and `<YOUR-SECRET-KEY>`  with the values you noted in the [Generate an access key for the App registration in AAD] section.

3. Add the following definition for the `getAADToken` function.

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
            });
        }

    Given the *tenant_domain*, integrated application *client id*, and application *key*, this function provides a Graph access token used for reading directory information.

4. Add the following `getUser` function which uses the Graph API to return the user's information.

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    This function is a thin wrapper around the [Get User] endpoint of the Graph REST API. It uses the Graph access token to get the user information using the user's object ID.

5. Update the exported `get` method as follows to return user information using the other functions.

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };


## <a name="update-app"></a>Update the app to use GetUserInfo

1. In Visual Studio, open MainPage.xaml.cs and add the following `using` statement to the top of the file.
 
        using System.Net.Http;

2. In MainPage.xaml.cs, add the following class definition to the namespace to help serialize the user information.

        public class UserDetails
        {
            public String displayName { get; set; }
            public String streetAddress { get; set; }
            public String city { get; set; }
            public String state { get; set; }
            public String postalCode { get; set; }
        }


3. In MainPage.xaml.cs, update the `AuthenticateAsync` method you implemented in the [Get Started with Authentication] tutorial to call the custom API to return additional information about the user from the AAD. 

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);
                    UserDetails userDetails = await App.MobileService.InvokeApiAsync<UserDetails>("getuserinfo", 
                        HttpMethod.Get, null);
                    message = string.Format("{0}, you are now logged in.\n\nYour address is...\n\n{1}\n{2}, {3} {4}", 
                        userDetails.displayName, userDetails.streetAddress, userDetails.city, userDetails.state, 
                        userDetails.postalCode);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


4. Save your changes and build the app to verify the changes.  


## <a name="test-app"></a>Test the app

1. Launch the app and authenticate with a user account that has the detailed information in the directory. 

    ![][2]

2. The custom API executes returning the user's directory information in a custom greeting. In this example the user needs to have, *streetAddress*, *city*, *state* and *postalCode* to display the user's address.

    ![][3]

<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app


<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png
[1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/client-id-and-key.png
[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/bob-login.png
[3]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/custom-greeting.png


<!-- URLs. -->
[Get Started with Authentication]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[How to Register with the Azure Active Directory]: /en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure Management Portal]: https://manage.windowsazure.com/
[Custom API Tutorial]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
[Store Server Scripts]: /en-us/documentation/articles/mobile-services-store-scripts-source-control/
[Register to use an Azure Active Directory Login]: /en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph REST API]: http://msdn.microsoft.com/en-us/library/azure/hh974478.aspx
[Get User]: http://msdn.microsoft.com/en-us/library/azure/dn151678.aspx
