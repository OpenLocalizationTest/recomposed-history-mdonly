# How to implement single sign-on with Windows Azure Active Directory for Office 365 users -Java Application#

<h2>Table of Contents</h2>
<ul>
<li>
<a href="#overview">Overview</a>
</li>
<li>
<a href="#prerequisites">Prerequisites</a></li>
<li><a href="#step1">Step 1 - Create a PHP application</a></li>
<li><a href="#step2">Step 2 - Provision the PHP application in Office 365</a></li>
<li><a href="#step3">Step 3 - Protect the PHP application via WS-Federation and onboard the first customer</a></li>
<li><a href="#step4">Step 4 - Configure the PHP application for single sign-on with multiple tenants</a></li>	

<a name="overview"></a>
## Overview ##
This guide provides instructions for creating a PHP application and configuring it to leverage Windows Azure Active Directory to accept Office 365 users. 

Imagine the following scenario:

- Fabricam is an independent software vendor with a PHP application

- Awesome Computers has a subscription to Office 365

- Trey Research Inc. has a subscription to Office 365



Awesome Computers wants to provide their users (employees) with the access to the Fabricam's PHP application.  After some deliberation, both parties agree to utilize the web single sign-on approach, also called identity federation. Using this approach,  Awesome Computers will provide single sign-on access to their users through a federated mechanism that relies on a Security Token Service (STS). Since Awesome Computers does not have its own STS, they will rely on the STS provided by Office 365. In the end, Awesome Computers' users will be able to access Fabricam's PHP application in exactly the same way they access Office 365 applications.  And I think here we need to add something about ACS and AAD. But what can I say? Can I say that ACS is officially part of AAD? Should I even name ACS?

In the instructions provided in this guide, we will play the roles of both Fabricam and Awesome Computers and recreate this scenario by performing the following tasks: 

- Create a simple PHP application (performed by Fabricam)
- Provision a PHP application in Office 365 (performed by Awesome Computers).

	**Note:** As part of this step, Awesome Computers must in turn be provisioned by the Fabricam as a customer of their PHP application. Basically, Fabricam needs to know that users from the Office 365 tenant with the domain **awesomecomputers.onmicrosoft.com** should be granted access to their PHP application.
- Protect the PHP application with WS-Federation and onboard the first customer (performed by Fabricam)
- Modify the PHP application to handle single sign-on with multiple tenants (performed by Fabricam)

**Assets**

This guide is available together with several code samples and scripts that can help you with some of the most time-consuming tasks. All materials are available here for you to study and modify to fit your environment. 

<a name="prerequisites"></a>
## Prerequisites ##

To complete the tasks in this guide, you will need the following:

**General environment requirements:**

- Internet Information Services (IIS) 7.5 (with SSL enabled)
- Windows PowerShell 2.0
- <a href="http://onlinehelp.microsoft.com/en-us/office365-enterprises/hh124998.aspx ">Microsoft Online Services Module</a>

**PHP-specific requirements:**
<ul>
    <li>PHP 5.3.1 (through Web Platform Installer)</li>
    <li>
      <a href=" http://www.eclipse.org/pdt/downloads/">Eclipse PDT 3.0.x All In Ones </a>
    </li>
   



<a name="step1"></a>
## Step 1 - Create a PHP application ##

The instructions in this step demonstrate how to create a simple PHP application. In our scenario, this step is performed by Fabricam.


**To create a PHP application:**

1.	Open a new instance of Eclipse.
2.	Create a new project: File -> New Project -> PHP Project
3.	In the first wizard window provide a project name and a folder where the project will be created and click Finish.

	<img src="../../../DevCenter/PHP/Media/phpstep1Step3.png" />

4. Select the sample project, Right-click and select New -> PHP File.

	<img src="../../../DevCenter/PHP/Media/phpstep1Step4.png" />

5.	Provide a name for the page and click Finish.

	<img src="../../../DevCenter/PHP/Media/phpstep1Step5.png" />

6.	Replace the generated code with the following:

	<img src="../../../DevCenter/PHP/Media/phpstep1Step6.png" />

7.	Open Internet Information Service (IIS) Manager.
8.	Right-click the Default Web Site item and choose Add Application…

	<img src="../../../DevCenter/PHP/Media/phpstep1Step8.png" />

9.	Provide an alias and the path where the phpSample project is created and click OK.

	<img src="../../../DevCenter/PHP/Media/phpstep1Step9.png" />

