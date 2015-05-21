
## <a id="register"></a>Register App for Push Notifications

* The [Apple Push Notification Service (APNS)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html) uses certificates to authenticate your mobile service. Follow the instructions at [Registering App IDs](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991) to register an App ID for the app. Check the optional **Push Notifications** option in **App Services** when registering the app.

> [AZURE.NOTE] The **Bundle ID** you supply must be identical to the **Bundle ID** of your Xcode project. Use the exact bundle ID value that you already use in your [quickstart project](mobile-services-ios-get-started.md). It is also crucial that you check the **Push Notifications** option in **App Services** when registering the app ID. Push notifications will not work if you don't check this box.

* Follow the steps at [Enabling Push Notifications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6) to now enable push notifications on this app. You may create either a development or production certificate (as long as you select the same option in the Mobile Services portal later.)

* Download the client SSL certificate to your Mac, and remember where you save it. Double-click the downloaded certificate; this installs the new certificate to the iOS Keychain.

* Next, [verify that the app ID enables push notifications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8).

* Finally, [refresh provisioning profiles in Xcode](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10) and then [verify that the provisioning profile was either created or regenerated to enable push notifications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12).
