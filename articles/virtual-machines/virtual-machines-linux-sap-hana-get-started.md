---
title: Quickstart guide for a manual installation of SAP HANA on Azure VMs | Microsoft Docs
description: Quickstart guide for manual installation of SAP HANA on Azure VMs
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''

ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd

---
# Quickstart guide for a manual installation of single-instance SAP HANA on Azure VMs
## Introduction
This Quickstart guide helps you set up a single-instance SAP HANA prototype or demo system on Azure Virtual Machines (VMs) when you perform a manual installation of SAP NetWeaver 7.5 and SAP HANA SP12.
The guide assumes that you are familiar with such Azure IaaS basics as how to deploy virtual machines or virtual networks through either the Azure portal or Powershell/CLI, including the option to use JavaScript Object Notification (JSON) templates. The guide also assumes that you are familiar with SAP HANA and SAP NetWeaver, and how to install them on-premises.

Additionally, you should be aware of the SAP Azure documentation that's mentioned in the general information section at the end of the guide.

Because the guide's content is restricted to non-production systems, it does not cover topics such as high availability (HA), backup, disaster recovery (DR), high performance, or special security considerations.

We performed the sample setup by using two virtual machines to accomplish a distributed SAP NetWeaver installation through the Azure Resource Manager model, because SAP-Linux-Azure is supported only on Resource Manager and not the classic model. For more information about Resource Manager, see the general information section at the end of the guide.

For the sample installation, we used the following two test VMs:

* hana-appsrv (type DS3) to host the NW 7.5 ASCS instance + PAS
* hana-dbsrv (type GS4) to host HANA SP12

Both VMs belonged to one Azure virtual network (azure-hana-test-vnet), and the OS in both cases was SLES 12 SP1.

Note that, as of July 2016, SAP HANA is fully supported only for OLAP (BW) production systems on Azure VM type GS5. For testing purposes, if you are not expecting official SAP support, it's fine to use something smaller, such as GS4. For SAP HANA on Azure, always use Azure Premium Storage for HANA data and log files (see the "Disk setup" section later in the guide). For more information about which SAP products are supported on Azure, see the "General information" section at the end of the guide.

The guide describes how to manually install SAP HANA on Azure VMs in two different ways:

* By using SAP Software Provisioning Manager (SWPM) as part of a distributed NetWeaver installation in the "install database instance" step.
* By using the HANA lifecycle management tool hdblcm and then installing NetWeaver afterward.

You can also use SWPM and install all components (SAP HANA, SAP application server, ASCS instance, SAP GUI) in one single VM. This option isn't described in the guide, but the items that must be considered are the same.

Before starting an installation, be sure to read "Prepare Azure VMs for manual installation of SAP HANA," which comes after the following two checklists. Doing so can help prevent several basic mistakes that might occur when you use only a default Azure VM configuration.

## Checklist for SAP HANA installation using SAP SWPM
This is a simple checklist of the key items related to a manual, single-instance SAP HANA installation for demo or prototyping purposes when you use SAP SWPM to perform a distributed SAP NW 7.5 install. The individual items are explained in more detail in  screenshots that are shown throughout the guide.

* Create an Azure virtual network that includes the two test VMs.
* Deploy two Azure VMs with OS SLES 12 SP1 according to the Azure Resource Manager model.
* Attach two standard storage disks to the app server VM (for example, 75-GB or 500-GB disks).
* Attach four disks to the HANA DB server VM: Two standard storage disks (like those for the app server VM) plus two premium storage disks (for example, two 512-GB disks).
* Depending on size or throughput requirements, attach multiple disks and create striped volumes by using either logical volume management (LVM) or a multiple devices administration utility (mdadm) at the OS level inside the VM.
* Create XFS file systems on the attached disks/logical volumes.
* Mount the new XFS file systems at the OS level. Use one file system to keep all the SAP software, and use the other one for, for example, the /sapmnt directory and maybe backups. On the SAP HANA DB server, mount the XFS file systems on the premium storage disks as /hana and /usr/sap. This process is necessary to prevent the root file system, which isn't very large on Linux Azure VMs, from filling up.
* Enter the local IP addresses of the test VMs in /etc/hosts.
* Enter the nofail parameter in /etc/fstab.
* Set kernel parameters according to the HANA-SLES-12 SAP note. For more information, see the "Kernel parameters" section.
* Add swap space.
* If you want, install a graphical desktop on the test VMs. Otherwise, use a remote SAPinst install.
* Download the SAP software from the SAP service marketplace.
* Install the SAP ASCS instance on the app server VM.
* Share the /sapmnt directory among the test VMs by using NFS. The app server VM is the NFS server.
* Install the database instance, including HANA, by using SWPM on the DB server VM.
* Install the primary application server (PAS) on the app server VM.
* Start SAP management console (MC) and connect with, for example, SAP GUI/HANA Studio.

