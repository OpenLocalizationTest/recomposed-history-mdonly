---
title: Error handling in Azure Automation Graphical Runbooks | Microsoft Docs
description: This article describes how to implement error handling logic in Azure Automation graphical runbooks.  
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: tysonn

ms.assetid:
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
---

# Error handling in Azure Automation graphical runbooks

A key runbook design principal that you need to consider is to identifying different issues that a runbook might experience such. These can include success, expected error states, and unexpected error conditions.

Runbooks should include error handling to detect errors accordingly.  With graphical runbooks, if you want to validate the output of an activity or handle an error in an appropriate manner, you could use a Windows PowerShell code activity, define conditional logic on the output link of the activity, or other apply another method.          

Often when your runbooks execute, if there is a non-terminating error that occurs with an activity, any activity that follows it will be processed regardless of the error. It's likely to generate an exception, but the point is that the next activity is allowed to run. The reason for this is the way that the PowerShell language is designed to handle errors.    

The types of PowerShell errors that can occur during execution are terminating or non-terminating. The differences between terminating and non-terminating errors are as follows:

* **Terminating error**: A serious error during execution that halts the command (or script execution) completely. Examples can include non-existent cmdlets, syntax errors that would prevent a cmdlet from running, or other fatal errors.

* **Non-terminating error**: A non-serious error that allows execution to continue despite the failure. Examples include operational errors such file not found, permissions problems, and so on.

Azure Automation graphical runbooks have been improved with the capability to include error handling, which means that you can now turn exceptions into non-terminating errors and create error links between activities. This allows a runbook author to catch errors and more easily manage the realized or unexpected condition.  

## When to use error handling

Having control of your workflow execution is important for ensuring that whenever there is a critical activity that throws an error or exception, you can prevent the next activity in your runbook from processing and handle the error appropriately. This is especially necessary when your runbooks are supporting a business or service operations process.

For each activity that can produce an error, the runbook author can add an error link pointing to any other activity.  The destination activity can be of any type, including code activities, invoking a cmdlet, invoking another runbook, and so on.

In addition, the destination activity can also have outgoing links. These can be regular links or error links, so the runbook author can implement complex error handling logic without resorting to a code activity.  While the recommended practice is to create a dedicated error handling runbook with common functionality, it is not mandatory and error handling logic in a PowerShell code activity it isn't the only alternative.  

As an example, consider a runbook that tries to start a VM and install an application on it. If the VM doesn't start correctly, it performs two actions:

1. It sends a notification about this problem.
2. It starts another runbook that automatically provisions a new VM instead.

One solution is to have an error link pointing to an activity to handle step 1. For example, you could have the **Write-Warning** cmdlet, connected to an activity for step 2, such as **Start-AzureRmAutomationRunbook** cmdlet.

You could also generalize this behavior for use in many runbooks by putting these two activities in a separate error handling runbook and following the guidance suggested earlier. Before calling this error-handling runbook, you could construct a custom message from the data in the original runbook, and then pass this as a parameter to the error-handling runbook.

## How to use error handling

Each activity has a configuration setting that turns exceptions into non-terminating errors. By default, this setting is disabled. We recommend that you enable this setting on any activity where you want to handle errors.  

By enabling his configuration, you are assuring that both terminating and non-terminating errors in the activity are handled as non-terminating errors, and can be handled with an error link.  

After configuring this setting, you create an activity that handles the error. If an activity produces any error, then the outgoing error links will be followed, and the regular links will not, even if an the activity produced regular output as well.<br><br> ![Automation runbook Error Link Exmaple](media/automation-runbook-graphical-error-handling/error-link-example.png)

In the following example, we have a runbook that retrieves a variable that contains the computer name of a virtual machine, and then attempts to start the virtual machine with the next activity.<br><br> ![Automation runbook error handling example](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

The **Get-AutomationVariable** activity and **Start-AzureRmVm** are configured to convert exceptions to errors.  If there are problems getting the variable or starting the VM, then errors are generated.<br><br> ![Automation runbook error handling activity settings](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Error links flow from these activities to a single **error management** activity (a code activity). This activity is configured with a simple PowerShell expression that uses the *Throw* keyword to stop processing, along with *$Error.Exception.Message* to get the message that describes the current exception.<br><br> ![Automation Runbook Error Handling Code Example](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## Next steps

* To learn more about links and link types in graphical runbooks, see [Graphical authoring in Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow).

* To learn more about runbook execution, how to monitor runbook jobs, and other technical details, see [Track a runbook job](automation-runbook-execution.md).
