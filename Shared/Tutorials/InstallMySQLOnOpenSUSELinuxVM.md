# Install MySQL on a virtual machine running OpenSUSE Linux in Windows Azure

[MySQL][MySQL] is a popular open source, SQL database.  Using the [Windows Azure (Preview) Management Portal][AzurePreviewPortal], you can create a virtual machine running OpenSUSE Linux from the Image Gallery.  You can then install and configure a MySQL database on the virtual machine.

In this tutorial, you will learn:

- How to use the Preview Management Portal to create an OpenSUSE Linux virtual machine from the gallery.
- How to connect to the virtual machine using SSH or PuTTY.
- How to install MySQL on the virtual machine.

## Sign up for the Virtual Machines preview feature

You will need to sign up for the Windows Azure Virtual Machines preview feature in order to create a virtual machine. You can also sign up for a free trial account if you do not have a Windows Azure account.

<div chunk="../../DevCenter/Shared/Chunks/antares-iaas-signup-iaas.md"/>

## Create a virtual machine running OpenSUSE Linux

<div chunk="../Chunks/create-and-configure-opensuse-vm-in-portal.md" />

##Install and run MySQL on the virtual machine

<div chunk="../Chunks/install-and-run-mysql-on-opensuse-vm.md" />

##Summary
In this tutorial you learned how to create a virtual machine running OpenSUSE Linux and remotely connect to it using SSH or PuTTY.  You also learned how to install and configure MySQL on the Linux virtual machine.  For more information on MySQL, see the [MySQL Documentation][MySQLDocs].



[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePreviewPortal]: http://manage.windowsazure.com
