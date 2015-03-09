<properties 
   pageTitle="Azure Mobile Engagement Troubleshooting Guide - SDK" 
   description="Troubleshooting Guides for Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Troubleshooting Guide - SDK

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-troubleshooting-guide/" title="Introduction">Introduction</a>
<a href="../mobile-engagement-troubleshooting-guide-analytics/" title="Analytics">Analytics</a>
<a href="../mobile-engagement-troubleshooting-guide-apis/" title="APIs">APIs</a>
<a href="../mobile-engagement-troubleshooting-guide-push-reach/" title="Push/Reach">Push/Reach</a>
<a href="../mobile-engagement-troubleshooting-guide-service/" title="Service">Service</a>
<a href="../mobile-engagement-troubleshooting-guide-sdk/" title="SDK">SDK</a>
<a href="../mobile-engagement-troubleshooting-guide-sr-info/" title="SR">SR Info</a>
</div>

# SDK

Issues with how Azure Mobile Engagement integrates into your application.

**Symptom List:**

1. <a href="#SDK1">Issues with the Azure Mobile Engagement SDK discovered by a failure in another area of your application</a>
2. <a href="#SDK2">Advanced Coding Issues</a>
3. <a href="#SDK3">Application Crash Issues</a>
4. <a href="#SDK4">App Store Upload Failures</a>
 
<a name="#SDK1">
## 1. Issues with the Azure Mobile Engagement SDK discovered by a failure in another area of your application:</a>

