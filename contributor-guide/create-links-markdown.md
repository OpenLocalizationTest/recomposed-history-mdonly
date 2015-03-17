<properties
   pageTitle="Create links in markdown articles" description="Explains how to code crosslinks in markdown." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="02/03/2015" ms.author="tysonn" />

#Linking guidance for Azure technical content
##Guidelines for technical articles on azure.microsoft.com

| Link scenario | Guidance  |
|---------------|-----------|
|Linking from an ACOM article to another ACOM article|Use relative links. Do not include the en-us language locale in your relative links.|
|​Linking to an MSDN library topic, a TechNet library topic, or KB article|​Use the actual link to the article or topic, but remove the en-us language locale from the link.|
|Linking from an ACOM article to any other web page|Use the direct link|

###Markdown syntax for ACOM relative links

To create an inline link to a page in the same directory as the current page (this is the case for all our ACOM technical articles), use the dot notation. Include the markdown file name extension:

    [link text](./article-name.md)
    [Create a Media Services account](./media-services-create-account.md)

To link to an anchor within the same article:

    [link text](article-name.md#anchor-name
    [Create cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-cache)

To link to an anchor in another article:

    [link text](./article-name.md#anchor-name)
    [Configure your profile](./media-services-create-account.md#configure-your-profile)

To link to a page in a different directory (to link from a tech doc to a feature page, for example), you need to use the two-dot syntax (../) to move the correct number of steps up the URL path. No file name extension is needed. For example, to link from an article to the virtual-machines service page, you would use this notation:

    [link text](../../services/virtual-machines/)
 
To link to the .NET samples page from an article:

    [link text](../../../develop/net/samples/)

To test your links, push your page to your fork, and view it in the rendered view. The cross links on the GitHub version of the page should work as long as the targets of the URLs are present in your fork.

Our [markdown template for technical articles](../markdown templates/markdown-template-for-new-articles.md/) shows an alternate way to create crosslinks in markdown so all the crosslinks are coded together at the end of the article, even while they display inline. 

##Guidelines for the Azure library on MSDN

| Link scenario | Guidance  |
|---------------|-----------|
|Linking to another topic in the same database|​Use the standard, GUID-based links in DxStudio|
|Linking to a topic in a different database|Create an external link in the link creation UI, and use a direct link that does not contain the language locale|
|Linking to an ACOM article|Use a direct link, but strip the language locale out of the link.|
|Linking to any other page on the web​|Use a direct link.|


##Remember the Azure library chrome!
If you want to link to an Azure library topic that lives under [this node](https://msdn.microsoft.com/library/azure), remember to specify the Azure chrome in the link (/azure/). The Azure chrome shares the ACOM navigation options and displays only the Azure content of the MSDN library. A properly scoped link looks like this:

    http://msdn.microsoft.com/library/azure/dd163896.aspx

Otherwise, the page will be rendered in the standard MSDN view, with the entire MSDN tree displayed.

##FWLinks

Avoid FWLinks (our redirection system). They should be used only as a last resort when you need to create a link for a page whose URL you don't yet know. They are almost never needed for ACOM articles or MSDN library articles. For ACOM, you define the file name, so you can know what it will be ahead of time. For a library topic that is not yet published, you can create a link that uses the topic GUID so that you don't have to use an FWLink. 

If you must use an FWLink on a web page, include the P parameter to make it a permanent redirect:

    http://go.microsoft.com/fwlink/p/?LinkId=389595

When you paste the target URL into the FWLink tool, remember to remove the locale if your target link is ACOM, or the MSDN or TechNet library.

##Linking from the Help drawer

See the [Help Drawer content guidance](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Help%20drawer%20content%20guidance.aspx) for linking information specific to the Help Drawer.

###Contributors' Guide Links

- [Overview article](./../CONTRIBUTING.md)
- [Index of guidance articles](./contributor-guide-index.md)

<!--image references-->
[1]: ./media/create-tables-markdown/table-markdown.png
[2]: ./media/create-tables-markdown/break-tables.png
