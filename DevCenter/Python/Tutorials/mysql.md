  <properties linkid="dev-python-mysql" urlDisplayName="Web App with MySQL" headerExpose="" pageTitle="Django Hello World - MySQL Edition" metaKeywords="" footerExpose="" metaDescription="" umbracoNaviHide="0" disqusComments="1" />
  <h1>Django Hello World - MySQL Edition</h1>
  <p>This tutorial describes how to use MySQL to access data from a Windows Azure Cloud Service application written in the Django Python web framework. This guide assumes that you have some prior experience using Windows Azure and Django. For an introduction to Windows Azure and Django, see <a href="http://www.windowsazure.com/en-us/develop/python/tutorials/getting-started/" style="color:red;">Django Hello World</a>. The guide also assumes that you have some knowledge of MySQL. For an overview of MySQL, see the <a href="http://dev.mysql.com/doc/">MySQL website</a>.</p>
  <p>In this tutorial, you will learn how to:</p>
  <ul>
    <li>
      <p>Setup a Windows Azure virtual machine to host MySQL.</p>
    </li>
    <li>
      <p>Install a <a href="http://pypi.python.org/pypi/MySQL-python/1.2.3">MySQL driver</a> for Python.</p>
    </li>
    <li>
      <p>Configure an existing Django application to use a MySQL database.</p>
    </li>
	<li>
      <p>Use MySQL directly from Python.</p>
    </li>
    <li>
      <p>Run your MySQL Django application locally using the Windows Azure compute emulator.</p>
    </li>
    <li>
      <p>Deploy the MySQL virtual machine to Windows Azure.</p>
    </li>
    <li>
      <p>Publish your MySQL Django application to Windows Azure.</p>
    </li>
  </ul>

  <p>You will expand upon the <a href="TODO" style="color:red;">Django Hello World</a> sample by utilizing a MySQL database, hosted in a Windows Azure VM,
  to find an interesting replacement for <i>World</i>. The replacement will in turn be determined via a MySQL-backed Django <i>counter</i> app. As was the case for the Hello World sample, this Django application will again be hosted in a web role.</p>

  <p>The project files for this tutorial will be stored in <strong><em>C:\django\helloworld\*</em></strong> and the completed application will look similar to:</p>
![][0]
  
  <h2>Setting up your development environment</h2>
  <p>Before you can begin developing your Windows Azure application, you need to get the tools and set up your development environment. For details about getting and installing the Windows Azure SDK for Python, see <a href="http://www.windowsazure.com/en-us/develop/python/tutorials/getting-started/#setup">Setup the Development Environment</a> in the Python "Hello World" Application tutorial.</p>
  <p><b>NOTE: </b>This tutorial requires Python 2.7 and Django 1.4. These versions are included in the current Windows Azure SDK for Python; however if you have installed a previous version you will need to <a href="http://www.windowsazure.com/en-us/develop/python/tutorials/getting-started/#setup">upgrade to the latest version</a>.</p>

<h2>Install the MySQL Python package</h2>
<p>You must download and install the MySQL Python package, in addition to installing the Windows Azure SDK for Python. You can install it directly <a href="http://www.codegood.com/download/10/">from this link</a>. Once completed, run the following command to verify your installation:</p>
<pre class="prettyprint">%SystemDrive%\Python27\python.exe -c "import MySQLdb"
</pre>
<p>The expected output is empty:</p>
![][1]

## Setting up a virtual machine to host MySQL
- Follow the instructions given <a style="color:red;" href="http://msdn.microsoft.com/en-us/library/windowsazure/gg433121.aspx">here</a> to create and configure a Windows Server 2008 R2 VM through the Preview Portal.

- **TODO**: "Open up TCP port for MySQL". Status: to be written by dfugate; I'm sure we need to do something special to open up port 3771 to outside traffic in Windows Azure before this tutorial will work. Not quite sure how to make this happen yet...

- Install the latest version of <a href="http://dev.mysql.com/downloads/">MySQL</a> for Windows on the virtual machine. Some useful hints are:
    <ul>
      <li>Do a <i>Full</i> installation.</li>
      <li>Set the MySQL configuration as <i>Dedicated</i>.</li>
      <li>Enable TCP/IP Networking on port <i>3306</i> (the default).</li>
    </ul>


- After MySQL has finished installing, click the Windows <i>Start</i> menu and run the freshly installed <i>MySQL 5.5 Command Line Client</i>.  Next, enter the following commands:


		CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
		CREATE DATABASE djangoazure;
		GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
		GRANT ALL ON world.* TO 'testazureuser'@'%';
		USE world;
		SELECT name from country LIMIT 1;