- UI data collection failure (in Analytics, Monitoring, Segmentation, or Dashboards).
- Push Failures (Pushes don't work in app, out of app, or both).
- Advanced Feature Failures (Tracking, Geolocation, or platform specific Pushes don’t work).
- API Failures (APIs fail often silently without error messages).
- Service Failures (none of Azure Mobile Engagement works for your application).

**Causes:**

- Most issues that need to be resolved with the Azure Mobile Engagement SDK will be discovered by a failure in your application (such as a UI data collection failure, push failure, advanced feature failure, API failure, Application crashes, or apparent service outage).  
- If a particular feature of Azure Mobile Engagement has never worked in your app before, you will need to complete the integration. 
- If a particular feature of Azure Mobile Engagement was working and stopped, you may need to upgrade to the last version with the Azure Mobile Engagement SDK. Remember that there is a different version of the Azure Mobile Engagement SDK for each platform supported by Azure Mobile Engagement (Android, iOS, Web, Windows, and Windows Phone).

*SDK Integration:* 

- Azure Mobile Engagement not correctly integrated in SDK (Analytics).
- Reach not correctly integrated in SDK (In App and Out of App Pushes).
- Certificate expired or incorrect PROD vs. DEV (iOS only).
- GCM or ADM not correctly integrated in SDK (Android only - Service Specific Pushes).
- Tracking not correctly integrated in SDK (Install store tracking).
- Lazy Location or GPS Location not correctly integrated in SDK (Targeting by geo-location).
[SDK Documentation - Integration Guides][Link 5], [Troubleshooting Guide - Push][Link 23]

*SDK Upgrade:*

- Need to upgrade SDK to resolve issues with older versions of the SDK (often related to newer versions of the device OS).
- Uninstall all previous versions of your app from your device and reinstall the newest version of your app, the re-register your Device ID from the Azure Mobile Engagement UI to confirm that your device is using the newest version of your app.
[SDK Documentation - Release Notes](http://go.microsoft.com/fwlink/?LinkId= 525554), [SDK Documentation - Upgrade Guides](http://go.microsoft.com/fwlink/?LinkId= 525554), [UI Documentation - Settings][Link 20]

*SDK Other:*

- Errors in sections of code related to Azure Mobile Engagement can cause Azure Mobile Engagement not to work.
- Errors in Application Manifest "AndroidManifest.xml" can cause Azure Mobile Engagement not to work (Android only).
- A common issue with SDK integration and 
- API usage is to confuse the SDK Key and the API Key.
[Concepts - Glossary][Link 6]
 
<a name="#SDK2">
## 2. Advanced Coding Issues:</a>

-  Platform specific code not directly related to Azure Mobile Engagement can cause issues on iOS, Android, and Windows Phone.

**Causes:**

- Many advanced coding issues with Azure Mobile Engagement are caused by improperly written platform specific code not directly related to Azure Mobile Engagement. You will need to consult documentation specific to the platform you are developing for in addition to Azure Mobile Engagement documentation (Android, iOS, Web, Windows, and Windows Phone).
- Not correctly configuring "categories", prevents linking from a notification to another location either inside or outside of the app (Android only). 
- Not setting "UIKit.framework" to "optional" in your iOS code, shows a "Symbol not found error" and/or crashes on older iOS devices (iOS only).
- Expired certificates or not correctly using the DEV or Prod version of the cert, causes push issues (iOS only).
- There are some limitations inherent to a platform that Azure Mobile Engagement can't control (like how the system center works for out of app pushes in Android and iOS).
- Azure Mobile Engagement publishes a full list of the internal packages used by Azure Mobile Engagement for iOS and Android for reference. Keep in mind that some features of Azure Mobile Engagement are specific to the platform (Android, iOS, Web, Windows, and Windows Phone).
- The SDKs for each platform are written in the following programming languages:
    -     Android SDK written in Java
    -     iOS SDK written in objective C
    -     Web SDK written in JavaScript
    -     Windows SDK written in C# and JavaScript
    -     Windows Phone SDK written in C# and JavaScript

**See also:**

 - [Troubleshooting Guide - Push][Link 23], [SDK Documentation - Release Notes][Link 5], [SDK Documentation - Upgrade Guides][Link 5], [SDK Documentation - Android - Azure Mobile Engagement technical documentation overview][Link 5], [SDK Documentation - iOS - Azure Mobile Engagement technical documentation overview][Link 5], [SDK Documentation - iOS - How to Prepare your Application for Apple Push notifications][Link 5], [Android Developer](https://developer.android.com/), [iOS Developer](https://developer.apple.com/), [Windows Developer](https://developer.windows.com/) 
 
<a name="#SDK3">
## 3. Application Crash Issues</a>

- Your application crashes on the end users' device.

**Causes:**

- Crash information can be viewed in the Analytics UI or the Analytics API.
- You can find the Device ID of your test device and take the same action that caused your application to crash for an end user to help identify the cause of your crash.
- Known issues with the Azure Mobile Engagement SDK that cause applications to crash are sometimes resolved by upgrading to the latest version of the SDK, so make sure to check the release notes about your platform when investigating crashes.

**See also:**

- [Concepts - FAQ][Link 6], [Concepts - Glossary][Link 6], [API Documentation - Analytics API - Crashes][Link 4], [UI Documentation - Analytics - Crashes][Link 15], [UI Documentation - Settings][Link 20], [SDK Documentation - Release Notes][Link 5], [SDK Documentation - Upgrade Guides][Link 5]

<a name="#SDK4">
## 4. App Store Upload Failures</a>

- Errors related to uploading the latest version of your app to iTunes, GooglePlay, the Windows or Windows Phone store.

**Causes:**

- App stores sometimes block apps with certain features enabled (The iTunes store prevents the use of IDFV in apps in the store and the GooglePlay store prevents the sharing of application information between apps). 
- Make sure that you check the release notes about your platform and current version of the SDK if you have difficulty uploading an app to the store.

**See also:**

- [SDK Documentation - Release Notes][Link 5], [SDK Documentation - Upgrade Guides][Link 5] 

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-user-interface-reach-campaign/
[Link 28]: ../mobile-engagement-user-interface-reach-criterion/
[Link 29]: ../mobile-engagement-user-interface-reach-content/
