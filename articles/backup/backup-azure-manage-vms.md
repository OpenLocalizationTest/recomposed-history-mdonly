
<properties
	pageTitle="Manage and monitor Resource Manager-deployed virtual machine backups | Microsoft Azure"
	description="Learn how to manage and monitor Resource Manager-deployed virtual machine backups"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2016"
	ms.author="jimpark; markgal;"/>

# Manage and monitor Azure virtual machine backups

> [AZURE.SELECTOR]
- [Manage Azure VM backups](backup-azure-manage-vms.md)
- [Manage Classic VM backups](backup-azure-manage-vms-classic.md)

This article provides guidance on how to manage the backups for your virtual machines (VM), as well as explain the backup alerts information available in the portal dashboard. The guidance in this article applies to using VMs with Recovery Services vaults. This article does not cover the creation of virtual machines, nor does it explain how to protect virtual machines. For a primer on protecting Azure Resource Manager-deployed VMs in Azure with a Recovery Services vault, see [First look: Back up VMs to a Recovery Services vault](backup-azure-vms-first-look-arm.md).

## Access vaults and protected virtual machines

In the Azure portal, the Recovery Services vault dashboard provides access to information about the vault including:

- the most recent backup snapshot, which is also the latest restore point <br\>
- the backup policy <br\>
- total size of all backup snapshots <br\>
- number of virtual machines that are protected with the vault <br\>

Many management tasks with a virtual machine backup begins with opening the vault in the dashboard. However, because vaults can be used to protect multiple items (or multiple virtual machines), to view the details about a particular virtual machine, you need to open the vault item dashboard. The following procedure shows you how to open the *vault dashboard* and then continue to the *vault item dashboard*. There are "tips" in both procedures that point out how to add the vault and vault item to the Azure dashboard by using the Pin to dashboard command. Pin to dashboard is a way of creating a shortcut to the vault or item. You can also execute common commands from the shortcut.

>[AZURE.TIP] If you have multiple dashboards and blades open, you can use the dark-blue slider at the bottom of the window to slide the view in the Azure dashboard back and forth.

![Full view with slider](./media/backup-azure-manage-vms/bottom-slider.png)

### Open a Recovery Services vault in the dashboard:

