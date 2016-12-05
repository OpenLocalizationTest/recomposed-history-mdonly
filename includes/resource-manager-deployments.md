## Incremental and complete deployments
When deploying your resources, you specify that the deployment is either an incremental update or a complete update. By default, Resource Manager handles deployments as incremental updates to the resource group. With incremental deployment, Resource Manager:

* **leaves unchanged** resources that exist in the resource group but are not specified in the template
* **adds** resources that are specified in the template but do not exist in the resource group 
* **does not reprovision** resources that exist in the resource group in the same condition defined in the template
* **reprovisions** existing resources that have updated settings in the template

With complete deployment, Resource Manager:

* **deletes** resources that exist in the resource group but are not specified in the template
* **adds** resources that are specified in the template but do not exist in the resource group 
* **does not reprovision** resources that exist in the resource group in the same condition defined in the template
* **reprovisions** existing resources that have updated settings in the template

You specify the type of deployment through the **Mode** property.

