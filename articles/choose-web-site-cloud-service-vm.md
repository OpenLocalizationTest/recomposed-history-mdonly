<properties linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Windows Azure" pageTitle="When to Choose Windows Azure Web Sites, Cloud Services, or Virtual Machines" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Windows Azure Web Sites, Cloud Services, and Virtual Machines for hosting web applications" metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />





# Windows Azure Web Sites, Cloud Services and Virtual Machines comparison

Windows Azure offers several ways to host your web applications, such as [Windows Azure Web Sites][], [Cloud Services][], and [Virtual Machines][]. After looking at these various options, you might be unsure which one best fits your needs, or you might be unclear about concepts such as IaaS vs PaaS. This article helps you understand your options and helps you make the right choice for your web scenario. Although all three options allow you to run highly scalable web applications in Windows Azure, there are differences which can help guide your decision.

In many situations, Windows Azure Web Sites is the best option. It provides simple and flexible options for deployment and management, and it is capable of hosting high-volume web sites. You can quickly create a new web site with popular software, such as WordPress, from the Web Application Gallery, or you can move an existing web site to Windows Azure Web Sites. Using the [Windows Azure WebJobs SDK][] (currently in preview) you can also add background job processing. 

You also have the option to host web applications on Windows Azure Cloud Services or Windows Azure Virtual Machines. These options are good choices when your web tier requires the additional level of control and customization that they provide; however, this increased control comes at a cost of increased complexity in application creation, management, and deployment. The following diagram illustrates the trade-offs among the three options.

![ChoicesDiagram][ChoicesDiagram]

Web Sites are easier to set up, manage, and monitor, but you have fewer configuration options. The key point is that when you do not have a compelling reason to place your web front-end on Cloud Services or Virtual Machines, use Windows Azure Web Sites. The remainder of this document provides the information needed to make an informed decision. This includes:

