---
title: 'Tutorial: Azure Active Directory integration with IBM Kenexa Survey Enterprise | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and IBM Kenexa Survey Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila

ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes

---
# Tutorial: Azure Active Directory integration with IBM Kenexa Survey Enterprise

In this tutorial, you learn how to integrate IBM Kenexa Survey Enterprise with Azure Active Directory (Azure AD).

Integrating IBM Kenexa Survey Enterprise with Azure AD provides you with the following benefits:

- You can control in Azure AD who has access to IBM Kenexa Survey Enterprise
- You can enable your users to automatically get signed-on to IBM Kenexa Survey Enterprise (Single Sign-On) with their Azure AD accounts
- You can manage your accounts in one central location - the Azure portal

If you want to know more details about SaaS app integration with Azure AD, see [what is application access and single sign-on with Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisites

To configure Azure AD integration with IBM Kenexa Survey Enterprise, you need the following items:

- An Azure AD subscription
- An IBM Kenexa Survey Enterprise single sign-on enabled subscription

> [!NOTE]
> To test the steps in this tutorial, we do not recommend using a production environment.

To test the steps in this tutorial, you should follow these recommendations:

- Do not use your production environment, unless it is necessary.
- If you don't have an Azure AD trial environment, you can get a one-month trial [here](https://azure.microsoft.com/pricing/free-trial/).

## Scenario description
In this tutorial, you test Azure AD single sign-on in a test environment. 
The scenario outlined in this tutorial consists of two main building blocks:

1. Adding IBM Kenexa Survey Enterprise from the gallery
2. Configuring and testing Azure AD single sign-on

## Adding IBM Kenexa Survey Enterprise from the gallery
To configure the integration of IBM Kenexa Survey Enterprise into Azure AD, you need to add IBM Kenexa Survey Enterprise from the gallery to your list of managed SaaS apps.

**To add IBM Kenexa Survey Enterprise from the gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon. 

	![Active Directory][1]

2. Navigate to **Enterprise applications**. Then go to **All applications**.

	![Applications][2]
	
3. To add new application, click **New application** button on the top of dialog.

	![Applications][3]

4. In the search box, type **IBM Kenexa Survey Enterprise**.

	![Creating an Azure AD test user](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. In the results panel, select **IBM Kenexa Survey Enterprise**, and then click **Add** button to add the application.

	![Creating an Azure AD test user](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  Configuring and testing Azure AD single sign-on
In this section, you configure and test Azure AD single sign-on with IBM Kenexa Survey Enterprise based on a test user called "Britta Simon".

For single sign-on to work, Azure AD needs to know what the counterpart user in IBM Kenexa Survey Enterprise is to a user in Azure AD. In other words, a link relationship between an Azure AD user and the related user in IBM Kenexa Survey Enterprise needs to be established.

In IBM Kenexa Survey Enterprise, assign the value of the **user name** in Azure AD as the value of the **Username** to establish the link relationship.

To configure and test Azure AD single sign-on with IBM Kenexa Survey Enterprise, you need to complete the following building blocks:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Creating an Azure AD test user](#creating-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
3. **[Creating an IBM Kenexa Survey Enterprise test user](#creating-an-ibm-kenexa-survey-enterprise-test-user)** - to have a counterpart of Britta Simon in IBM Kenexa Survey Enterprise that is linked to the Azure AD representation of user.
4. **[Assigning the Azure AD test user](#assigning-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - to verify whether the configuration works.

### Configuring Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal and configure single sign-on in your IBM Kenexa Survey Enterprise application.

**To configure Azure AD single sign-on with IBM Kenexa Survey Enterprise, perform the following steps:**

1. In the Azure portal, on the **IBM Kenexa Survey Enterprise** application integration page, click **Single sign-on**.

	![Configure Single Sign-On][4]

2. On the **Single sign-on** dialog, select **Mode** as	**SAML-based Sign-on** to enable single sign-on.
 
	![Configure Single Sign-On](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. On the **IBM Kenexa Survey Enterprise Domain and URLs** section, perform the following steps:

	![Configure Single Sign-On](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. In the **Identifier** textbox, type a URL using the following pattern: `https://surveys.kenexa.com/<companycode>`

	b. In the **Reply URL** textbox, type a URL using the following pattern: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

	> [!NOTE] 
	> These values are not real. Update these values with the actual Identifier and Reply URL. Contact [IBM Kenexa Survey Enterprise support team](https://www.ibm.com/support/home/?lnk=fcw) to get these values.

4. On the **SAML Signing Certificate** section, click **Certificate(Base64)** and then save the certificate file on your computer.

	![Configure Single Sign-On](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

5. The IBM Kenexa Survey Enterprise application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. User Identifier claim value in the Response has to match with SSO ID configured in Kenexa system. So please work with [IBM Kenexa Survey Enterprise support team](https://www.ibm.com/support/home/?lnk=fcw) to map the appropriate user identifier in your organization as SSO ID. By default Azure AD will set the User Identifier as UPN value. You can change this from Attribute tab as shown in the screenshot below. The integration will only work after completing the correct mapping.
	
	![Configure Single Sign-On](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)	

6. Click **Save** button.

	![Configure Single Sign-On](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

7. On the **IBM Kenexa Survey Enterprise Configuration** section, click **Configure IBM Kenexa Survey Enterprise** to open **Configure sign-on** window. Copy the **Sign-Out URL, SAML Entity ID, and SAML Single Sign-On Service URL** from the **Quick Reference section.**

	![Configure Single Sign-On](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png) 

8. To configure single sign-on on **IBM Kenexa Survey Enterprise** side, you need to send the downloaded **Certificate(Base64), Sign-Out URL, SAML Entity ID, and SAML Single Sign-On Service URL** to [IBM Kenexa Survey Enterprise support team](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> You can now read a concise version of these instructions inside the [Azure portal](https://portal.azure.com), while you are setting up the app!  After adding this app from the **Active Directory > Enterprise Applications** section, simply click the **Single Sign-On** tab and access the embedded documentation through the **Configuration** section at the bottom. You can read more about the embedded documentation feature here: [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### Creating an Azure AD test user
The objective of this section is to create a test user in the Azure portal called Britta Simon.

![Create Azure AD User][100]

**To create a test user in Azure AD, perform the following steps:**

1. In the **Azure portal**, on the left navigation pane, click **Azure Active Directory** icon.

	![Creating an Azure AD test user](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. To display the list of users, go to **Users and groups** and click **All users**.
	
	![Creating an Azure AD test user](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. To open the **User** dialog, click **Add** on the top of the dialog.
 
	![Creating an Azure AD test user](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. On the **User** dialog page, perform the following steps:
 
	![Creating an Azure AD test user](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a. In the **Name** textbox, type **BrittaSimon**.

    b. In the **User name** textbox, type the **email address** of BrittaSimon.

	c. Select **Show Password** and write down the value of the **Password**.

    d. Click **Create**.
 
### Creating an IBM Kenexa Survey Enterprise test user

In this section, you create a user called Britta Simon in IBM Kenexa Survey Enterprise. 

You can work with [IBM Kenexa Survey Enterprise support team](https://www.ibm.com/support/home/?lnk=fcw) to create the users in their system and map the SSO Id for them. Also this SSO ID value should be mapped to the User Identifier value from Azure AD. You can change this default setting in the Attribute tab.

### Assigning the Azure AD test user

In this section, you enable Britta Simon to use Azure single sign-on by granting access to IBM Kenexa Survey Enterprise.

![Assign User][200] 

**To assign Britta Simon to IBM Kenexa Survey Enterprise, perform the following steps:**

1. In the Azure portal, open the applications view, and then navigate to the directory view and go to **Enterprise applications** then click **All applications**.

	![Assign User][201] 

2. In the applications list, select **IBM Kenexa Survey Enterprise**.

	![Configure Single Sign-On](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. In the menu on the left, click **Users and groups**.

	![Assign User][202] 

4. Click **Add** button. Then select **Users and groups** on **Add Assignment** dialog.

	![Assign User][203]

5. On **Users and groups** dialog, select **Britta Simon** in the Users list.

6. Click **Select** button on **Users and groups** dialog.

7. Click **Assign** button on **Add Assignment** dialog.
	
### Testing single sign-on

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the IBM Kenexa Survey Enterprise tile in the Access Panel, you should get automatically signed-on to your IBM Kenexa Survey Enterprise application.

## Additional resources

* [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](active-directory-saas-tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

