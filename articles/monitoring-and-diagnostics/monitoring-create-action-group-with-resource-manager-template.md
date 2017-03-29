# Create an action group with a Resource Manager Template #
This article shows how you can use an [Azure Resource Manager template](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) to configure action groups. This enables you to automatically set up action groups on your resources when they are created to ensure that all the correct parties are notified for resource updates.

>[!NOTE]
>This feature is currently in public preview. Not all functionality may be available at this time.
>
>

The basic steps are as follows:

1.	Create a template as a JSON file that describes how to create the action group.
2.	[Deploy the template using any deployment method.](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy)

Below we describe how to create a Resource Manager template first for an action group alone, then for an action group during the creation of another resource.

## Resource Manager template for an action group ##

To create an action group using a Resource Manager template, you create a resource of type `microsoft.insights/actiongroups` and fill in all related properties. Below is a template that creates an action group.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location":{
        "type":"string"
    },
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actiongroups",
      "apiVersion": "2017-03-01-preview",
      "name": "[parameters('actionGroupName')]",
      "location": "[parameters('location')]",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": "True",
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## Next Steps ##
Learn more about [Action Groups](monitoring-action-groups.md)  
Learn more about [Alerts](monitoring-overview-alerts.md)  
How to add [Alerts using a Resource Manager template](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
