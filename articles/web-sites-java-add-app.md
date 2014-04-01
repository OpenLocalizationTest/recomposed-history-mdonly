<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java web site" pageTitle="Add an application to your Java web site" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java web site on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java web site" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

# Adding an application to your Java web site on Azure

Once you have initialized your Java web site as documented at [Get started with Microsoft Azure Web Sites and Java](../web-sites-java-get-started), or by using the custom upload as documented at [Upload a custom Java web site to Azure](../web-sites-java-custom-upload), you can upload your application by placing your WAR in the **webapps** folder.

The navigation path to the **webapps** folder differs based on how you set up your web site.

- If you set up your web site by using the Azure application gallery, the path to the **webapps** folder is in the form **d:\home\site\wwwroot\bin\application\_server\webapps**, where **application\_server** is the name of the application server in effect for your web site. 
- If you set up your web site by using the Azure configuration UI, the path to the **webapps** folder is in the form **d:\home\site\wwwroot\webapps**. 
- If you set up your web site by using a custom upload, navigate as needed to the **webapps** folder. 

Note that you can use source control to upload your application or web pages, including in continuous integration scenarios. Instructions for using source control with your web site are available at [Publishing from Source Control to Azure Web Sites](../web-sites-publish-source-control). FTP is also an option for uploading your application or web pages.

Once you've uploaded your WAR file to the **webapps** folder, the application server will detect that you've added it and will automatically load it. 

Note that if you copy files (other than WAR files) to the ROOT directory, the application server will need to be restarted before those files are used. The autoload functionality for the Java web sites running on Azure is based on a new WAR file being added, or new files or directories added to the **webapps** folder. 