1. Sign in to the [Azure portal](https://portal.azure.com/).

2. On the Hub menu, click **Browse** and in the list of resources, type **Recovery Services**. As you begin typing, the list will filter based on your input. Click **Recovery Services vault**.

    ![Create Recovery Services Vault step 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    The list of Recovery Services vaults are displayed.

    ![List of Recovery Services vaults ](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

    >[AZURE.TIP] If you pin a vault to the Azure Dashboard, that vault is immediately accessible when you open the Azure portal. To pin a vault to the dashboard, in the vault list, right-click the vault, and select **Pin to dashboard**.

3. From the list of vaults, select the vault to open its dashboard. When you select the vault, the vault dashboard and the **Settings** blade open. In the following image, the **Contoso-vault** dashboard is highlighted.

    ![Open vault dashboard and Settings blade](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### Open a vault item dashboard

In the previous procedure you opened the vault dashboard. To open the vault item dashboard:

1. In the vault dashboard, on the **Backup Items** tile, click **Azure Virtual Machines**.

    ![Open backup items tile](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    The **Backup Items** blade lists the last backup job for each item. In this example, there is one virtual machine, demovm-markgal, protected by this vault.  

    ![Backup items tile](./media/backup-azure-manage-vms/backup-items-blade.png)

    >[AZURE.TIP] For ease of access, you can pin a vault item to the Azure Dashboard. To pin a vault item, in the vault item list, right-click the item and select **Pin to dashboard**.

2. In the **Backup Items** blade, click the item to open the vault item dashboard.

    ![Backup items tile](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    The vault item dashboard and its **Settings** blade opens.

    ![Backup items dashboard with Settings blade](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    From the vault item dashboard, you can accomplish many key management tasks, such as:

    - change policies or create a new backup policy<br\>
	- view restore points, and see their consistency state <br\>
	- on-demand back up of a virtual machine <br\>
	- stop protecting virtual machines <br\>
	- resume protection of a virtual machine <br\>
	- delete a backup data (or recovery point) <br\>
	- [restore a backup (or recovery point)](./backup-azure-arm-restore-vms.md#restore-a-recovery-point)  <br\>

For the following procedures, the starting point is the vault item dashboard.

## Change policies or Create a new backup policy

1. On the [vault item dashboard](backup-azure-manage-vms.md#open-a-vault-item-dashboard), click **All Settings** to open the **Settings** blade.

    ![Backup policy blade](./media/backup-azure-manage-vms/all-settings-button.png)

2. On the **Settings** blade, click **Backup policy** to open that blade.

    On the blade, the backup frequency and retention range details are shown.

    ![Backup policy blade](./media/backup-azure-manage-vms/backup-policy-blade.png)

3. From the **Choose backup policy** menu:
    - To change policies, select a different policy and click **Save**. The new policy is immediately applied to the vault. <br\>
    - To create a new policy, select **Create New**.

    ![Virtual machine backup](./media/backup-azure-manage-vms/backup-policy-create-new.png)

    For instructions on creating a backup policy, see [Defining a backup policy](backup-azure-manage-vms.md#defining-a-backup-policy).


## On-demand backup of a virtual machine
You can take an on-demand backup of a virtual machine once it is configured for protection. If the initial backup is pending for the virtual machine, on-demand backup will create a full copy of the virtual machine in the Recovery Services vault. If the initial backup is completed, an on-demand backup will only send changes from the previous snapshot, to the Recovery Services vault i.e. it is always incremental.

>[AZURE.NOTE] The retention range for an on-demand backup is the retention value specified for the Daily backup point in the policy. If no Daily backup point is selected, then the weekly backup point is used.

To trigger an on-demand backup of a virtual machine:

- On the [vault item dashboard](backup-azure-manage-vms.md#open-a-vault-item-dashboard), click **Backup now**.

    ![Backup now button](./media/backup-azure-manage-vms/backup-now-button.png)

    The portal makes sure that you want to start an on-demand backup job. Click **Yes** to start the backup job.

    ![Backup now button](./media/backup-azure-manage-vms/backup-now-check.png)

    The backup job creates a new recovery point. The retention range of the recovery point created is the same as that specified in the policy associated with the virtual machine. To track the progress for the job, in the vault dashboard, click the **Backup Jobs** tile.  


## Stop protecting virtual machines
If you choose to stop protecting a virtual machine, you are asked if you want to retain the recovery points. There are two ways to stop protecting virtual machines:
- stop all future backup jobs and delete all recovery points, or
- stop all future backup jobs but leave the recovery points <br/>

There is a cost associated with leaving the recovery points in storage. However, the benefit of leaving the recovery points is you can restore the virtual machine later, if desired. For information about the cost of leaving the recovery points, see the  [pricing details](https://azure.microsoft.com/pricing/details/backup/). If you choose to delete all recovery points, you will no longer have an option for restoring the virtual machine.

To stop protection for a virtual machine:

1. On the [vault item dashboard](backup-azure-manage-vms.md#open-a-vault-item-dashboard), click **Stop backup**.

    ![Stop backup button](./media/backup-azure-manage-vms/stop-backup-button.png)

    The Stop Backup blade opens.

    ![Stop backup blade](./media/backup-azure-manage-vms/stop-backup-blade.png)

2. On the **Stop Backup** blade, choose whether to retain or delete the backup data. The information box provides details about your choice.

    ![Stop protection](./media/backup-azure-manage-vms/retain-or-delete-option.png)

3. If you chose to retain the backup data, skip to step 4. If you chose to delete backup data, confirm that you want to stop the backup jobs and delete the recovery points - type the name of the item.

    ![Stop verification](./media/backup-azure-manage-vms/item-verification-box.png)

    If you aren't sure of the item name, hover over the exclamation mark to view the name. Also, the name of the item is under **Stop Backup** at the top of the blade.

4. Optionally provide a **Reason** or **Comment**.

5. To stop the backup job for the current item, click
    ![Stop backup button](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    A notification message lets you know the backup jobs have been stopped.

    ![Confirm stop protection](./media/backup-azure-manage-vms/stop-message.png)


## Resume protection of a virtual machine
If the **Retain Backup Data** option was chosen when protection for the virtual machine was stopped, then it is possible to resume protection. If the **Delete Backup Data** option was chosen, then protection for the virtual machine cannot resume.

To resume protection for the virtual machine

1. On the [vault item dashboard](backup-azure-manage-vms.md#open-a-vault-item-dashboard), click **Resume backup**.

    ![Resume protection](./media/backup-azure-manage-vms/resume-backup-button.png)

    The Backup Policy blade opens.

    >[AZURE.NOTE] When re-protecting the virtual machine, you can choose a different policy than the policy with which virtual machine was protected initially.

2. Follow the steps in [Change policies or Create a new backup policy](backup-azure-manage-vms.md#change-policies-or-create-a-new-backup-policy), to assign the policy for the virtual machine.

    Once the backup policy is applied to the virtual machine, you will see the following message.

    ![Successfully protected VM](./media/backup-azure-manage-vms/success-message.png)

## Delete Backup data
You can delete the backup data associated with a virtual machine during the **Stop backup** job, or anytime after the backup job has completed. It may even be beneficial to wait days or weeks before deleting the recovery points. Unlike restoring recovery points, when deleting backup data, you cannot choose specific recovery points to delete. If you choose to delete your backup data, you delete all recovery points associated with the item.

The following procedure assumes the Backup job for the virtual machine has been stopped or disabled. Only when the Backup job has been disabled, will the **Resume backup** and **Delete backup** options be available in the vault item dashboard.

![Resume and delete buttons](./media/backup-azure-manage-vms/resume-delete-buttons.png)

To delete backup data on a virtual machine with the *Backup disabled*:

1. On the [vault item dashboard](backup-azure-manage-vms.md#open-a-vault-item-dashboard), click **Delete backup**.

    ![VM Type](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    The **Delete Backup Data** blade opens.

    ![VM Type](./media/backup-azure-manage-vms/delete-backup-blade.png)

2. You must type the name of the item to confirm that you want to delete the recovery points.

    ![Stop verification](./media/backup-azure-manage-vms/item-verification-box.png)

    If you aren't sure of the item name, hover over the exclamation mark to view the name. Also, the name of the item is under **Delete Backup Data** at the top of the blade.

3. Optionally provide a **Reason** or **Comment**.

4. To delete the backup data for the current item, click
    ![Stop backup button](./media/backup-azure-manage-vms/delete-button.png)

    A notification message lets you know the backup data has been deleted.

## Auditing Operations
You can review the operation and event logs to see the management operations performed on the Recovery Services vault. Event logs enable great post-mortem and audit support for the backup operations. The following operations are available logged in Audit logs:

- Register
- Unregister
- Configure protection
- Backup (Both scheduled as well as on-demand backup)
- Restore
- Stop protection
- Delete backup data
- Add policy
- Delete policy
- Update policy
- Cancel job

There are multiple ways to filter and view events. In the vault dashboard the **Backup Alerts** tile shows the counts for events with severity equal to Critical or Warning. Use the **Audit Logs** setting to view any event for any operations in the subscription. You can also use **Audit Logs** setting to customize the view of events for Recovery Services vaults or vault items however you like. The following section, [Alert notifications](backup-azure-manage-vms.md#alert-notifications), provides information on using alerts with Recovery Service vaults. For additional information on events, operations, and audit logs in Azure, see the article, [View events and audit logs](../azure-portal/insights-debugging-with-events.md). 

### Alert notifications

The **Backup Alerts** tile provides shortcuts to filtered views of Critical or Warning-level alerts.
>[AZURE.TIP] You can configure the service to send email notifications when a particular alert occurs, or each hour. For details see Configure event notifications.
To open one of these filtered views:

1. On the **Backup Alerts** tile on the vault dashboard, click **Critical** or **Warning** to view the operation logs filtered to show just those event levels.

    ![Backup Alerts tile](./media/backup-azure-manage-vms/backup-alerts-tile.png)

    The **Backup Alerts** blade opens and displays the filtered alerts.

    ![Backup Alerts tile](./media/backup-azure-manage-vms/backup-alerts-critical.png)

2. To view detailed information about a particular alert, from the list click the alert to open its **Details** blade.

    ![Event Detail](./media/backup-azure-manage-vms/audit-logs-event-detail.png)

    To customize the attributes displayed in the list, see [View additional event attributes](backup-azure-manage-vms.md#view-additional-event-attributes)

### Customize your view of events

1. In the [vault dashboard](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), browse to and click **Audit Logs** to open the **Events** blade.

    ![Audit Logs](./media/backup-azure-manage-vms/audit-logs-1606-1.png)

    The Events blade opens to the operational events filtered just for the current vault.

    ![Audit Logs Filter](./media/backup-azure-manage-vms/audit-logs-filter.png)

    The blade shows the list of Critical, Error, Warning, and Informational events that occurred in the past week. The time span is a default value set in the **Filter** settings. The **Events** blade also shows a bar chart tracking when the events occurred. If you don't want to see the bar chart, in the **Events** menu, click **Show chart** to toggle the chart off. The default view of Events shows Operation, Level, Status, Resource, and Time, information. For information about exposing additional Event attributes, see the section [expanding Event information](backup-azure-manage-vms.md#view-additional-event-attributes).

2. For additional information on a particular operation, from the list of Operations, click that operation to open its blade. The blade contains detailed information about the operation, and a list of the Events that occurred in the Time span.

    ![Operation Details](./media/backup-azure-manage-vms/audit-logs-details-window.png)

3. To view detailed information about a particular event, from the list of events, click the operation to open its Detail blade.

    ![Event Detail](./media/backup-azure-manage-vms/audit-logs-details-window-deep.png)

    The Event-level information is as detailed as the information gets. The remainder of this procedure explains how to edit or alter the available information. If you like seeing this much information about each event, and would like to see this much detail to the **Events** blade, see the section [expanding Event information](backup-azure-manage-vms.md#view-additional-event-attributes).

4. To edit the list of available filters, on the **Events** menu, click **Filter** to open that blade.

    ![open filter blade](./media/backup-azure-manage-vms/audit-logs-filter-button.png)

5. On the **Filter** blade, adjust the **Level**, **Time span**, and **Caller** filters. The other filters are not available since they were set to provide the current information for the Recovery Services vault.

    ![Audit Logs-query details](./media/backup-azure-manage-vms/filter-blade.png)

    You can specify the **Level** of event: Critical, Error, Warning, or Informational. You can choose any combination of event Levels, but you must have at least one Level selected. Toggle the Level on or off. The **Time span** filter allows you to specify the length of time for capturing events. If you use a custom Time span, you can set the start and end times.

6. Once you are ready to query the operations logs using your filter, click **Update**. The results display in the **Events** blade.

    ![Operation Details](./media/backup-azure-manage-vms/edited-list-of-events.png)


### View additional event attributes
Using the **Columns** button, you can enable additional event attributes to appear in the list on the **Events** blade. The default list of events displays information for Operation, Level, Status, Resource, and Time. To enable additional attributes:
1. On the **Events** blade, click **Columns**.

    ![Open Columns](./media/backup-azure-manage-vms/audi-logs-column-button.png)

    The **Choose columns** blade opens.

    ![Columns blade](./media/backup-azure-manage-vms/columns-blade.png)

2. Click the checkbox to select that attribute. The attribute checkbox toggles on and off.

3. Click **Reset** to reset the list of attributes in the **Events** blade. After adding or removing attributes from the list, use **Reset** to view the new list of Event attributes.

4. Click **Update** to update the data in the Event attributes. The following table provides information about each attribute.

| Column name      |Description|
| -----------------|-----------|
| Operation|The name of the operation|
| Level|The level of the operation, values can be: Informational, Warning, Error, or Critical|
|Status|Descriptive state of the operation|
|Resource|URL that identifies the resource; also known as the resource ID|
|Time|Time, measured from the current time, when the event occurred|
|Caller|Who or what called or triggered the event; can be the system, or a user|
|Timestamp|The time when the event was triggered|
|Resource Group|The associated resource group|
|Resource Type|The internal resource type used by Resource Manager|
|Subscription ID|The associated subscription ID|
|Category|Category of the event|
|Correlation ID|Common ID for related events|



## Customized alerts
You can get custom alert notifications for the jobs in the portal. To get these jobs, define PowerShell-based alert rules on the operational logs events. Use *PowerShell version 1.3.0 or later*.

To define a custom notification to alert for backup failures, use a command like this:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/RecoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/RecoveryServicesVault/trinadhVault -Actions $actionEmail
```

**ResourceId** : You can get this information from the Audit logs. The ResourceId is a URL provided in the Resource column of the Operation logs.

**OperationName** : This will be of the format "Microsoft.RecoveryServices/recoveryServicesVault/<EventName>" where EventName can be:
- Register
- Unregister
- ConfigureProtection
- Backup
- Restore
- StopProtection
- DeleteBackupData
- CreateProtectionPolicy
- DeleteProtectionPolicy
- UpdateProtectionPolicy

**Status** : Supported values are- Started, Succeeded, or Failed.

**ResourceGroup** : This is the Resource Group to which the resource belongs. You can add the Resource Group column to the generated logs. Resource Group is one of the available types of event information.

**Name** : Name of the Alert Rule.

**CustomEmail** : Specify the custom email address to which you want to send an alert notification

**SendToServiceOwners** : This option sends alert notifications to all administrators and co-administrators of the subscription. It can be used in **New-AzureRmAlertRuleEmail** cmdlet

### Limitations on Alerts
Event-based alerts are subjected to the following limitations:

1. Alerts are triggered on all virtual machines in the Recovery Services vault. You cannot customize the alert for specific set of virtual machines in a Recovery Services vault.
2. This feature is in Preview. [Learn more](../azure-portal/insights-powershell-samples.md#create-alert-rules)
3. Alerts are sent from "alerts-noreply@mail.windowsazure.com". Currently you can't modify the email sender.

[AZURE.INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## Next steps

For information on re-creating a virtual machine from a recovery point, check out [Restore Azure VMs](backup-azure-restore-vms.md). If you need information on protecting your virtual machines, see [First look: Back up VMs to a Recovery Services vault](backup-azure-vms-first-look-arm.md).
