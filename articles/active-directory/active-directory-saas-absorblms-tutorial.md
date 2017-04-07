---
title: 'Tutorial: Azure Active Directory integration with Absorb LMS | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Absorb LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila

ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: jeedes

---
# Tutorial: Azure Active Directory integration with Absorb LMS

In this tutorial, you learn how to integrate Absorb LMS with Azure Active Directory (Azure AD).

Integrating Absorb LMS with Azure AD provides you with the following benefits:

- You can control in Azure AD who has access to Absorb LMS
- You can enable your users to automatically get signed-on to Absorb LMS (Single Sign-On) with their Azure AD accounts
- You can manage your accounts in one central location - the Azure Management portal

If you want to know more details about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisites

To configure Azure AD integration with Absorb LMS, you need the following items:

- An Azure AD subscription
- A Absorb LMS single-sign on enabled subscription

> [!NOTE]
> To test the steps in this tutorial, we do not recommend using a production environment.

To test the steps in this tutorial, you should follow these recommendations:

- You should not use your production environment, unless this is necessary.
- If you don't have an Azure AD trial environment, you can get an one-month trial [here](https://azure.microsoft.com/pricing/free-trial/).

## Scenario description
In this tutorial, you test Azure AD single sign-on in a test environment. 
The scenario outlined in this tutorial consists of two main building blocks:

1. Adding Absorb LMS from the gallery
2. Configuring and testing Azure AD single sign-on

## Adding Absorb LMS from the gallery
To configure the integration of Absorb LMS into Azure AD, you need to add Absorb LMS from the gallery to your list of managed SaaS apps.

**To add Absorb LMS from the gallery, perform the following steps:**

1. In the **[Azure Management Portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon. 

	![Active Directory][1]

2. Navigate to **Enterprise applications**. Then go to **All applications**.

	![Applications][2]
	
3. Click **Add** button on the top of the dialog.

	![Applications][3]

4. In the search box, type **Absorb LMS**.

	![Creating an Azure AD test user](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_search.png)

5. In the results panel, select **Absorb LMS**, and then click **Add** button to add the application.

	![Creating an Azure AD test user](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

##  Configuring and testing Azure AD single sign-on
In this section, you configure and test Azure AD single sign-on with Absorb LMS based on a test user called "Britta Simon".

For single sign-on to work, Azure AD needs to know what the counterpart user in Absorb LMS is to a user in Azure AD. In other words, a link relationship between an Azure AD user and the related user in Absorb LMS needs to be established.

This link relationship is established by assigning the value of the **user name** in Azure AD as the value of the **Username** in Absorb LMS.

To configure and test Azure AD single sign-on with Absorb LMS, you need to complete the following building blocks:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Creating an Azure AD test user](#creating-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
3. **[Creating an Absorb LMS test user](#creating-an-absorb-lms-test-user)** - to have a counterpart of Britta Simon in Absorb LMS that is linked to the Azure AD representation of her.
4. **[Assigning the Azure AD test user](#assigning-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - to verify whether the configuration works.

### Configuring Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure Management portal and configure single sign-on in your Absorb LMS application.

**To configure Azure AD single sign-on with Absorb LMS, perform the following steps:**

1. In the Azure Management portal, on the **Absorb LMS** application integration page, click **Single sign-on**.

	![Configure Single Sign-On][4]

2. On the **Single sign-on** dialog, as **Mode** select **SAML-based Sign-on** to enable single sign on.
 
	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. On the **Absorb LMS Domain and URLs** section, perform the following steps:

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. In the **Identifier** textbox, type a URL using the following pattern: `https://<domain>.myabsorb.com/Account/SAML`

	b. In the **Reply URL** textbox, type a URL using the following pattern: `https://<domain>.myabsorb.com/Account/SAML`

	> [!NOTE] 
	> Please note that these are not the real values. You have to update these values with the actual Identifier and Reply URL. Contact [Absorb LMS support team](https://www.absorblms.com/support) to get these values.
 
4. On the **SAML Signing Certificate** section, click **Metadata XML** and then save the XML file on your computer.

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Click **Save** button.

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)

6. On the **Absorb LMS Configuration** section, click **Configure Absorb LMS** to open **Configure sign-on** window. Copy the **Sign-Out URL and SAML Single Sign-On Service URL** from the **Quick Reference section.**

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png)

7. In a different web browser window, log into your Absorb LMS company site as an administrator.

8. Click on **Account Icon** on the very top right corner of the admin interface. 

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/1.png)

9. Navigate to **Portal Settings**.

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/2.png)
	
10.	On the Portal Settings, click on **Users** tab.

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/3.png)

