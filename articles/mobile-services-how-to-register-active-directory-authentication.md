<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Register for Windows Azure Active Directory Authentication" pageTitle="Register for Windows Azure Active Directory authentication - Mobile Services" metaKeywords="Windows Azure registering application, Windows Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Windows Azure Active Directory authentication in your Mobile Services application." title="Register your account to use a Windows Azure Active Directory account login" authors="" />

# Register your apps to use a Windows Azure Active Directory Account login

This topic shows you how to register your apps to be able to use Windows Azure Active Directory as an authentication provider for Windows Azure Mobile Services. 

> [WACOM.NOTE] The Windows Azure Active Directory authentication provider for Mobile Services is currently in preview. If you would like to enroll in the preview, please contact <a href="mailto:mobileservices@microsoft.com">mobileservices@microsoft.com</a>. Otherwise, the authentication provider will not be visible from the `Identity` tab of your mobile service.

> [WACOM.NOTE] When you intend to also provide client-driven authentication for single sign-on (SSO) or push notifications from a Windows Store app, consider also registering your app with the Windows Store. For more information, see <a href="/en-us/develop/mobile/how-to-guides/register-for-single-sign-on">Register your Windows Store apps for Windows Live Connect authentication</a>. 

1. Log on to the [Windows Azure Management Portal], click **Mobile Services**, and then click your mobile service.

    ![][1]

2. Click the **Identity** tab for your mobile service. 

    ![][2]

3. Scroll down to the **windows azure active directory** identity provider section and copy the **APP URL** listed there.

    ![][3]

4. Navigate to **Active Directory** in the management portal, then click your directory.

    ![][4] 

5. Click the **Applications** tab at the top, then click to **ADD** an app. 

    ![][10]

6. Click **Add an application my organization is developing**.

7. In the Add Application Wizard, enter a **Name** for your application and click the  **Web Application And/Or Web API** type. Then click to continue.

    ![][5]

8. In the **SIGN-ON URL** box, paste the App ID you copied from the Active Directory identity provider settings of your mobile service. Also enter a unique resource identifier in the **App ID URI** box. The application will use the URI to submit single sign-on requests to the Windows Azure Active Directory. Then click to continue.

    ![][6]

9. Click to enable the **Single Sign-On** access type for the application. Click to complete the Add Application Wizard.

    ![][7]

10. Once the application has been added, scroll to the bottom. Then click the **Enable your app to read or write directory data** section to expand it. Then click to copy the **Client ID** for the app.

    ![][8]


11. Return to your mobile service's **Identity** tab. At the bottom, paste in the **Client ID** setting for the azure active directory identity provider. Then click **Save**.

    ![][9]



You are now ready to use a Windows Azure Active Directory for authentication in your app. 



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png

<!-- URLs. -->
[Windows Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services Preview SDK Download]: http://www.windowsazure.com/