You should now see this response: 


![][2]

- Give MySQL access through the Windows Firewall

	<ol>
		<li>Click the Windows <i>Start</i> button and then the <i>Control Panel</i>.</li>
        <li>Click <i>Check Firewall Status</i>.</li>
        <li>Click <i>Allow a program or feature through Windows Firewall</i>.</li>
        <li>Scroll down to <i>MySQL55</i> in the <i>Allowed programs and features</i> list and ensure that <b>both</b> <i>Home/Work (Private)</i> and <i>Public</i> are checked.</li>
	</ol>

You should end up with this:
 


![][3]

## Extend the Django Hello World application
  <ol>
    <li>
      <p>Follow the instructions given in the <a href="TODO">Django Hello World</a> tutorial to create a trivial "Hello World" web application in Django.</p>
    </li>
    <li>
      <p>Open <strong><em>C:\django\helloworld\hello_dj\hello_dj\settings.py</em></strong> in your favorite text editor.  Modify the <b><i>DATABASES</i></b> global dictionary to read: </p>
<pre>	
		DATABASES = {
		    'default': {
		        'ENGINE': <b style="color:orange;">'django.db.backends.mysql'</b>
				'NAME': <b style="color:orange;">'djangoazure'</b>,               
			    'USER': <b style="color:orange;">'testazureuser'</b>,  
			    'PASSWORD': <b style="color:orange;">'testazure'</b>,
			    'HOST': <b style="color:red;">'127.0.0.1'</b>, 
			    'PORT': <b style="color:orange;">'3306'</b>,
			    }
			}
</pre>

<p>

As you can see, we've just given Django instructions on where to find our MySQL database. 
</p>

**IMPORTANT NOTE:**You <b style="color:red;">must</b> change the <i>HOST</i> key to match your Windows Azure (MySQL) VM's <b>permanent</b> IP address. At this point, <i>HOST</i> should be set to whatever the <i>ipconfig</i> Windows command reports it as being.

<p>After you've modified <i>HOST</i> to match the MySQL VM's IP address, please save this file and close it.</p>

    </li>
    <li>
      <p>Now that we've referenced our <i>djangoazure</i> database, let's do something useful with it! To this end, we'll create a model for a trivial <i>counter</i> app.  To instruct Django to create this, run the following commands:<pre class="prettyprint">
cd C:\django\helloworld\hello_dj\hello_dj
%SystemDrive%\Python27\python.exe manage.py startapp counter
</pre>If Django doesn't report any output from the final command above, it succeeded.</p>
    </li>
    <li>
      <p>Append the following text to <strong><em>C:\django\helloworld\hello_dj\hello_dj\counter\models.py</em></strong>:<pre class="prettyprint">class Counter(models.Model):
    count = models.IntegerField()
    def __unicode__(self):
        return u'%s' % (self.count)</pre>All we've done here is defined a subclass of Django's <i>Model</i> class named <i>Counter</i> with a single integer field, <i>count</i>. This trivial counter model will end up recording the number of hits to our Django application.</p>
    </li>
    <li>
      <p>Next we make Django aware of <i>Counter</i>'s existence:
		<ol>
			<li>Edit <strong><em>C:\django\helloworld\hello_dj\hello_dj\settings.py</em></strong> again. Add <i>'counter'</i> to the <i>INSTALLED_APPS</i> tuple.</li>
			<li>From a command prompt, please run:<pre class="prettyprint">cd C:\django\hello_dj\hello_dj
