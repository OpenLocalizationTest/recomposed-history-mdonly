<properties
   pageTitle="Azure Active Directory Reporting: Getting started | Microsoft Azure"
   description="Lists the various available reports in Azure Active Directory reporting"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="markvi"/>

# Azure Active Directory reporting 

With Azure Active Directory reporting, you get all the information you need to determine how your environment is doing.

There are two main areas of reporting:

- **Sign-in activities** – Information about the usage of managed applications and user sign-in activities 

- **System activities** - Audit information about users and group management, your managed applications and directory activities 

Depending on the scope of the data you are looking for, you can access these reports either by clicking **User management** or **Enterprise applications** in the left navigation bar of the Azure management portal. 


 

## Sign-in activities


### User sign-in activities 

With the information provided by the user sign-in report, you find answers to questions such as:

- What is the sign-in pattern of a user?
- How many users have users signed-in over a week?
- What’s the status of these sign-ins?

Your entry point to this data is the user sign-in graph in the **Overview** section under **User management**. 

 ![Reporting](./media/active-directory-reporting/05.png "Reporting")

The user sign-in graph shows weekly aggregations of sign ins for all users in a given time period. The default for the time period is 30 days. 
 

![Reporting](./media/active-directory-reporting/02.png "Reporting")
 

When you click on a day in the sign-in graph, you get a detailed list of the sign-in activities.

![Reporting](./media/active-directory-reporting/03.png "Reporting")
 

Each row in the sign-in activities list gives you the detailed information about the selected sign-in such as:

- Who has signed-in?

- What was the related UPN?

- What application was the target of the sign-in?

- What is the IP address of the sign-in?

- What was the status of the sign-in?


### Usage of managed applications

With an application centric view of your sign-in data, you can answer questions such as:

- Who is using my applications?

- What are the top 3 applications in your organization?

- I have recently rolled-out an application. How is it doing?


Your entry point to this data is the top 3 applications in your organization within the last 30 days report in the **Overview** section under **Enterprise applications**.

 ![Reporting](./media/active-directory-reporting/06.png "Reporting")


The app usage graph weekly aggregations of sign ins for your top 3 applications in a given time period. The default for the time period is 30 days.

![Reporting](./media/active-directory-reporting/top3apps.png "Reporting")

If you want to, you can set the focus on a specific application. 

![Reporting](./media/active-directory-reporting/single_spp_usage_graph.png "Reporting")


When you click on a day in the app usage graph, you get a detailed list of the sign-in activities.


![Reporting](./media/active-directory-reporting/top_app_sign_ins.png "Reporting")



The Usage option gives you a complete overview of all sign-in events to your applications.

![Reporting](./media/active-directory-reporting/sign-ins_from_left_nav.png "Reporting")

By using the column chooser, you can select the data fields you want to display.

![Reporting](./media/active-directory-reporting/column_chooser.png "Reporting")



### Filtering sign-ins

You can filter sign-ins by a time interval to limit the amount of displayed data.

![Reporting](./media/active-directory-reporting/927.png "Reporting")


Another method to filter the entries of a audit log is to search for specific entries.
The search method enables scope your sign-ins around specific users, groups or applications.


![Reporting](./media/active-directory-reporting/sign_ins_search.png "Reporting")




## System activities

The auditing logs in Azure Active Directory provide records of system activities for compliance.

There are three main categories for auditing related activities in the Azure portal:

- Users and groups   

- Applications

- Directory   


For a complete list of audit report activities, see the [list of audit report events](active-directory-reporting-audit-events.md#list-of-audit-report-events).


Your entry point to all auditing data is **Audit logs** in the **Activity** section of **Azure Active Directory**.


![Auditing](./media/active-directory-reporting/61.png "Auditing")


An audit log has a list view that shows the actors (who), the activities (what) and the targets. 


![Auditing](./media/active-directory-reporting/345.png "Auditing")


By clicking an item in the list view, you can get more details about it.

![Auditing](./media/active-directory-reporting/873.png "Auditing")




### Users and groups audit logs


With user and group-based audit reports, you can get answers to questions such as:

- What types of updates have been applied the users?

- How many users were changed?

- How many passwords were changed? 

- What has an administrator done in a directory?

- What are the groups that have been added?

- Are there groups with membership changes?

- Have the owners of group been changed? 


If you just want to review auditing data that is related to users and groups, you can find a filtered view under **Audit logs** in the **Activity** section of **Users and Groups**.


![Auditing](./media/active-directory-reporting/93.png "Auditing")


### Application audit logs 

With application-based audit reports, you can get answers to questions such as:

- What are the applications that have been added?

- What are the applications that have been removed?

- Has a service principle for an application changed?

- Have the names of applications been changed? 


If you just want to review auditing data that is related to applications, you can find a filtered view under **Audit logs** in the **Activity** section of **Enterprise applications**.


![Auditing](./media/active-directory-reporting/134.png "Auditing")


### Filtering audit logs 

You can filter an audit report by a time interval to limit the amount of displayed data.

![Auditing](./media/active-directory-reporting/324.png "Auditing")

Another method to filter the entries of a audit log is to search for specific entries.
 
![Auditing](./media/active-directory-reporting/237.png "Auditing")