## Checklist for SAP HANA installation using hdblcm
This is a simple checklist of the key items related to a manual, single-instance SAP HANA installation for demo or prototyping purposes when you use SAP hdblcm to perform a distributed SAP NW 7.5 install. The individual items are explained in more detail in  screenshots that are shown throughout the guide.

* Create an Azure virtual network that includes the two test VMs.
* Deploy two Azure VMs with OS SLES 12 SP1 according to the Azure Resource Manager model.
* Attach two standard storage disks to the app server VM (for example, 75-GB or 500-GB disks).
* Attach four disks to the HANA DB server VM: Two standard storage disks (like those for the app server VM) plus two premium storage disks (for example, two 512-GB disks).
* Depending on size or throughput requirements, attach multiple disks and create striped volumes by using either logical volume management (LVM) or a multiple devices administration utility (mdadm) at the OS level inside the VM.
* Create XFS file systems on the attached disks/logical volumes.
* Mount the new XFS file systems at the OS level. Use one file system to keep all the SAP software, and use the other one for, for example, the /sapmnt directory and maybe backups. On the SAP HANA DB server, mount the XFS file systems on the premium storage disks as /hana and /usr/sap. This process is necessary to help prevent the root file system, which isn't very large on Linux Azure VMs, from filling up.
* Enter the local IP addresses of the test VMs in /etc/hosts.
* Enter the nofail parameter in /etc/fstab.
* Set kernel parameters according to the HANA-SLES-12 SAP note. For more information, see the "Kernel parameters" section.
* Add swap space.
* If you want, install a graphical desktop on the test VMs. Otherwise, use a remote SAPinst install.
* Download the SAP software from the SAP service marketplace.
* Create a group, "sapsys," with group ID 1001 on the HANA DB server VM.
* Install SAP HANA on the DB server VM by using HANA database lifecycle manager (HDBLCM).
* Install the SAP ASCS instance on the app server VM.
* Share the /sapmnt directory among the test VMs by using NFS. The app server VM is the NFS server.
* Install the database instance, including HANA, by using SWPM on the HANA DB server VM.
* Install the primary application server (PAS) on the app server VM.
* Start SAP MC and connect through SAP GUI/HANA Studio.

## Prepare Azure VMs for a manual installation of SAP HANA
This section covers the following five topics:

* Disk setup
* Kernel parameters
* File systems
* /etc/hosts
* /etc/fstab

### Disk setup
The root file system in a Linux VM on Azure is of limited size. Therefore, it's necessary to attach additional disk space to a VM for running SAP. In the case of a SAP app server VM that's used in a pure prototype or demo environment, it's fine to use Azure standard storage disks. For SAP HANA DB data and log files, use Azure Premium Storage disks even in a non-production landscape.

