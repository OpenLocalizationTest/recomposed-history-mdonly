# How to Import a Publish Profile from Windows Azure

This tutorial walks you through how to import a publish profile in WebMatrix. [Click here](http://www.windowsazure.com/en-us/develop/net/tutorials/web-site-with-sql-database/) for importing a publish profile in Visual Web Developer.

1. In the Windows Azure portal, navigate to the dashboard of your website. Under the **quick glance** section click the **Download publish profile** link and save the file.

	![quick glance][1]

2. In WebMatrix, open the site you want to publish.

	![open site][2]

3. Click the Publish button.

	![open site][3]

4. If this is the first time you have published, you will be prompted to enter remote settings. Click the **Import publish settings** link on the right-hand side of the dialog and browse to the location where you saved the file.

	![open site][4]

5. Click **Yes** to run the compatibility check. You can skip this check but it is not recommended.

	![open site][5]

6. Review the compatibility results. If any of the items have a warning icon and say “Not Available”, you can click on the hyperlink to learn more. 

	![open site][6]

7. The publish preview shows you what files have changed and are being published. You also have the option to publish the database along with content. Click **Continue** to begin publishing.

	![open site][7]

8. Publishing will switch to the notification bar and when it’s done you will see a link to browse to the remote site.

	![open site][8]

[1]: media/webmatrix-pubgprofile-install-01.png
[2]: media/webmatrix-pubgprofile-install-02.png
[3]: media/webmatrix-pubgprofile-install-03.png
[4]: media/webmatrix-pubgprofile-install-04.png
[5]: media/webmatrix-pubgprofile-install-05.png
[6]: media/webmatrix-pubgprofile-install-06.png
[7]: media/webmatrix-pubgprofile-install-07.png
[8]: media/webmatrix-pubgprofile-install-08.png


