<properties title="Custom domain name" pageTitle="Configuring a custom domain name for a Microsoft Azure web site" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="Web Sites" documentationCenter="" authors="" />

#Configuring a custom domain name for an Azure web site (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name/" title="Web Sites" class="current">Web Site</a> | <a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name-traffic-manager/" title="Web Site using Traffic Manager">Web Site using Traffic Manager</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

This article provides instructions on using a custom domain name purchased from [eNom](https://enom.com) with Azure Web Sites.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

In this article:

-   [Understanding DNS records](#understanding-records)
-   [Configure your web sites for basic, shared or standard mode](#bkmk_configsharedmode)
-   [Add a DNS record for your custom domain](#bkmk_configurecname)
-   [Enable the domain on your web site](#enabledomain)

<h2><a name="understanding-records"></a>Understanding DNS records</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configure your web sites for basic, shared or standard mode</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Add a DNS record for your custom domain</h2>

To associate your custom domain with an Azure Web Site, you must add a new entry in the DNS table for your custom domain by using tools provided by eNom. Use the following steps to locate the DNS tools for enom.com

1. Log on to your account with eNom, and select **Domains** and then **My Domains**. This will display your domain names.

2. From the **My Domains** page, use the **Manage Domain** field to select **Host Records**. This will display the host records fields.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. The Host Records editor allows you to select the specific record type using the **Record Type** field. For Azure Web Sites, you should only use the **CNAME (Alias)** or **A (Address)** selection.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecords.png)

	> [WACOM.NOTE] Before adding entries to the zone file, note that GoDaddy has already created DNS records for popular sub-domains (called **Host** in editor,) such as **email**, **files**, **mail**, and others. If the name you wish to use already exists, modify the existing record instead of creating a new one.

	* When adding a CNAME record, you must set the **Host Name** field to the sub-domain you wish to use. For example, **www**. You must set the **Address** field to the **.azurewebsites.net** domain name of your Azure Web Site. For example, **contoso.azurwebsites.net**.

		> [WACOM.NOTE] If you will be using an A record, you must also add a CNAME record with one of the following configurations:
		> 
		> * An **Alias** value of **www** with an **Other host** value of **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OR
		> 
		> * An **Alias** value of **awverify.www** with an **Other host** value of **awverify.&lt;yourwebsitename&gt;.azurewebsites.net.
		> 
		> This CNAME record is used by Azure to validate that you own the domain described by the A record.

	* When adding an A record, you must set the **Host Name** field to either **@** (this represents root domain name, such as **contoso.com**,) * (a wildcard for matching multiple sub-domains,) or the specific sub-domain you wish to use (for example, **www**.) You must set the **Address** field to the IP address of your Azure Web Site.

		> [WACOM.NOTE] When adding an A record, you must also add a CNAME record with a host of **awverify**, and a **Points to** of **awverify.&lt;yourwebsitename&gt;.azurewebsites.net.

5. When you have finished adding or modifying records, click **Save** to save the changes.

	> [WACOM.NOTE] It can take some time for your CNAME to propagate through the DNS system. You cannot set the CNAME for the web site until the CNAME has propagated. You can use a service such as <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> to verify that the CNAME is available.

<h2><a name="enabledomain"></a>Enable the domain name on your web site</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