10.	Go back to Eclipse, click the arrow beside the play button on the toolbar and select Index.

	<img src="../../../DevCenter/PHP/Media/phpstep1Step10.png" />

11.	At this point you should be able to browse the index page.

	<img src="../../../DevCenter/PHP/Media/phpstep1Step11.png" />

12.	Open a Powershell console and run the following command to generate a new GUID for this application:

	<img src="../../../DevCenter/PHP/Media/phpstep1Step12.png" />
	
*Note:* Make sure to record this value. This identifier will be the AppPrincipalId used in further steps in this guide when provisioning this JAVA web applicaiton in Office 365. 

<a name="step2"></a>
## Step 2 - Provision the PHP application in Office 365 ##

Instructions in this step demonstrate how you can provision the PHP application in the Office 365 tenant. In our scenario, this step is performed by Awesome Computers.  Then Awesome Computers provides the application owner (Fabricam) with the data Fabricam needs in order to set up single sign-on access for Awesome Computers's users. 

Note: If you don’t have access to an Office 365 tenant, you can obtain one by applying for a FREE TRIAL subscription on the [Office 365’s Sign-up page](http://www.microsoft.com/en-us/office365/online-software.aspx#fbid=8qpYgwknaWN). 

To provision the PHP application in Office 365, Awesome Computers creates a new Service Principal for it in the Office 365 tenant. In order to create a new Service principal for the PHP application in the Office 365 tenant, Awesome Computers must obtain the following information from Fabricam:

- The value of the ServicePrincipalName (phpSample/localhost)
- The AppPrincipalId (d184f6dd-d5d6-44c8-9cfa-e2d630dea392)
- The ReplyUrl 

**To provision the PHP applicaiton in Office 365**

1.	Download and install a set of Powershell scripts from the Office 365’s online help page. (I need a link here).
2.	Locate the CreateServicePrincipal.ps1 script in this code example set under WAAD.WebSSO.PHP/Scripts (i need a proper link here)

3.	Launch the Microsoft Online Services Module for Windows PowerShell console.

4.	Run the SampleAppServicePrincipal.ps1 command from the Microsoft Online Services Module for Windows PowerShell Console.

	<img src="../../../DevCenter/PHP/Media/phpstep2Step4.png" />	

	When asked to provide a name for your Service Principal, type in a descriptive name that you can remember in case you wish to inspect or remove the Service Principal later on.

	<img src="../../../DevCenter/PHP/Media/phpstep2Step45.png" />

5.	When prompted, enter your administration credentials for your Office365 tenant:

	<img src="../../../DevCenter/PHP/Media/phpstep2Step5.png" />

6.	If the script runs successfully, your screen will look similar to the figure below. Make sure to record the values of the following for use later in this guide:

- company ID
- AppPrincipal ID
- App Principal Secret
- Audience URI


	<img src="../../../DevCenter/PHP/Media/phpstep2Step6.png" />

*Note: In the command shown here, AppPrincipalId values are those provided by Fabrikam.*

The Fabricam's PHP application has been successfully provisioned in the directory tenant of Awesome Computers. 

Now Fabricam must provision Awesome Computers as a customer of the PHP application. In other words, Fabricam must know that users from the Office 365 tenant with domain *awesomecomputers.onmicrosoft.com* should be granted access to the PHP applicaiton. How that information reaches Fabricam depends on how the subscriptions are handled. In this guide, the instructions for this provisioning step are not provided. 

<a name="step3"></a>
## Step 3 - Protect the PHP application via WS-Federation and onboard the first customer##

The instructions in this step demonstrate how to add support for federated login to the JAVA web application created in Step 1. In our scenario, this step is performed by Fabricam. 

This step is performed by using the **federation** and **simpleSAML.php** libraries  and adding some extra artifacts, like a login page. With the application ready to authenticate requests using the WS-Federation protocol, we’ll add the Windows Azure Active Directory tenant of Awesome Computers as a trusted provider.

1.	Create a federation.ini file in the project’s root and provide the following information:

	*NOTE: **audienceuri=** and **realm=** are the values you retrieved from the PowerShell command above. Remember that you must add **spn:** to be beginning of this value. Use the **audienceuri** for both values below.*

	<img src="../../../DevCenter/PHP/Media/phpstep3Step1.png" />

2.	Create a new PHP file called secureResource.php, include the following code, and replace the highlighted path according to the library’s folder location (the federation and the simpleSAMLphp folders should be inside the phpLibraries folder):

	<img src="../../../DevCenter/PHP/Media/phpstep3Step2.png" />

3.	Open the index.php page to secure the page by adding the require_once sentence and the code for listing the claims for the authenticated user.

	<img src="../../../DevCenter/PHP/Media/phpstep3Step3.png" />

4.	Run the project. You should be automatically redirected to the Office365 page where you can log in using your awesomecomputers.onmicrosoft.com credentials (e.g. john.doe@awesomecomputers.onmicrosoft.com).

	<img src="../../../DevCenter/PHP/Media/phpstep3Step4.png" />

5.	Finally, if the login process is successful you will be redirected to the secured page (phpSample/index.php) as an authenticated user.

	<img src="../../../DevCenter/PHP/Media/phpstep3Step5.png" />


**Important:** If your application is meant to work with a single Office 365 tenant, for example, if you are writing a LoB application, you can stop following the instructions in this guide at this point. By running the three steps above, you have successfully set up Windows Azure AD-enabled single sign-on to a simple PHP application for the users in one Office 365 tenant.

If, however, you are developing applications that need to be accessed by more than one tenant, the next step can help you modify your code to accommodate multiple tenants.  

<a name="step4"></a>
## Step 4 - Configure the PHP application for single sign-on with Multiple tenants ##

What if Fabricam wants to provide access to its application to multiple customers? The steps we performed in this guide so far ensure that single sign-on works with only one trusted provider. Fabricam's developers must make some changes to their PHP application in order to provide single sign-on to whatever future customers they obtain. The main new features needed are:

- Support for multiple identity providers in the login page
- Maintenance of the list of all trusted providers and the audienceURI they will send to the application; That list can be used to determine how to validate incoming tokens

Let's add another fictitious customer to our scenario, Trey research Inc. Trey Research Inc. must register Fabricam's PHP application in its tenant the same way Awesome Computers have done in Step 2. The following is the list of configuration changes that Fabricam needs to perform to their PHP application to enable multi-tenant single sign-on, intertwined with the provisioning of Trey Research Inc.

1.	From Eclipse, Right-click the phpSample project, select New -> Xml File and provide “trustedIssuers.xml” as the file name. This file will contain a list of the trusted issuers for the application (in this case with Awesome Computers and Trey Research Inc.) which will be used by the dynamic audience Uri validator.

	<img src="../../../DevCenter/PHP/Media/phpstep4Step1.png" />

2.	Go to the scripts folder and open the Microsoft.Samples.Waad.Federation.PS link to generate the trusted issuers’ nodes to add to the XML repository. It will ask you for the AppPrincipalId and the AppDomain name to generate the issuer node as depicted below:

	<img src="../../../DevCenter/PHP/Media/phpstep4Step2.png" />

	*Note: Behind the scenes the script will retrieve the federation metadata to get the issuer identifier for generating the realm’s SPN value.*

3.	Open the XML file, create an issuers root node and include the output node:

	<img src="../../../DevCenter/PHP/Media/phpstep4Step3.png" />

4.	Repeat Step 2 to generate Trey Research Inc. node. Notice that you can change the display name to show a user-friendly name.

	<img src="../../../DevCenter/PHP/Media/phpstep4Step4.png" />

5.	Create a new login.php file and include the following code: 

	<img src="../../../DevCenter/PHP/Media/phpstep4Step5.png" />

6.	Open the secureResource.php file and replace the required library “federation/FederatedLoginManager.php” with “waad-federation/ConfigurableFederatedLoginManager.php”

	<img src="../../../DevCenter/PHP/Media/phpstep4Step6.png" />

7.	Replace the class name FederatedLoginManager with ConfigurableFederatedLoginManager when instantiating the object.

	<img src="../../../DevCenter/PHP/Media/phpstep4Step7.png" />

8.	Also replace the Location header with the following:

	<img src="../../../DevCenter/PHP/Media/phpstep4Step8.png" />

9.	Run the project and you will be able to see a list with the links for each trusted identity provider retrieved from the “trusted.issuers.xml” repository.

	<img src="../../../DevCenter/PHP/Media/phpstep4Step9.png" />

	*Note: The home realm discovery strategy of presenting an explicit list of trusted providers is not always feasible in practice. Here it is used for the sake of simplicity.*

	Once you see the list of the trusted identity providers in your browser, you can navigate to either provider: the authentication flow will unfold in the same way described in the former section. The application will validate the incoming token accordingly. You can try to delete entries in trusted.issuers.xml, as it would happen, for example, once a subscription expires, and verify that the application then will reject authentication attempts from the corresponding provider. 