11. Perform the following steps to access the Single Sign-On configuration fields:

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/4.png)

	a. Select the appropriate **Mode**.

	> [!Note:]
	> Mode: Both IdP & SP initiated are supported.

	b. In the **Key** textbox paste the X509 certificate from your Metadata XML file which you have downloaded from Azure portal.

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/certificate.png)
	
	c. In the **Id Property** choose the Absorb field that your Azure AD username (i.e user principal name) has been mapped to.

	d. In the **Login URL** paste the **“SAML Single Sign-On Service URL”** value you have copied from the **Configure sign-on** window of the Azure portal.

	e. In the **Logout URL** paste the **“Sign-Out URL”** value you have copied from the **Configure sign-on** window of the Azure portal.

12. Enable **Only Allow SSO Login** and/or **Automatically Redirect** as desired 

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/5.png)

13. Click **"Save"**.


### Creating an Azure AD test user
The objective of this section is to create a test user in the Azure Management portal called Britta Simon.

![Create Azure AD User][100]

**To create a test user in Azure AD, perform the following steps:**

1. In the **Azure Management portal**, on the left navigation pane, click **Azure Active Directory** icon.

	![Creating an Azure AD test user](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Go to **Users and groups** and click **All users** to display the list of users.
	
	![Creating an Azure AD test user](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. At the top of the dialog click **Add** to open the **User** dialog.
 
	![Creating an Azure AD test user](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. On the **User** dialog page, perform the following steps:
 
	![Creating an Azure AD test user](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. In the **Name** textbox, type **BrittaSimon**.

    b. In the **User name** textbox, type the **email address** of BrittaSimon.

	c. Select **Show Password** and write down the value of the **Password**.

    d. Click **Create**.
 
### Creating an Absorb LMS test user

In order to enable Azure AD users to log into Absorb LMS, they must be provisioned into Absorb LMS.  
In the case of Absorb LMS, provisioning is a manual task.

**To provision a user accounts, perform the following steps:**

1. Log in to your Absorb LMS company site as an administrator.

2. Click **“Users”** tab on left panel.

    ![Invite People](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. Click **“Users sub-menu”** on left panel.

    ![Invite People](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4.  Click on **“User”** under **Add New** drop down.

    ![Invite People](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. On the **“Add User”** page, fill in the mandatory fields as the following.

	![Invite People](./media/active-directory-saas-absorblms-tutorial/user.png)

	a. In the **First Name** textbox, type the first name like Britta.

	b. In the **Last Name** textbox, type the last name like Simon.
	
	c. In the **Username** textbox, type the username of Britta Simon account.

	d. In the **Password** textbox, type the password of Britta Simon.

	e. In the **Confirm Password** dropdown, select the confirm password  of Britta Simon.
	
	f. Toggle **IsActive** button.	

6. Click **"Save"**. 
	

### Assigning the Azure AD test user

In this section, you enable Britta Simon to use Azure single sign-on by granting her access to Absorb LMS.

![Assign User][200] 

**To assign Britta Simon to Absorb LMS, perform the following steps:**

1. In the Azure Management portal, open the applications view, and then navigate to the directory view and go to **Enterprise applications** then click **All applications**.

	![Assign User][201] 

2. In the applications list, select **Absorb LMS**.

	![Configure Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. In the menu on the left, click **Users and groups**.

	![Assign User][202] 

4. Click **Add** button. Then select **Users and groups** on **Add Assignment** dialog.

	![Assign User][203]

5. On **Users and groups** dialog, select **Britta Simon** in the Users list.

6. Click **Select** button on **Users and groups** dialog.

7. Click **Assign** button on **Add Assignment** dialog.
	
### Testing single sign-on

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

If you want to test your single sign-on settings, open the Access Panel. For more details about the Access Panel, see [Introduction to the Access Panel](https://msdn.microsoft.com/library/dn308586). When you click the Absorb LMS tile in the Access Panel, you should get automatically signed-on to your Absorb LMS application.

## Additional resources

* [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](active-directory-saas-tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png

