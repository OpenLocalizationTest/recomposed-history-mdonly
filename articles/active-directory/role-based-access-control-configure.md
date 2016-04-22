<properties
	pageTitle="Get started with access management in the Azure portal | Microsoft Azure"
	description="Get started in access management with Azure role-based access control in the Azure Portal. Use role assignments to assign permissions in your directory."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="04/20/2016"
	ms.author="kgremban"/>

# Get started with access management in the Azure portal

## Role-Based Access Control
Azure Role-Based Access Control (RBAC) enables fine-grained access management for Azure. Using RBAC, you can segregate duties within your DevOps team and grant only the amount of access to users that they need to perform their jobs. This article helps you get up and running with RBAC in the Azure portal.



## Manage access using the Azure portal
### View access
You can see who has access to a resource, resource group, or subscription from its main blade in the [Azure portal](https://portal.azure.com). For example, we want to see who has access to one of our resource groups:

1. Select the **Resource group** icon in the navigation bar on the left.
2. Select the name of the resource group you want to examine from the **Resource groups** blade.
3. Select the **Users** icon on the top right of the resource group blade.
4. The **Users** blade lists all users, groups, and applications that have been granted access to the resource group.

![Users blade - inherited vs assigned access screenshot](./media/role-based-access-control-configure/view-access.png)

Notice that some users were **Assigned** access while others **Inherited** it. Access is either assigned specifically to the resource group or inherited from an assignment to the parent subscription.

> [AZURE.NOTE] Classic subscription admins and co-admins are considered owners of the subscription in the new RBAC model.


### Add Access
You grant access from within the resource, resource group, or subscription that is the scope of the role assignment.

1. Select the **Add** icon on the **Users** blade. ![Add access blade - select a role screenshot](./media/role-based-access-control-configure/grant-access1.png)
2. Select the role that you wish to assign.
3. Select the user, group, or application in your directory that you wish to grant access to. You can search the directory with display names, email addresses, and object identifiers.
![Add users blade - search screenshot](./media/role-based-access-control-configure/grant-access2.png)

### Remove Access

1. In the **Users** blade, select the role assignment that you wish to remove.
2. Click the **Remove** icon in the assignment details blade.
3. Click **yes** to confirm removal.

![Users blade - remove from role screenshot](./media/role-based-access-control-configure/remove-access1.png)

Inherited assignments cannot be removed from child scopes. Go to the parent scope to remove the role assignment.

![Users blade - inherited access disables remove button screenshot](./media/role-based-access-control-configure/remove-access2.png)

## Other tools to manage access
You can assign roles and manage access with Azure RBAC commands in tools other than the Azure portal.  Follow the links to learn more about the prerequisites and get started with the Azure RBAC commands.

- [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure Command-Line Interface](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Next Steps
- [Create an access change history report](role-based-access-control-access-change-history-report.md)
- See the [RBAC built-in roles](role-based-access-built-in-roles.md)
- Define your own [Custom roles in Azure RBAC](role-based-access-control-custom-roles.md)
