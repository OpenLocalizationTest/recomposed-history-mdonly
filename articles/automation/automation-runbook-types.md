<properties 
   pageTitle="Azure Automation Runbook Types"
   description="Describes the difference types of runbooks that you can use in Azure Automation and considerations that you should take into account when determining which type to use. "
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2015"
   ms.author="bwren" />

# Azure Automation runbook types

Azure Automation supports three types of runbooks that are  briefly described in the following table.  The sections below provide further information about each type including considerations on when to use each.


| Type |  Description |
|:---|:---|
| [Graph](#graph-runbooks) | Based on Windows PowerShell Workflow and created and edited completely in graphical editor in Azure portal. | 
| [PowerShell Workflow](powershell-workflow-runbooks) | Text runbook based on Windows PowerShell Workflow. |
| [PowerShell Script](powershell-script-runbooks) | Text runbook based on Windows PowerShell script. |

## Graph runbooks

[Graph runbooks](automation-runbook-types.md#graph-runbooks) are created and edited with the graphical editor in the Azure portal.  You can export them to a file and then import them into another automation account, but you cannot create or edit them with another tool.  Graph runbooks generate PowerShell Workflow code, but you can't directly view or modify the code. Graph runbooks cannot be converted to one of the [text formats](automation-runbook-types.md), nor can a text runbook be converted to graph format.

### Advantages

- Create runbooks with minimal knowledge of [PowerShell Workflow](automation-powershell-workflow.md).
- Use [checkpoints](automation-powershell-workflow.md#checkpoints) to resume runbook in case of error.

### Limitations

- Can't edit runbook outside of Azure portal.
- Can't view or directly edit the PowerShell Workflow code.
- Can't use [parallel processing](automation-powershell-workflow.md#parallel_processing) with graphical activities.
- May require a [Workflow Script Control](automation-powershell-workflow.md#activities) containing PowerShell Workflow code to perform complex logic.
- Runbook takes longer to start than PowerShell Script runbooks since it needs to be compiled before running.

## PowerShell Workflow runbooks

PowerShell Workflow runbooks are text runbooks based on [Windows PowerShell Workflow](automation-powershell-workflow.md).  You directly edit the code of the runbook using the text editor in the Azure portal.  You can also use any offline text editor and [import the runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) into Azure Automation.

### Advantages

- Implement all complex logic with PowerShell Workflow code.
- Use [checkpoints](automation-powershell-workflow.md#checkpoints) to resume runbook in case of error.
- Use [parallel processing](automation-powershell-workflow.md#parallel_processing) to perform multiple actions in parallel.

### Limitations

- Author must be familiar with PowerShell Workflow.
- Runbook must deal with the additional complexity of PowerShell Workflow such as [deserialized objects](automation-powershell-workflow.md#code-changes).
- Runbook takes longer to start than PowerShell Script runbooks since it needs to be compiled before running.

## PowerShell Script runbooks

PowerShell Script runbooks are based on Windows PowerShell.  You directly edit the code of the runbook using the text editor in the Azure portal.  You can also use any offline text editor and [import the runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) into Azure Automation.

### Advantages

- Implement all complex logic with PowerShell code without the additional complexities of PowerShell Workflow. 
- Runbook starts faster than Graph or PowerShell Workflow runbooks since it doesn't need to be compiled before running.

### Limitations

- Must be familiar with PowerShell scripting.
- Can't use [parallel processing].
- Can't use [checkpoints](automation-powershell-workflow.md#checkpoints) to resume runbook in case of error.
- Can't run runbooks on Hybrid Runbook Worker.


## Considerations

You should take into account the following additional considerations when determining which type to use for a particular runbook.

- You can't convert runbooks from one type to another.
- You can't use a PowerShell Workflow runbook or a Graph runbook as a [child](automation-child-runbooks) in a PowerShell script runbook.  Similarly, you can't use a PowerShell script runbook as a child in a PowerShell Workflow runbook or a Graph runbook.  PowerShell Script runbooks can only use another PowerShell Script as a child.  Graph and PowerShell Workflow runbooks can use each other as child runbooks. 
- [Hybrid Runbook Workers](automation-hybrid-runbook-worker.md) cannot run PowerShell Script runbooks.  You must use Graph or PowerShell Workflow runbooks.
  
## Related articles

- [Graphical authoring in Azure Automation](automation-graphical-authoring-intro.md)
- [Learning Windows PowerShell Workflow](automation-powershell-workflow.md)
- [Creating or Importing a Runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx)