- [Scenarios](#scenarios)
- [Service Summaries](#services)
- [Feature Comparison](#features)

##<a name="scenarios"></a>Scenarios

### I'm a small business owner, and I need an inexpensive way to host my site but with future growth in mind.

Windows Azure Web Sites (WAWS) is a great solution for this scenario, because you can start using it for free and then add more capabilities when you need them. For example, each free web site comes with a domain provided by Windows Azure (*your_company*.azurewebsites.net). When you’re ready to start using your own domain, you can add this for as low as $9.80 a month (as of 1/2014). There are many other services and scaling options that allow the site to evolve with increased user demand. With **Windows Azure Web Sites**, you can:

- Begin with the free tier and then scale up as needed.
- Use the Application Gallery to quickly setup popular web applications, such as WordPress.
- Add additional Windows Azure services and features to your application as needed.
- Secure your web site with HTTPS using the certificate provided with your *your_company*.azurewebsites.net domain name.

### I'm a web or graphic designer, and I want to design and build web sites for my customers

For web developers, Windows Azure Web Sites gives you what you need to create sophisticated web applications. Web Sites offers tight integration with tools such as Visual Studio and SQL database. With **Web Sites**, developers can:

- Use command-line tools for [automated tasks][scripting].
- Work with popular languages such as [.Net][dotnet], [PHP][], [Node.js][nodejs], and [Python][].
- Select three different scaling levels for scaling up to very high capacities.
- Integrate with other Azure services, such as [SQL Database][sqldatabase], [Service Bus][servicebus] and [Storage][], or partner offerings from the [Windows Azure Store][azurestore], such as MySQL and MongoDB.
- Integrate with tools, such as Visual Studio, Git, WebMatrix, WebDeploy, TFS, and FTP.

### I'm migrating my multi-tier application with a web front-end to the Cloud

If you’re running a multi-tier application, such as a web server that talks to a database server to store and retrieve website data, you have several options for in Windows Azure. These architectural options include Web Sites, Cloud Services, and Virtual Machines. First, **Web Sites** is a good option for the web tier of your solution and can be used with Windows Azure SQL Database to create a two-tier architecture. Web Sites also allows you to run background or long running processes using the Windows Azure WebJobs SDK preview. If you need more complex architecture or more flexible scaling options, Cloud Services or Virtual Machines are a better choice. 

**Cloud Services** enables you to:

- Host web, middle-tier, and backend services on scalable web and worker roles. 
- Host only the middle-tier and backend services on worker roles, keeping the front-end on Windows Azure Web Sites. 
- Scale frontend and backend services independently.

**Virtual Machines** enables you to: 

- More easily migrate highly customized environments as a virtual machine image.
- Run software or services that cannot be configured on Web Sites or Cloud Services.

### My application depends on highly customized Windows or Linux environments

If your application requires complex installation or configuration of software and the operating system, Virtual Machines is probably the best solution. With **Virtual Machines**, you can:

- Use the Virtual Machine gallery to start with an operating system, such as Windows or Linux, and then customize it for your application requirements. 
- Create and upload a custom image of an existing on-premises server to run on a virtual machine in Windows Azure. 

### My site uses open source software, and I want to host it in Windows Azure

All three options allow you to host open source languages and frameworks. **Cloud Services** requires you to use startup tasks to install and configure any required open source software that runs on Windows. With **Virtual Machines**, you install and configure the software on the machine image, which can be Windows or Linux-based. If your open source framework is support on Web Sites, this provides a simpler way to host these types of applications as Web Sites can be automatically configured with the languages and frameworks needed by your application. **Web Sites** enables you to:

- Use many popular open source languages, such as [.NET][dotnet], [PHP][], [Node.js][nodejs], and [Python][]. 
- Setup WordPress, Drupal, Umbraco, DNN, and many other third-party web applications. 
- Migrate an existing application or create a new one from the Application Gallery. 

### I have a line-of-business application that needs to connect to the corporate network

If you want to create a line-of-business application, your web site might require direct access to services or data on the corporate network. This is possible on **Web Sites**, **Cloud Services**, and **Virtual Machines**. There are differences in the approach you take, which include the following:

- Web Sites can securely connect to on-premises resources through the use of Service Bus Relay. This allows services on the corporate network to perform tasks on behalf of the web application without moving everything to the Cloud or setting up a virtual network. 
- Cloud Services and Virtual Machines can take advantage of Virtual Network. In effect, Virtual Network allows machines running in Windows Azure to connect to an on-premises network. Windows Azure then becomes an extension of your corporate datacenter.

### I want to host a REST API or web service for mobile clients

HTTP-based web services allows you to support a wide variety of clients, including mobile clients. Frameworks like the ASP.NET Web API integrate with Visual Studio to make it easier to create and consume REST services.  These services are exposed from a web endpoint, so it is possible to use any web hosting technique on Windows Azure to support this scenario. However, **Web Sites** is a great choice for hosting REST APIs. With Web Sites, you can:

- Quickly create a Web Site to host the HTTP web service in one of Windows Azure’s globally distributed datacenters.
- Migrate existing services or create new ones, potentially taking advantage of the ASP.NET Web API in Visual Studio.
- Achieve SLA for availability with a single instance, or scale out to multiple dedicated machines. 
- Use the published site to provide REST APIs to any HTTP clients, including mobile clients.

##<a name="services"></a>Service Summaries

[Windows Azure Web Sites][] enables you to build highly scalable web sites quickly on Windows Azure. You can use the Windows Azure Portal or the command-line tools to set up a web site with popular languages such as .NET, PHP, Node.js, and Python. Supported frameworks are already deployed and do not require more installation steps. The Windows Azure Web Sites gallery contains many third-party applications, such as Drupal and WordPress as well as development frameworks such as Django and CakePHP. After creating a site, you can either migrate an existing web site or build a completely new web site. Web Sites eliminates the need to manage the physical hardware, and it also provides several scaling options. You can move from a shared multi-tenant model to a standard mode where dedicated machines service incoming traffic. Web Sites also enable you to integrate with other Windows Azure services, such as SQL Database, Service Bus, and Storage. Using the [Windows Azure WebJobs SDK][] preview, you can add background processing. In summary, Windows Azure Web Sites make it easier to focus on application development by supporting a wide range of languages, open source applications, and deployment methodologies (FTP, Git, Web Deploy, or TFS). If you don’t have specialized requirements that require Cloud Services or Virtual Machines, a Windows Azure Web Site is most likely the best choice.

[Cloud Services][] enable you to create highly-available, scalable web applications in a rich Platform as a Service (PaaS) environment. Unlike Web Sites, a cloud service is created first in a development environment, such as Visual Studio, before being deployed to Windows Azure. Frameworks, such as PHP, require custom deployment steps or tasks that install the framework on role startup. The main advantage of Cloud Services is the ability to support more complex multitier architectures. A single cloud service could consist of a frontend web role and one or more worker roles. Each tier can be scaled independently. There is also an increased level of control over your web application infrastructure. For example, you can remote desktop onto the machines that are running the role instances. You can also script more advanced IIS and machine configuration changes that run at role startup, including tasks that require administrator control.

[Virtual Machines][] enable you to run web applications on virtual machines in Windows Azure. This capability is also known as Infrastructure as a Service (IaaS). Create new Windows Server or Linux machines through the portal, or upload an existing virtual machine image. Virtual Machines give you the most control over the operating system, configuration, and installed software and services. This is a good option for quickly migrating complex on-premises web applications to the cloud, because the machines can be moved as a whole. With Virtual Networks, you can also connect these virtual machines to on-premises corporate networks. As with Cloud Services, you have remote access to these machines and the ability to perform configuration changes at the administrative level. However, unlike Web Sites and Cloud Services, you must manage your virtual machine images and application architecture completely at the infrastructure level. One basic example is that you have to apply your own patches to the operating system.

##<a name="features"></a>Feature Comparison

The following table compares the capabilities of Web Sites, Cloud Services, and Virtual Machines to help you make the best choice. Boxes with an asterisk are explained more in the notes following the table.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Feature</th>
   <th align="left" valign="middle">Web Sites</th>
   <th align="left" valign="middle">Cloud Services (web roles)</th>
   <th align="left" valign="middle">Virtual Machines</th>
</tr>
<tr>
   <td valign="middle"><p>Access to services like Service Bus, Storage, SQL Database</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Host web or web services tier of a multi-tier architecture</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Host middle tier of a multi-tier architecture</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Integrated MySQL-as-a-service support</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X <sup>1</sup></td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Support for ASP.NET, classic ASP, Node.js, PHP, Python</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Scale out to multiple instances without redeploy</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X <sup>2</sup></td>
</tr>
<tr>
   <td valign="middle"><p>Support for SSL</p></td>
   <td valign="middle">X <sup>3</sup></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Visual Studio integration</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Remote Debugging</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Deploy code with TFS</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Deploy code with GIT, FTP</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Deploy code with Web Deploy</p></td>
   <td valign="middle">X</td>
   <td valign="middle"><sup>4</sup></td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>WebMatrix support</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Near-instant deployment</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Instances share content and configuration</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Scale up to larger machines without redeploy</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Multiple deployment environments (production and staging)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Network isolation with Windows Azure Virtual Network</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Support for Windows Azure Traffic Manager</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Remote desktop access to servers</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Ability to define/execute start-up tasks</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Automatic OS update management</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Integrated Endpoint Monitoring</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
</tr>
<tr>
   <td valign="middle"><p>Seamless platform switching (32bit/64bit)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
</table>

<sup>1</sup> Web or worker roles can integrate MySQL-as-a-service through ClearDB's offerings, but not as part of the Management Portal workflow.

<sup>2</sup> Although Virtual Machines can scale out to multiple instances, the services running on these machines must be written to handle this scale-out. An additional load balancer must be configured to route requests across the machines. Finally, an Affinity Group should be created for all machines participating in the same role to protect them from simultaneous restarts from maintenance or hardware failures.

<sup>3</sup> For Web Sites, SSL for custom domain names is only supported for standard mode. For more information on using SSL with Web Sites, see [Configuring an SSL certificate for a Windows Azure Web Site][].

<sup>4</sup> Web Deploy is supported for cloud services when deploying to single-instance roles. However, production roles require multiple instances to meet the Windows Azure SLA. Therefore, Web Deploy is not a suitable deployment mechanism for cloud services in production.


  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png
  [Windows Azure Web Sites]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Cloud Services]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Virtual Machines]: http://go.microsoft.com/fwlink/?LinkID=306053
  [ClearDB]: http://www.cleardb.com/
  [Windows Azure WebJobs SDK]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Configuring an SSL certificate for a Windows Azure Web Site]: http://www.windowsazure.com/en-us/develop/net/common-tasks/enable-ssl-web-site/
  [azurestore]: http://www.windowsazure.com/en-us/gallery/store/
  [scripting]: http://www.windowsazure.com/en-us/documentation/scripts/?services=web-sites
  [dotnet]: http://www.windowsazure.com/en-us/develop/net/
  [nodejs]: http://www.windowsazure.com/en-us/develop/nodejs/
  [PHP]: http://www.windowsazure.com/en-us/develop/php/
  [Python]: http://www.windowsazure.com/en-us/develop/python/
  [servicebus]: http://www.windowsazure.com/en-us/documentation/services/service-bus/
  [sqldatabase]: http://www.windowsazure.com/en-us/documentation/services/sql-database/
  [Storage]: http://www.windowsazure.com/en-us/documentation/services/storage/
