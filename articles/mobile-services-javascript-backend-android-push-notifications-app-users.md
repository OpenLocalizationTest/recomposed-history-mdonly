<properties pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Send push notifications to authenticated users" authors="wesmc" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="09/29/2014" ms.author="wesmc" />

# Send push notifications to authenticated users

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

This topic shows you how to send push notifications to an authenticated user on any registered device. Unlike the previous [push notification][Get started with push notifications] tutorial, this tutorial changes your mobile service to require that a user be authenticated before the client can register with the notification hub for push notifications. Registration is also modified to add a tag based on the assigned user ID. Finally, the server script is updated to send the notification only to the authenticated user instead of to all registrations.

This tutorial walks you through the following process:

+ [Updating the service to require authentication for registration]
+ [Updating the app to log in before registration]
+ [Testing the app]
 
This tutorial supports Android apps.

##Prerequisites 

Before you start this tutorial, you must have already completed these Mobile Services tutorials:

+ [Get started with authentication]<br/>Adds a login requirement to the TodoList sample app.

+ [Get started with push notifications]<br/>Configures the TodoList sample app for push notifications by using Notification Hubs. 

After you have completed both tutorials, you can prevent unauthenticated users from registering for push notifications from your mobile service.

##<a name="register"></a>Update the service to require authentication to register

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)] 

<ol start="5"><li><p>Replace the insert function with the following code, then click <strong>Save</strong>:</p>
<pre><code>function insert(item, user, request) {

    // Define a payload for the Google Cloud Messaging toast notification.
    var payload = 
        '{"data":{"message" : "Hello from Mobile Services! An Item was inserted"}}';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
            // registered to the logged-in user as a tag.
            push.gcm.send(userId, payload, {
                success: function(pushResponse) {
                    console.log("Sent push with " + userId + " tag:", pushResponse, payload);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                },
                error: function(err) {
                    console.log("request.execute error", err)
                    request.respond();
                }
            });
}</code></pre>

<p>This insert script uses the user ID tag to send a push notification (with the text of the inserted item) to all Google Cloud Messaging registrations created by the logged-in user.</p></li></ol>

##<a name="update-app"></a>Update the app to log in before registration

[WACOM.INCLUDE [mobile-services-android-push-notifications-app-users](../includes/mobile-services-android-push-notifications-app-users.md)] 

##<a name="test"></a>Test the app

[WACOM.INCLUDE [mobile-services-android-test-push-users](../includes/mobile-services-android-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]:#next-steps


<!-- URLs. -->
[Get started with authentication]: /en-us/documentation/articles/mobile-services-android-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push/

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