For more information, see [how to attach disks to a Linux VM](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

For Azure disk caching, enter **None** for disks that are to be used to store the
HANA transaction logs. For HANA data files, it's OK to use read caching. Because HANA is an in-memory database, how much the read cache on Azure disk level will improve performance (for example, starting HANA and reading data from the disk into memory) depends on the overall usage pattern.

For more information, see [Premium Storage: High-performance storage for Azure Virtual Machine workloads](../storage/storage-premium-storage.md)

To find sample JSON templates for creating VMs, go to [Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates).
The "101-vm-simple-linux" shows a basic template, which includes a storage section that adds a 100-GB data disk.

For information about how to find a SUSE image by using Powershell or CLI, and to understand the importance of attaching a disk by using UUID, see [Running SAP NetWeaver on Microsoft Azure SUSE Linux VMs](virtual-machines-linux-sap-on-suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Depending on the size and throughput requirements of the system, it might be necessary to attach multiple disks instead of one and, later, to create a stripe set across those disks at the OS level. The reason for creating a stripe set across multiple Azure disks has two aspects:

* You can increase throughput.
* You need a single file system that's greater than 1 TB, because the current Azure disk-size limit is 1 TB (as of July 2016).

For more information about the two main tools for configuring striping, see the following articles:

* [Configure software RAID on Linux ](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configure LVM on a Linux VM in Azure](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

In the test environment, two Azure standard storage disks were attached to the SAP app server VM, as shown in the following screenshot. One was used to store all the SAP software for installation (that is, NetWeaver 7.5, SAP GUI, SAP HANA, and so forth). The other disk was used to ensure that enough space is available for additional requirements (for example, backup, test data), as well as the /sapmnt directory (that is, SAP profiles) to be shared among all VMs that belong to the same SAP landscape.

![SAP HANA app server Disks window displaying two data disks and their sizes](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

Contrary to the app server VM, four disks were attached to the SAP HANA server VM, as shown in the following screenshot. As before, two disks were used to store the SAP software (you can also share the SAP software disk by using NFS) and to make enough space available for, for example, backup. The additional two disks were Azure Premium Storage disks to store SAP HANA data and log files as well as the /usr/sap directory.

![SAP HANA app server Disks window displaying four data disks and their sizes](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

### Kernel parameters
SAP HANA requires specific Linux kernel settings, which are not part of the standard Azure gallery images and must be set manually. A SAP HANA note describes the recommended OS settings for SLES 12/SLES for SAP Applications 12: [SAP Note 2205917](https://launchpad.support.sap.com/#/notes/2205917).

You'll find an additional page-cache topic that's related to running SAP HANA on SLES in chapter "6.1 Kernel: Page-Cache Limit" of the [SUSE Linux Enterprise Server for SAP Applications 12 SP1 Guide](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache).

Another SAP note describes the page-cache limit: [SAP Note 1557506](https://launchpad.support.sap.com/#/notes/1557506).

SLES 12 has a new tool that replaces the old sapconf utility. It's called tuned-adm, and a special SAP HANA profile is available for it. For more information about tuned-adm, see the following articles:

* [SLES documentation about tuned-adm profile sap-hana.](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)
* [SLES documentation about tuned-adm profile sap-hana: Chapter 6.2, "Tuning Systems for SAP Workloads with tuned-adm."](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)

In the following screenshot, you can see how tuned-adm changed the transparent_hugepage and numa_balancing values, according to the required SAP HANA settings.

![The tuned-adm tool changes values in line with required SAP HANA settings](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

To make the SAP HANA kernel settings permanent one has to use grub2 on SLES 12. For more information about grub2, go to the ["Configuration File Structure" section of SUSE documentation](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html).

The following screenshot shows how the kernel settings were changed in the config file and then "compiled" by using grub2-mkconfig.

![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Another option might be to change the settings by using YaST and the Boot Loader Kernel Parameters settings.

![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

### File systems
The following screenshot shows two file systems that were created on the SAP app server VM on top of the two attached Azure standard storage disks. Both file systems are of type XFS and mounted to /sapdata and /sapsoftware.

It is not mandatory to do it this way. You have other options for structuring the disk space. The most important consideration is to prevent the root file system from running out of space.

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Regarding the SAP HANA DB VM, it's important to know that during a database installation, when you use sapinst (swpm) and the simple "typical" installation option, it will install everything by default under /hana and /usr/sap. The default setting for SAP HANA log backup is under /usr/sap. Again, because it's important to prevent the root file system from running out of space, make sure that there is enough free space under /hana and /usr/sap before you install SAP HANA by using swpm.

For a description of the standard file-system layout of SAP HANA, see the [SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).
![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

When you install SAP NetWeaver on a standard SLES 12 Azure gallery image, a message will be displayed that says that there is no swap space. To dismiss this message, you can manually add a swap file by using dd, mkswap, and swapon. To learn how, search for "Adding a Swap File Manually"
in the ["Using the YaST Partitioner" section of SUSE documentation](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html).

Another option is to configure swap space by using the Linux VM agent. More information can be found [here](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)


### /etc/hosts
Another important thing to keep in mind before starting to install SAP is to include host names and IP addresses of the SAP VMs in the /etc/hosts file. Deploy all the SAP VMs within one Azure virtual network and then use the internal IP addresses.

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

### /etc/fstab

During the testing phase, it's a good idea to add the nofail parameter to fstab. If something goes wrong with the disks, the VM will still come up and not hang in the boot process. But be sure to watch out, because, as in this case, the additional disk space might not be available and processes might fill up the root file system. If /hana is missing, SAP HANA won't start.

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

## Install graphical Gnome desktop on SLES 12
This chapter covers the following topics:

* Installing Gnome desktop and xrdp on SLES 12
* Running Java-based SAP MC by using Firefox on SLES 12

You can also use alternatives such as Xterminal or VNC but, as of September 2016, the guide describes only xrdp.

### Installing Gnome desktop and xrdp on SLES 12
If you have a Microsoft Windows background and want to use a graphical desktop directly within the SAP Linux VMs to run Firefox, Sapinst, SAP GUI, SAP MC, or HANA Studio and maybe connect to the VM via RDP from a Microsoft Windows computer, there is a simple way to achieve this. While this might not be appropriate e.g. for a production database server it's ok for a pure prototype/demo environment. Here are the steps to install the Gnome desktop on an Azure SLES 12 VM:

Install the gnome desktop by entering the following command (e.g. in a putty window):

`zypper in -t pattern gnome-basic`

Install xrdp to allow connection to the VM via RDP:

`zypper in xrdp`

Edit /etc/sysconfig/windowmanager and set the default windows manager to Gnome:

`DEFAULT_WM="gnome"`

Run chkconfig to make sure that xrdp starts automatically after a reboot:

`chkconfig -level 3 xrdp on`

If you have an issue with the RDP connection, try to restart (maybe out of a putty window):

`/etc/xrdp/xrdp.sh restart`

If xrdp restart as mentioned above doesn't work, check whether there is a .pid file and remove it:

`check /var/run` and look for `xrdp.pid`   

Remove it and then try the restart again.

### SAP MC
After you install the Gnome desktop (as described in the previous section), if you start the graphical Java-based SAP MC out of Firefox running in an Azure SLES 12 VM, you will get an error because of the missing Java-browser plug-in.

The URL to start the SAP MC is <server>:5<instance_number>13

For more information, see [Starting the Web-Based SAP Management Console ](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

The following screenshot shows the error message that's displayed when the Java-browser plug-in is missing.

![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

One way to solve the problem is simply to install the missing plug-in by using YaST, as shown in the following screenshot.

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Repeating the SAP Management Console URL the first time will display a dialog box that asks you to activate the plug-in.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

One additional issue that might occur is an error message about a missing file, javafx.properties. The error is very likely related to the installation of Java 1.8, which is required for SAP GUI 7.4. The IBM Java version that's seen in YaST doesn't include this file. The solution is a Java download from Oracle. For more information about this issue, see [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## Install SAP HANA manually by using SWPM as part of a NetWeaver 7.5 installation
The series of screenshots in this section shows the key steps for installing SAP NetWeaver 7.5 and SAP HANA SP12 when you use SWPM (SAPinst). As part of a NetWeaver 7.5 installation, SWPM can also install the HANA database as a single instance.

For the sample test environment, just one ABAP app server was installed. As shown in the following screenshot, the option "Distributed System"
was used to install the ASCS and primary application server instances in one Azure VM and SAP HANA as the database system in an another Azure VM.

![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

After the ASCS instance is installed on the app server VM and is set to "green" in the SAP MC, the /sapmnt directory, which includes the SAP profile directory, has to be shared with the SAP HANA DB server VM. The DB installation step needs access to this information. The best way to provide access is to use NFS, which can be configured by using YaST.

![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

On the app server VM, the /sapmnt directory should be shared through NFS by using the options **rw** and **no_root_squash**. The defaults are "ro" and "root_squash", which could lead to problems when you install the database instance.

![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

As shown below, on the SAP HANA DB server VM, the /sapmnt share from the app server VM has to be configured by using **NFS client** (with the help of YaST).

![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

To accomplish a distributed NW 7.5 installation, **Database Instance**, as shown in the following screenshot, log in to the SAP HANA DB server VM and start SWPM.

![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

After selecting a "typical" installation and the path to the installation media, enter a DB SID, the host name, the instance number, and the DB System Administrator password.

![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Enter the password for the DBACOCKPIT schema.

![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Enter a question for the SAPABAP1 schema password.

![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

After the task has completed, there should be a green checkmark next to each phase of the DB installation process. A Message Box window should display a message that says, "Execution of ... Database Instance has completed."

![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

After the successful installation, the SAP Management Console should also show the DB instance as "green" and display the full list of SAP HANA processes (hdbindexserver, hdbcompileserver, and so forth).

![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

The following screenshot shows the parts of the file structure under /hana/shared that SWPM created during the HANA installation. Because there was no option to specify a different path, it's mportant to mount additional disk space under /hana before the SAP HANA installation by using SWPM to prevent the root file system from running out of free space.

![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

This screenshot shows the file structure of the /usr/sap directory.

![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

The final step of the distributed ABAP installation is the "Primary Application Server Instance."

![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

After the Primary Application Server and SAP GUI are installed, check by using the transaction "dbacockpit" that everything looks right with the SAP HANA installation.

![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

As a last step, you might want to install SAP HANA Studio in the SAP app server VM and connect to the SAP HANA instance running on the DB server VM.

![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

## Install SAP HANA manually by using the HANA lifecycle management tool (hdblcm)
Besides installing SAP HANA as part of a distributed installation by using SWPM, you can also install HANA standalone first by using hdblcm and then install, for example, SAP NetWeaver 7.5 afterward. The following screenshots show how this process works.

Here are three sources of information about the HANA hdblcm tool:

* [Choosing the Correct SAP HANA HDBLCM for Your Task](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA Server Installation and Update Guide](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

To avoid running into problems later with a default group ID setting for the \<HANA SID\>adm user (created by the hdblcm tool), define a new group called "sapsys" with group ID 1001 before installing SAP HANA by using hdblcm.

![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

When you start hdblcm the first time, a simple start menu is displayed. You must select item 1, "Install new System," as shown in the following screenshot.

![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

The following screenshot displays all the key options that you selected previously.

[!IMPORTANT]
Directories that were named for HANA log and data volumes, as well as the installation path (/hana/shared in this sample) and /usr/sap, should not be part of the root file system. The directories belong to the Azure data disks that were attached to the VM, as described in the Azure VM setup section. This approach will help prevent the risk the root file system running out of space. You can also see that the HANA admin user has user ID 1005 and is part of the sapsys group (ID 1001) that was defined before the installation.

![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

You can check the HANA \<HANA SID\>adm (azdadm in the following screenshot) user details in /etc/passwd.

![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

After you install SAP HANA by using hdblcm, you can see the file structure in SAP HANA Studio. The SAPABAP1 schema, which includes all the SAP NetWeaver tables, isn't available yet.

![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

After you install SAP HANA, you can install SAP NetWeaver on top of it. As shown in this screenshot, the installation was performed as a "distributed installation" by using SWPM as described in the previous section. When you install the database instance by using SWPM, you enter the same data as before by using hdblcm (for example, hostname, HANA SID, instance number). SWPM then uses the existing HANA installation and adds more schemas.

![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

The following screenshot shows the SWPM installation step where you enter data about the DBACOCKPIT schema.

![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Enter data about the SAPABAP1 schema.

![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

After the SWPM database instance installation is finished, you can see the SAPABAP1 schema in HANA Studio.

![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Finally, after the installation of the SAP app server and SAP GUI, you can verify the HANA DB instance with transaction "dbacockpit."

![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

## About SAP Azure certifications, running SAP HANA on Azure, and SAP software download
For more information, refer to the following documentation:
* General SAP Azure information about running SAP on Azure with Windows OS in classic mode: [Using SAP on Windows virtual machines in Azure](virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Information about existing SAP templates for customer use: [Azure Quickstart Templates for SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).
* General SAP Azure information about running SAP on Azure with Linux OS in Azure Resource Manager model: [Using SAP on Linux virtual machines (VMs)](virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Certified SAP HANA hardware directory, which lists which Azure VM types are supported for production: [Certified SAP HANA® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
* Information about virtual machine sizes especially for Linux workloads: [Sizes for virtual machines in Azure](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* SAP Note that lists all supported SAP products on Azure and supported Azure VM types for SAP: [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533/E).
* SAP Note about SAP "enhanced monitoring" with Linux VMs on Azure: [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E).
* SAP HANA offering on Azure "Large Instances". It's important to understand that this information is not about running SAP HANA on Azure VMs. It's about a hybrid environment where the SAP app servers run in Azure VMs but SAP HANA runs on bare-metal servers: [SAP Note 2316233](https://launchpad.support.sap.com/#/notes/2316233/E).
* SAP Note with information about SAPOSCOL on Linux: [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E).
* Key monitoring metrics for SAP on Microsoft Azure: [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E).
* Information about Azure Resource Manager: [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md).
* Information about deploying Linux VMs by using templates: [Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Comparison of Azure Resource Manager and classic deployment models: [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../azure-resource-manager/resource-manager-deployment-model.md).
* Download NetWeaver 7.5 for Linux/HANA from the SAP Service Marketplace:
  ![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)
* Download HANA SP12 Platform Edition from the SAP Service Marketplace:
  ![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)