%SystemDrive%\Python27\python manage.py sql counter
%SystemDrive%\Python27\python manage.py syncdb
</pre>These commands store the <i>Counter</i> model in the live Django database, and result in output similar to the following:<pre>C:\django\helloworld\hello_dj\hello_dj> %SystemDrive%\Python27\python manage.py sql counter
BEGIN;
CREATE TABLE `counter_counter` (
    `id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY,
    `count` integer NOT NULL
)
;
COMMIT;

C:\django\helloworld\hello_dj\hello_dj> %SystemDrive%\Python27\python manage.py syncdb
Creating tables ...
Creating table auth_permission
Creating table auth_group_permissions
Creating table auth_group
Creating table auth_user_user_permissions
Creating table auth_user_groups
Creating table auth_user
Creating table django_content_type
Creating table django_session
Creating table django_site
Creating table counter_counter

You just installed Django's auth system, which means you don't have any superusers defined.
Would you like to create one now? (yes/no): no
Installing custom SQL ...
Installing indexes ...
Installed 0 object(s) from 0 fixture(s)</pre></li>
		</ol>
</p>
    </li>
    <li>
      <p>Replace the contents of <strong><em>C:\django\helloworld\hello_dj\hello_dj\views.py</em></strong>. The new implementation of the <i>hello</i> function below uses our <i>Counter</i> model in conjunction with a separate sample database, <i>world</i> (distributed with MySQL), to generate a suitable replacement for the "<i>World</i>" string:</p>
      <pre class="prettyprint">
from django.http import HttpResponse

from MySQLdb import connect
from settings import DATABASES
from counter.models import Counter

def getCountry(intId):
    #Connect to the MySQL sample database 'world'
    connection = connect(db='world',
                         user=DATABASES['default']['USER'],
                         passwd=DATABASES['default']['PASSWORD'],
                         host=DATABASES['default']['HOST'],  
                         port=int(DATABASES['default']['PORT']))
    cur = connection.cursor()

    #Execute a trivial SQL query which returns the name of 
    #all countries contained in 'world'
    cur.execute("SELECT name from country")
    tmp = cur.fetchall()
    
    #Clean-up after ourselves
    cur.close()
    connection.close()

    if intId >= len(tmp):
        return "countries exhausted"
    return tmp[intId][0]

def hello(request):
    if len(Counter.objects.all())==0:
        #when the database corresponding to 'helloworld.counter' is 
        #initially empty...
        c = Counter(count=0)
        c.save()
    else:
        c = Counter.objects.all()[0]
    c.count += 1
    c.save()
   
    world = getCountry(int(c.count))
    return HttpResponse("&lt;html&gt;&lt;body&gt;Hello &lt;em&gt;" + world + "&lt;/em&gt;&lt;/body&gt;&lt;/html&gt;")
</pre>
    </li>
  </ol>

<h2>Running Your Application Locally</h2> 
  <p>Before running your application in the Windows Azure emulator, let's run it as a normal Django application to ensure everything's working properly:<pre class="prettyprint">
pushd C:\django\helloworld\hello_dj\hello_dj
%SystemDrive%\Python27\python.exe manage.py runserver
"%ProgramFiles%\Internet Explorer\iexplore.exe" http://localhost:<b style="color:orange;">8000</b>/hello
</pre>

<p>Note, that you might need to change port <b style="color:orange;">8000</b> to another port depending upon how Django configured your application locally.</p>You should see output similar to the following in your web browser:
</p>

![][4] 

<p>Refresh the web browser a few times and you should see the message change from "<i>Hello Aruba</i>" to "<i>Hello <em>&lt;some other country&gt;</em></i>".
</p>
  
  <h2>Running Your Application Locally in the Emulator</h2> 

  <p>Start the Windows Azure emulator and open the Django webpage exactly as you did in the <a href="TODO" style="color:red;">Django Hello World</a> tutorial</p>
  <p>The output should be essentially the same to what you saw with the locally hosted version:</p>
![][5]


##Deploying the application to Windows Azure
  From here, all you need to do is duplicate the steps performed in the <a href="TODO" style="color:red;">Django Hello World</a> tutorial to publish the MySQL derivation to Windows Azure.
  <p style="color:red;">TODO: "Recap the steps from the Django Hello World tutorial here!"
Status: to be written by dfugate; waiting for the original tutorial to be finalized (or chunk'ed)
</p>

<p style="color:red">TODO - this next section is just a guess at what our PS cmdlet story is going to look like. Also need to throw something in here about shutting down the MySQL VM.</p>

## Stop and Delete the Application

Windows Azure bills role instances per hour of server time consumed, and server time is consumed while your application is deployed, even if the instances are not running and are in the stopped state. With your web role plus one instance of the MySQL VM, your application
is currently running two Windows Azure instances.

The following steps describe how to stop and delete your application.

1.  In the Windows PowerShell window, call the **Stop-AzureService**
    command to stop the service deployment created in the previous section:

		PS C:\django\helloworld> Stop-AzureService

    Stopping the service may take several minutes. When the service is stopped, you will receive a message indicating that it has stopped.

2.  To delete the service, call the **Remove-AzureService** command:

    	PS C:\django\helloworld> Remove-AzureService
              

3.  When prompted, enter **Y** to delete the service.

    After the service has been deleted you will receive a message indicating that the service has been deleted.

[0]: ../Media/mysql_tutorial01.png
[1]: ../Media/mysql_tutorial01-1.png
[2]: ../Media/mysql_tutorial01-2.png
[3]: ../Media/mysql_tutorial01-3.png
[4]: ../Media/mysql_tutorial01.png
[5]: ../Media/mysql_tutorial01.png
 