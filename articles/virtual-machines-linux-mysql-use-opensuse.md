<properties urlDisplayName="Install MySQL" pageTitle="Install MySQL on a virtual machine running OpenSUSE Linux in Azure" metaKeywords="Azure, MySQL" description="Learn to install MySQL on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="kathydav" />

# Install MySQL on a virtual machine running OpenSUSE Linux in Azure

[MySQL][MySQL] is a popular, open-source SQL database.  Using the [Azure Management Portal][AzurePortal], you can create a virtual machine running OpenSUSE Linux.  You can then install and configure a MySQL database on the virtual machine.

This tutorial shows you:

- How to use the Management Portal to create an OpenSUSE Linux virtual machine from an image available through Azure.
- How to connect to the virtual machine using SSH or PuTTY.
- How to install MySQL on the virtual machine.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Create a virtual machine running OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Install and run MySQL on the virtual machine

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Summary
In this tutorial you learned to create a virtual machine running OpenSUSE Linux and remotely connect to it using SSH or PuTTY.  You also learned how to install and configure MySQL on the Linux virtual machine.  For more information on MySQL, see the [MySQL Documentation][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com
