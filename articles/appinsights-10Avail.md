<properties title="Monitor your web app's availability and responsiveness" pageTitle="Web tests in Application Insights" description="Make sure your web application is available and responsive. Get alerts if it becomes unavailable or responds slowly." metaKeywords="analytics web test" authors="awills"  />
 
# H1 (Monitor your web app's availability and responsiveness) 

If your web app is unavailable to your users, you'll want to know about it. After you've deployed your web app, set up web tests to monitor its availability and responsiveness. Application Insights will send web requests at regular intervals from points around the world, and can alert you if your application responds slowly or not at all.

You can set up web tests for any HTTP endpoint that is accessible from the public internet.


+ [1. Create a new application?] 
+ [2. Set up a web test]
+ [3. See monitor reports]
+ [4. Edit or disable a test]
+ [Next steps]



## 1. Create a new application?

Skip this step if you're already [monitoring usage and health][setup] for this web app, and you want to see the availability data in the same place.

But if you want to keep these results separate, login to Microsoft Azure preview and create a new application in Application Insights. 

![New > Application Insights](./media/appinsights/appinsights-11newApp.png)

## 2. Set up a web test

In the home blade for your application in Application Insights, click through the empty webtests tile. 

![Click the empty availability test](./media/appinsights/appinsights-12avail.png)

> *Already got web tests? Click through the webtests tile and then choose Add Webtest.*

Provide the URL that you want to test. It has to be visible from the public internet.

![Fill at least the URL of your website](./media/appinsights/appinsights-13availChoices.png)

- Test locations are the places from where our servers send web requests to your URL. Choose more than one so that you can distinguish problems in your website from network issues.

- Success criteria - Specify the acceptable HTTP return codes - 200 is usual. 

    You can also specify a string that should be found in every correct response. It must be a plain string, without wildcards. Don't forget that if your content changes you might have to update it.

- Alerts - By default, alerts are sent to you if there are repeated failures over 15 minutes. But you can change it to be more sensitive, and you change the notified email addresses.

### Test more URLs

You can add more tests for as many URLs as you like. For example, as well as testing your home page, you could make sure your database is running by testing the URL for a search.

![On the web tests blade, choose Add](./media/appinsights/appinsights-16anotherWebtest.png)


## 3. See monitor reports

After 1-2 minutes, close and re-open the application blade. (In this release, it doesn't refresh automatically.)

![Summary results on the home blade](./media/appInsights/appinsights-14availSummary.png)

This is a summary. If you've defined several web tests for this application, they're all combined here.

Click through to the Webtests blade to see a list of your web tests.

Open a specific web test.

![Click a specific webtest](./media/appInsights/appinsights-15webTestList.png)

In the specific web test blade, scroll down to Failed Results and pick a result.

![Click a specific webtest](./media/appInsights/appinsights-17-availViewDetails.png)

The result shows the reason for failure.

![Webtest run result](./media/appInsights/appinsights-18-availDetails.png)

For even more detail, download the result file and inspect it in Visual Studio.

## 4. Edit or disable a test

Open an individual test to edit or disable it.

![Edit or disable a web test](./media/appInsights/appinsights-19-availEdit.png)

You might want to disable web tests while you are performing maintenance on your service.

## Next steps

[Search diagnostic logs][diagnostics]

<!--Anchors-->
[1. Create a new application?]: #subheading-1
[2. Set up a web test]: #subheading-2
[3. See monitor reports]: #subheading-3
[4. Edit or disable a test]: #subheading-4
[Next steps]: #next-steps

<!--Link references-->
[setup]: ../appInsights-01-start/
[setup existing]: ../appInsights-02-existing/
[diagnostics]: ../appInsights-24diagnostics/

