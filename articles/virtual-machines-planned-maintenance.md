<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section: this is required in all topics. Please fill it out!-->
<properties title="required" pageTitle="required" description="required" metaKeywords="" services="" solutions="" documentationCenter="" authors="" videoId="" scriptId="" />

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Planned Maintenance 

<p> Microsoft Azure periodically performs updates across the globe in order to improve the reliability, performance, and security of the host infrastructure that underlies Virtual Machines. Many of these updates are performed without any impact to Virtual Machines or Cloud Services. However, some of these updates do require a reboot to your Virtual Machine to apply the required updates to the infrastructure. The Virtual Machine will be shut down while we patch the infrastructure and then the Virtual Machines will be restarted. Please note that there are two kinds of maintenance can impact the availability of your virtual machine: Planned and Unplanned. This page will describe how Microsoft Azure performs Planned Maintenance. For more on Unplanned Maintenance, see “<a href="http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-manage-availability/#Understand-planned-versus-unplanned-maintenance">Understand planned versus unplanned maintenance</a>”. 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

+ [Virtual Machine Configurations]  
+ [Multi-Instance Update]
+ [Single-Instance Update]
+ [Email Notification]



## Virtual Machine Configurations

There are two kinds of Virtual Machine configurations: multi-instance and single-instance.  Multi-instance Virtual Machines are configured by placing identical Virtual Machines into an Availability Set. The Multi-Instance configuration provides redundancy and is recommended to ensure the availability of your application. All virtual machines in the Availability Set should be nearly identical and serve the same purpose to your application. For more information on configuring your virtual machines for high availability, refer to “Manage the Availability of your Virtual Machines”. 

In contrast, single-instance Virtual Machines are standalone virtual machines that are not placed into an Availability Set. By themselves, single-instance Virtual Machines do not qualify for the Service Level Agreement (SLA) which requires two or more virtual machines deployed under the same Availability Set. For more information on SLA, refer to the Cloud Services, Virtual Machines and Virtual Network section of “Service Level Agreements”.


## Multi-Instance Update

During planned maintenance, the Azure platform will first update the set of host machines that are hosting the set of Virtual Machines in a Multi-Instance configuration, causing a reboot to these Virtual Machines. For Virtual Machines in a Multi-Instance configuration, Virtual Machines are updated in way that preserves availability throughout the process, assuming each machine serves a similar function as the others in the set. Each virtual machine in your Availability Set is assigned an Update Domain (UD) and a Fault Domain (FD) by the underlying Azure platform. Each UD is a group of Virtual Machines that will be rebooted at the same time. Each FD is a group of Virtual Machines that share a common power source and network switch. 

For more information on UDs and FDs, see “Configure multiple virtual machines in an Availability Set for redundancy”.

Microsoft Azure guarantees that no planned maintenance event will cause Virtual Machines from two different UDs to go offline at the same time. The maintenance is performed by taking down all Virtual Machines in a single UD, applying the update to the host machines, restarting the Virtual Machines, and moving on to the next UD. The planned maintenance event ends once all UDs have been updated. The order of UDs being rebooted may not proceed sequentially during planned maintenance but only one UD will be rebooted at a time. Today, no advance notification of planned maintenance is provided for virtual machines in the multi-instance configuration.

## Single-Instance Update
 
Once these multi-instance updates are completed, we will then perform the update on the set of host machines that are hosting Single-Instance virtual machines. This update will also cause a reboot to your Virtual Machines that are not running in Availability Sets. Please note, even if you only have one instance running in an availability set, the Azure platform will still treat them as multi-instance. For Virtual Machines in a Single-Instance configuration, Virtual Machines are updated by shutting down the Virtual Machine, applying the update to the host machine, and restarting the Virtual Machine. These updates are executed across all Virtual Machines in a region in a single maintenance window. This planned maintenance event will impact the availability of your application for this type of Virtual Machine configuration. 
 
## Email Notification

For single-instance configuration virtual machines only, we send email communication at least one week in advance in order to alert you of the upcoming planned maintenance. This email will be sent to the primary email account provided by the Subscription. An example of this type of email is shown below:
[][5]

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

<!--Image references-->
[5]: ./media/0-markdown-template-for-new-articles/octocats.png
[6]: ./media/0-markdown-template-for-new-articles/pretty49.png
[7]: ./media/0-markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial/
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name/
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account/
