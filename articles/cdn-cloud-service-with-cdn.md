<properties linkid="cdn-cloud-service-with-cdn" urlDisplayName="Integrate a cloud application with Azure CDN" pageTitle="Integrate a cloud application with Azure CDN" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN, MVC, cloud service" description="A tutorial that teaches you how to deploy a cloud application that serves content from an integrated Azure CDN endpoint" metaCanonical="" services="cdn" documentationCenter=".NET" title="Integrate a cloud application with Azure CDN" authors="cephalin" solutions="" manager="wpickett" editor="tysonn" />

<a name="intro"></a>
# Integrate a cloud application with Azure CDN #
***By [Cephas Lin](https://twitter.com/Cephas_MSFT) Updated 18 July 2014.***

Azure CDN can be integrated with a cloud service, serving any content from the cloud service's CDN directory. This approach gives you the following advantages:

- Easily deploy and update images, scripts, and stylesheets in your cloud application's project directories
- Easily upgrade the NuGet packages in your cloud application, such as jQuery or Bootstrap versions 
- Manage your Web application and your CDN-served content all from the same Visual Studio interface
- Unified deployment workflow for your Web application and your CDN-served content
- Integrate ASP.NET bundling and minification with Azure CDN

## What you will learn ##

In this tutorial, you will learn how to:

-	[Integrate an Azure CDN endpoint with your cloud application and serve static content in your Web pages from Azure CDN](#deploy)
-	[Configure cache settings for static content in your cloud application](#caching)
-	[Serve content from controller actions through Azure CDN](#controller)
-	[Serve bundled and minified content through Azure CDN while preserving the script debugging experience in Visual Studio](#bundling)
-	[Configure fallback your scripts and CSS when your Azure CDN is offline](#fallback) 

## What you will build ##

You will deploy a cloud service Web role using the default ASP.NET MVC template, add code to serve content from an integrated Azure CDN, such as an image, controller action results, and the default JavaScript and CSS files, and also write code to configure the fallback mechanism for bundles served in the event that the CDN is offline.

## What you will need ##

This tutorial has the following prerequisites:

-	An active [Microsoft Azure account](http://azure.microsoft.com/en-us/account/)
-	Visual Studio 2013 with [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>You need an Azure account to complete this tutorial:</h5>
  <ul>
    <li>You can <a href="http://acom-int.azurewebsites.net/en-us/pricing/free-trial/?WT.mc_id=A261C142F">open an Azure account for free</a> - You get credits you can use to try out paid Azure services, and even after they're used up you can keep the account and use free Azure services, such as Web Sites.</li>
    <li>You can <a href="http://acom-int.azurewebsites.net/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">activate MSDN subscriber benefits</a> - Your MSDN subscription gives you credits every month that you can use for paid Azure services.</li>
  <ul>
</div>

<a name="deploy"></a>
## Deploy a cloud application with an integrated CDN endpoint ##

In this section, you will deploy the default ASP.NET MVC application template in Visual Studio 2013 to a cloud service Web role, and then integrate it with a new CDN endpoint. Follow the instructions below:

1. In Visual Studio 2013, create a new Azure cloud service from the menu bar by going to **File > New > Project > Cloud > Windows Azure Cloud Service**. Give it a name and click **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Select **ASP.NET Web Role** and click the **>** button. Click OK.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Select **MVC** and click **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Now, publish this Web role to an Azure cloud service. Right-click the cloud service project and select **Publish**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. If you have not yet signed into Microsoft Azure, click the **Sign In** button.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. In the sign-in page, sign in with the Microsoft account you used to activate your Azure account.
7. One you're signed in, click **Next**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. Assuming that you haven't created a cloud service or storage account, Visual Studio will help you create both. In the **Create Cloud Service and Account** dialog, type the desired service name. Then, click **Create**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

	>[WACOM.NOTE]Note that I'm using **East Asia** as the region for this cloud service. My goal here is to make sure that the cloud service (and the content in the storage account) is far enough away from the client (in this case, my computer in the US) to test the CDN later.
9. In the publish settings page, verify the configuration and click **Publish**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[WACOM.NOTE] The publishing process for cloud services takes a long time. The Enable Web Deploy for all roles option can make debugging your cloud application much quicker by providing fast (but temporary) updates to your Web roles. For more information on this option, see [Publishing a Cloud Service using the Azure Tools](http://msdn.microsoft.com/en-us/library/ff683672.aspx).

	When the **Windows Azure Activity Log** shows that publishing status is **Completed**, you will create a CDN endpoint that's integrated with this cloud service. 

1. To create a CDN endpoint, log into your [Azure management portal](http://manage.windowsazure.com/). 
2. Click **New** > **App Services** > **CDN** > **Quick Create**. Select **http://*&lt;servicename>*.cloudapp.net/cdn/** and click **Create**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png)

	>[WACOM.NOTE] Once your CDN endpoint is created, the Azure portal will show you its URL and the origin domain that it's integrated with. However, it can take awhile for the new CDN endpoint's configuration to be fully propagated to all the CDN node locations. 

	Note that the CDN endpoitn is tied to the path **cdn/** of your cloud service. You can either create a **cdn** folder in your **WebRole1** project, or you can use URL rewrite to strip all incoming links of this path. In this tutorial, you will take the latter route.

3. Back in the Azure portal, in the **CDN** tab, click the name of the CDN endpoint you just created.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png)

3. Click **Enable Query String** to enable query strings in the CDN cache. Once you enable this, the same link accessed with different query strings will be cached as separate entries.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png)

	>[WACOM.NOTE] While enabling the query string is not necessary for this tutorial section, you want to do this as early as possible for convenience since any change here is going to take time to propagate to all the CDN nodes, and you don't want any non-query-string-enabled content to clog up the CDN cache (updating CDN content will be discussed later).

3. Ping your CDN endpoint to make sure that it's propagated to the CDN nodes. You may need to wait up to an hour before it will respond to pings.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png)

2. Back in Visual Studio 2013, open **Web.config** in your **WebRole1** project and add the following code into the `<system.webServer>` tag:  
	<pre class="prettyprint">
	&lt;system.webServer&gt;
	  <mark>&lt;rewrite&gt;
	    &lt;rules&gt;
	      &lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt;
	        &lt;match url=&quot;^cdn/(.*)$&quot;/&gt;
	        &lt;action type=&quot;Rewrite&quot; url=&quot;{R:1}&quot;/&gt;
	      &lt;/rule&gt;
	    &lt;/rules&gt;
	  &lt;/rewrite&gt;</mark>
      ...
	&lt;/system.webServer&gt;
	</pre>

4. Publish the cloud service again. Right-click the cloud service project and select **Publish**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

1. When the publishing status is **Completed**, open a browser window and navigate to **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**. In my setup, this URL is:

		http://az632148.vo.msecnd.net/Content/bootstrap.css

	Which corresponds to the following origin URL at the CDN endpoint:

		http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

	After URL rewrite in my Web app, the actual file that gets cached to my CDN cache is:

		http://cephalinservice.cloudapp.net/Content/bootstrap.css

	When you navigate to **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**, you will be prompted to download the bootstrap.css that came from your published Web app. 

	![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

You can similarly access any publicly accessible URL at **http://*&lt;serviceName>*.cloudapp.net/**, straight from your CDN endpoint. For example:

-	A .js file from the /Script path
-	Any content file from the /Content path
-	Any controller/action 
-	If the query string is enabled at your CDN endpoint, any URL with query strings

In fact, using the above setup, you can host the entire cloud application from **http://*&lt;cdnName>*.vo.msecnd.net/**. If I navigate to **http://az632148.vo.msecnd.net/**, I get the action result from Home/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

This does not mean, however, that it's always a good idea (or generally a good idea) to serve an entire cloud application through Azure CDN. Some of the caveats are:

-	If the CDN endpoint goes offline for any reason, whether scheduled maintenance or user error, your entire cloud application goes offline unless the customers can be redirected to the origin URL **http://*&lt;serviceName>*.cloudapp.net/**. 
-	Even with the custom Cache-Control settings (see [Configure caching options for static files in your cloud application](#caching)), a CDN endpoint does not improve the performance of highly-dynamic content. If you tried to load the home page from your CDN endpoint as shown above, notice that it took at least 5 seconds to load the default home page the first time, which is a fairly simple page. Imagine what would happen to the client experience if this page contains dynamic content that must update every minute. Serving dynamic content from a CDN endpoint requires short cache expiration, which translates to frequent cache misses at the CDN endpoint. This hurts the performance or your cloud application and defeats the purpose of a CDN.

The alternative is to determine which content to serve from Azure CDN on a case-by-case basis in your cloud application. To that end, you have already seen how to access individual content files from the CDN endpoint. I will show you how to serve a specific controller action through the CDN endpoint in [Serve content from controller actions through Azure CDN](#controller).

<a name="caching"></a>
## Configure caching options for static files in your cloud application ##

With Azure CDN integration in your cloud application, you can specify how you want static content to be cached in the CDN endpoint. To do this, open *Web.config* from your Web role project (e.g. WebRole1) and add a `<staticContent>` element to `<system.webServer>`. The XML below configures the cache to expire in 3 days.  
<pre class="prettyprint">
&lt;system.webServer&gt;
  <mark>&lt;staticContent&gt;
    &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt;
  &lt;/staticContent&gt;</mark>
  ...
&lt;/system.webServer&gt;
</pre>

Once you do this, all static files in your cloud application will observe the same rule in your CDN cache. For more granular control of cache settings, add a *Web.config* file into a folder and add your settings there. For example, add a *Web.config* file to the *\Content* folder and replace the content with the following XML:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

This setting causes all static files from the *\Content* folder to be cached for 15 days.

For more information on how to configure the `<clientCache>` element, see [Client Cache &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

In [Serve content from controller actions through Azure CDN](#controller), I will also show you how you can configure cache settings for controller action results in the CDN cache.

<a name="controller"></a>
## Serve content from controller actions through Azure CDN ##

When you integrate a cloud service Web role with Azure CDN, it is relatively easy to serve content from controller actions through the Azure CDN. Other than serving your cloud application directly through Azure CDN (demonstrated above), [Maarten Balliauw](https://twitter.com/maartenballiauw) shows you how to do it with a fun MemeGenerator controller in [Reducing latency on the web with the Windows Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). I will simply reproduce it here.

Suppose in your cloud application you want to generate memes based on a Chuck Norris image like this:

	![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

You have a simple Index action that allows the customers to specify the superlatives in the image, then generates the meme once they post to the action. Since it's Chuck Norris, you would expect this page to become wildly popular globally. This is a good example of serving semi-dynamic content with Azure CDN. 

Follow the steps above to setup this controller action:

1. In the *\Controllers* folder, create a new .cs file called *MemeGeneratorController.cs* and replace the content with the following code. Be sure to replace the highlighted portion with your CDN name.  
	<pre class="prettyprint">
	using System.Diagnostics;
	using System.Drawing;
	using System.IO;
	using System.Web.Hosting;
	using System.Web.Mvc;
	using System.Web.UI;
	
	namespace WebRole1.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        //
	        // GET: /MemeGenerator/
	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
	        public ActionResult Index_Post(string top, string bottom)
	        {
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Show?top={0}&amp;bottom={1}&quot;, top, bottom));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://<mark>&lt;cdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Show?top={0}&amp;bottom={1}&quot;, top, bottom));
	            }
	        }
	
	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;~/Content/chuck.bmp&quot;);
	            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
	
	            using (Graphics graphics = Graphics.FromImage(bitmap)) 
	            {
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant()))
	                {
	                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(0, 0));
	                }
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant()))
	                {
	                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(0, 0));
	                }
	            }
	
	            MemoryStream ms = new MemoryStream();
	
	            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
	
	            return new FileStreamResult(ms, &quot;image/png&quot;);
	        }
	
	        private Font FindBestFitFont(Bitmap bitmap, Graphics graphics, string p)
	        {
	            Font f = new Font(FontFamily.GenericSansSerif, 20);
	
	            // Find best fit
	
	            return f;
	        }
		}
	}
	</pre>

2. Right-click in the default `Index()` action and select **Add View**.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Accept the settings below and click **Add**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Open the new *Views\MemeGenerator\Index.cshtml* and replace the content with the following simple HTML for submitting the superlatives:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Publish the cloud application again and navigate to **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index** in your browser. 

When you submit the form values to `/MemeGenerator/Index`, you reach the following controller action:  
<pre class="prettyprint">
[HttpPost, ActionName(&quot;Index&quot;)]
public ActionResult Index_Post(string top, string bottom)
{
    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Show?top={0}&amp;bottom={1}&quot;, top, bottom));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://&lt;cdnName&gt;.vo.msecnd.net/MemeGenerator/Show?top={0}&amp;bottom={1}&quot;, top, bottom));
    }
}
</pre>

If your local debugger is attached, then you will get the regular debug experience with a local redirect. If it's running in the cloud service, then it will redirect to:

	http://<cdnName>.vo.msecnd.net/MemeGenerator/Show?top=<formInput>&bottom=<formInput>

Which corresponds to the following origin URL at your CDN endpoint:

	http://cephalinservice.cloudapp.net/cdn/MemeGenerator/Show?top=<formInput>&bottom=<formInput>

After URL rewrite rule previously applied, the actual file that gets cached to your CDN endpoint is:

	http://cephalinservice.cloudapp.net/MemeGenerator/Show?top=<formInput>&bottom=<formInput>

You can then use the `OutputCacheAttribute` attribute to specify how the action result should be cached, which Azure CDN will honor. The code below specify a cache expiration of 1 hour (3,600 seconds).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Likewise, you can serve up content from any controller action in your cloud application through your Azure CDN, with the desired caching option.

In the next section, I will show you how to serve the bundled and minified scripts and CSS through Azure CDN. 

<a name="bundling"></a>
## Integrate bundling and minification with Azure CDN ##

Scripts and CSS stylesheets change infrequently and are prime candidates for the Azure CDN cache. Serving the entire Web role through your Azure CDN is the easiest way to integrate bundling and minification with Azure CDN. However, as you may not want to do this, I will show you how to do it while preserving the desired develper experience of ASP.NET bundling and minification, such as:

-	Great debug mode experience
-	Streamlined deployment
-	Immediate updates to clients for script/CSS version upgrades
-	Fallback mechanism when your CDN endpoint fails
-	Minimize code modification

In the **WebRole1** project that you created in [TODO](#integrate), open *App_Start\BundleConfig.cs* and take a look at the `bundles.Add()` method calls.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

The first `bundles.Add()` statement adds a script bundle at the virtual directory `~/bundles/jquery`. Then, open *Views\Shared\_Layout.cshtml* to see how the script bundle tag is rendered. You should be able to find the following line of Razor code:

    @Scripts.Render("~/bundles/jquery")

When this Razor code is run in the Azure Web role, it will render a `<script>` tag for the script bundle similar to the following: 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

However, when it is run in Visual Studio by typing `F5`, it will render each script file in the bundle individually (in the case above, only one script file is in the bundle):

    <script src="/Scripts/jquery-1.10.2.js"></script>

This enables you to debug the JavaScript code in your development environment while reducing concurrent client connections (bundling) and improving file download performance (minification) in production. It's a great feature to preserve with Azure CDN integration. Furthermore, since the rendered bundle already contains an automatically generated version string, you want to replicate that functionality so the whenever you update your jQuery version through NuGet, it can be updated at the client side as soon as possible.

Follow the steps below to integration ASP.NET bundling and minification with your CDN endpoint.

1. Back in *App_Start\BundleConfig.cs*, modify the `bundles.Add()` methods to use a different [Bundle constructor](http://msdn.microsoft.com/en-us/library/jj646464.aspx), one that specifies a CDN address. To do this, replace the `RegisterBundles` method definition with the following code:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://&lt;yourCDNName&gt;.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	Be sure to replace `<yourCDNName>` with the name of your Azure CDN.

	In plain words, you are setting `bundles.UseCdn = true` and added a carefully crafted CDN URL to each bundle. For example, the first constructor in the code:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	is the same as: 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	This constructor tells ASP.NET bundling and minification to render individual script files when debugged locally, but use the specified CDN address to access the script in question. However, note two important characteristics with this carefully crafted CDN URL:
	
	-	The origin for this CDN URL is `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, which is actually the virtual directory of the script bundle in your cloud application.
	-	Since you are using CDN constructor, the CDN script tag for the bundle no longer contains the automatically generated version string in the rendered URL. You must manually generate a unique version string every time the script bundle is modified to force a cache miss at your Azure CDN. At the same time, this unique version string must remain constant through the life of the deployment to maximize cache hits at your Azure CDN after the bundle is deployed.
	-	The query string v=<W.X.Y.Z> pulls from *Properties\AssemblyInfo.cs* in your Web role project. You can have a deployment workflow that includes incrementing the assembly version every time you publish to Azure. Or, you can just modify *Properties\AssemblyInfo.cs* in your project to automatically increment the version string every time you build, using the wildcard character '*'. For example:
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Any other strategy to streamline generating a unique string for the life of a deployment will work here.

3. Republish the cloud application and access the home page.
 
4. View the HTML code for the page. You should be able to see the CDN URL rendered, with a unique version string every time you republish changes to your cloud application. For example:  
	<pre class="prettyprint">
	...

    &lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...</pre>

5. In Visual Studio, debug the cloud application in Visual Studio by typing `F5`., 

6. View the HTML code for the page. You will still see each script file individually rendered so that you can have a consistent debug experience in Visual Studio.  
	<pre class="prettyprint">
	...
	
	    &lt;link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/&gt;
	&lt;link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/&gt;
	
	    &lt;script src=&quot;/Scripts/modernizr-2.6.2.js&quot;&gt;&lt;/script&gt;
	
	...
	
	    &lt;script src=&quot;/Scripts/jquery-1.10.2.js&quot;&gt;&lt;/script&gt;
	
	    &lt;script src=&quot;/Scripts/bootstrap.js&quot;&gt;&lt;/script&gt;
	&lt;script src=&quot;/Scripts/respond.js&quot;&gt;&lt;/script&gt;
	
	...    
	</pre>

<a name="fallback"></a>
## Fallback mechanism for CDN URLs ##

When your Azure CDN endpoint fails for any reason, you want your Web page to be smart enough to access your origin Web server as the fallback option for loading JavaScript or Bootstrap. It's one thing to lose images on your website due to CDN unavailability, but another to lose crucial page functionality provided by your scripts and stylesheets.

The [Bundle](http://msdn.microsoft.com/en-us/library/system.web.optimization.bundle.aspx) class contains a property called [CdnFallbackExpression](http://msdn.microsoft.com/en-us/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) that enables you to configure the fallback mechanism for CDN failure. To use this property, follow the steps below:

1. In your Web role project, open *App_Start\BundleConfig.cs*, where you added a CDN URL in each [Bundle constructor](http://msdn.microsoft.com/en-us/library/jj646464.aspx), and make the following highlighted changes to add fallback mechanism to the default bundles:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	When `CdnFallbackExpression` is not null, script is injected into the HTML to test whether the bundle is loaded successfully and, if not, access the bundle directly from the origin Web server. This property needs to be set to a JavaScript expression that tests whether the respective CDN bundle is loaded properly. The expression needed to test each bundle differs according to the content. For the default bundles above:
	
	-	`window.jquery` is defined in jquery-{version}.js
	-	`$.validator` is defined in jquery.validate.js
	-	`window.Modernizr` is defined in modernizer-{version}.js
	-	`$.fn.modal` is defined in bootstrap.js
	
	You might have noticed that I did not set CdnFallbackExpression for the `~/Cointent/css` bundle. This is because currently there is a [bug in System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) that injects a `<script>` tag for the fallback CSS instead of the expected `<link>` tag.
	
	There is, however, a good [Style Bundle Fallback](https://github.com/EmberConsultingGroup/StyleBundleFallback) offered by [Ember Consulting Group](https://github.com/EmberConsultingGroup). 

2. To use the workaround, create a new .cs file in your Web role project's *App_Start* folder called *StyleBundleExtensions.cs*, and replace its content with the [code from GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 

4. In *App_Start\StyleFundleExtensions.cs*, rename the namespace to your Web role's name (e.g. **WebRole1**). 

3. Go back to `App_Start\BundleConfig.cs` and modify the last `bundles.Add` statement with the following highlighted code:  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	This new extension method uses the same idea to inject script in the HTML to check the DOM for the a matching class name, rule name, and rule value defined in the CSS bundle, and falls back to the origin Web server if it fails to find the match.

4. Publish the cloud application again and access the home page. 
5. View the HTML code for the page. You should find in injected scripts:    
	<pre class="prettyprint">...
	
		&lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/&gt;
	<mark>&lt;script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i &lt; len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;&lt;link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; /&gt;&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;&lt;script src=&quot;/Content/css&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.Modernizr)||document.write(&#39;&lt;script src=&quot;/bundles/modernizr&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...	
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.jquery)||document.write(&#39;&lt;script src=&quot;/bundles/jquery&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;($.fn.modal)||document.write(&#39;&lt;script src=&quot;/bundles/bootstrap&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...
	</pre>

	Note that injected script for the CSS bundle still contains the errant remnant from the `CdnFallbackExpression` property in the line:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	But since the first part of the || expression will always return true (in the line directly above that), the document.write() function will never run.

# More Information #
[Overview of the Azure Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
