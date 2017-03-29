
# Configure Activity Log Alerts
## Overview
This article shows you how to set up activity log alerts for activity log events using the Azure portal.

[!NOTE] This feature is currently in private preview. Not all functionality may be available at this time.

You can receive an alert based on operations that were performed on resources in your subscription

You can configure the alert based on:
* Event Category (for [service health events click here](monitoring-activity-log-alerts-on-service-notifications.md))
- Resource Group
- Resource
- Resource Type
- Operation name
- The level of the notifications (Verbose, Informational, Warning, Error, Critical)
- Status
- Event initiated by

You can also the configure who the alert should be sent to:
* Select an existing Action Group
- Create a new Action Group (that can be later used for future alerts)

You can learn more about [Action Groups here](monitoring-action-groups.md)

You can configure and get information about service notification alerts using
* [Azure Portal](monitoring-activity-log-alerts.md)
- [Resource Manager templates](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## Create an alert on an activity log event with a new action group with the Azure Portal
1.	In the [portal](https://portal.azure.com), navigate to the **Monitor** service

    ![Monitor](./media/monitoring-activity-log-alerts/Home-Monitor.png)
2.	Click the **Monitor** option to open the Monitor blade. It first opens to the **Activity log** section.

3.	Now click on the **Alerts** section.

    ![Alerts](./media/monitoring-activity-log-alerts/Alerts-Blades.png)
4.	Select the **Add activity log alert** command and fill in the fields

5.	**Name** your activity log alert, and choose a **Description**. These will appear in the notifications sent when this alert fires.

    ![Add-Alert](./media/monitoring-activity-log-alerts/Add-Activity-Log-Alert.png)
6.	The **Subscription** should be auto filled to the subscription you are currently operating under.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/Activity-Log-Alert-New-Action-Group.png)
7.	Choose the **Resource Group** for this alert.

8.	Provide the **Event Category**, **Resource Group**, **Resource**, **Resource Type**, **Operation Name**, **Level**, **Status** and **Event intiated by** values to scope for what events this alert should apply.

9.	Create a **New** Action Group by giving it **Name** and **Short Name**; the Short Name will be referenced in the notifications sent when this alert fires.

10.	Then, define a list of receivers by providing the receiver’s

    a. **Name:** Receiver’s name, alias or identifier.

    b. **Action Type:** Choose to contact the receiver via SMS, Email, or Webhook

    c. **Details:** Based on the action type chosen, provide a phone number, email address or webhook URI.

11.	Select **OK** when done to create the alert.

Within a few minutes, the alert is active and triggers as previously described.

***Note:*** The action group defined in these steps will be reusable, as an existing action group, for all future alert definition.
## Create an alert on an activity log event for an existing action group with the Azure Portal
1.	In the [portal](https://portal.azure.com), navigate to the **Monitor** service

    ![Monitor](./media/monitoring-activity-log-alerts/Home-Monitor.png)
2.	Click the **Monitor** option to open the Monitor blade. It first opens to the **Activity log** section.

3.	Now click on the **Alerts** section.

    ![Alerts](./media/monitoring-activity-log-alerts/Alerts-Blades.png)
4.	Select the **Add activity log alert** command and fill in the fields

5.	**Name** your activity log alert, and choose a **Description**. These will appear in the notifications sent when this alert fires.

    ![Add-Alert](./media/monitoring-activity-log-alerts/Add-Activity-Log-Alert.png)
6.	The **Subscription** should be auto filled to the subscription you are currently operating under.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/Activity-Log-Alert-Existing-Action-Group.png)
7.	Choose the **Resource Group** for this alert.

8.	Provide the **Event Category**, **Resource Group**, **Resource**, **Resource Type**, **Operation Name**, **Level**, **Status** and **Event intiated by** values to scope for what events this alert should apply.

9.	Choose to **Notify Via** an **Existing action group**. Select the respective action group.

10.	Select **OK** when done to create the alert.

Within a few minutes, the alert is active and triggers as previously described.

## Managing your alerts ##

Once you have created an alert, it will be visible in the Alerts section of the Monitor service. Select the alert you wish to manage, you will be able to:
* **Edit** it.
* **Delete** it.
* **Disable** or **Enable** it if you want to temporarily stop of resume receiving notifications for the alert.

## Next Steps: ##
Get an [overview of alerts](monitoring-overview-alerts.md)  
Learn more about [action groups](monitoring-action-groups.md)  
Learn about [Service Notifications](monitoring-service-notifications.md)
