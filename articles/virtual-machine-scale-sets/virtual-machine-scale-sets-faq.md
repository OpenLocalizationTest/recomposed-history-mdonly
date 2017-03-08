---
title: Virtual Machine Scale Sets FAQ | Microsoft Docs
description: Get answers to frequently asked questions about Virtual Machine Scale Sets
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/08/2017
ms.author: negat
ms.custom: na

---

# About this article

This article contains frequently asked questions about scale sets.

## Compliance

### Are VM scale sets PCI compliant?

VMSS is a thin API layer on top of the Compute Resource Provider which is all a part of the “Compute Platform” area within the Azure Service Tree.

So, therefore, from a compliance perspective, VMSS is a fundamental part of the Azure Compute Platform and shares the same team, tools, processes, deployment methodology, security controls, JIT, monitoring, alerting, etc. as the Compute Resource Provider (CRP) itself.  VMSS is PCI compliant because Compute Resource Provider is a part of the current PCI DSS attestation:

For more information, See: [https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI).



## VM Properties

### How do I get property information for each VM without having to make multiple calls? E.g. getting the Fault Domain for each VM in my 100 VM scale set?

You can call ListVMInstanceViews by

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### Are there ways to parse unique arguments to extensions in VM Scale Sets?

No, but extensions can take actions based on unique aspects of the VM they are running on such as machine name, and they can query instance metadata on http://169.254.169.254 to get more information.

### Why are there gaps between my VMSS VM machine names and VM IDs? E.g. 0,1,3...

Because your scale set overprovision property is set to the default value of true. This means more VMs than requested are created, and the extra VMs subsequently deleted. What you gain is increased deployment reliability at the expense of contiguous naming, contiguous NAT rules. You can set this property to false, and for small scale sets it won’t make much difference to deployment reliability.

### What is the difference between deleting a VM in a Scale Set, vs. deallocating the VM?
When should I choose one over the other?
A. The main difference is dealloc doesn’t delete the VHDs, so there are storage costs associated with stop dealloc. Reasons you might use one over the other include:

- You want to stop paying Compute but keep the disk state of the VMs.
- You want to start a set of VMs faster than scaling out a scale set.
  - related to this, you created your own autoscale engine and want faster end to end scale.
  - You have a scale set that is unevenly distributed across FD/UDs (due to selectively deleting VMs or due to VMs being deleted after overprovisioning). Stop dealloc followed by start on the scale set will evenly distribute the VMs across FD/UDs.

## Updates

### How to I update my VMScaleSet to a new image? How do I manage patching?

See: https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set

### Image reset
Q. Can you use the “reimage” operation to reset a VM without changing the image (i.e. reset a VM to factory settings rather than to a new image)?
A. Yes. See: https://docs.microsoft.com/en-us/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set

However, if your scale set references a platform image with version = “latest” your VM can update to a later OS image when you call reimage.

## Autoscale

### Any best practices tips for Azure autoscale?

Yes. See https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/insights-autoscale-best-practices

### Where do I find the metric names for autoscaling using host based metrics?

https://azure.microsoft.com/en-us/documentation/articles/monitoring-supported-metrics/

### Are there any examples of autoscaling off of a service bus topic and queue length?

Yes. See:

https://azure.microsoft.com/en-us/documentation/articles/insights-autoscale-common-metrics/

for service bus queue:

"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"

For storage queues use this format:

"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"

replace these with the appropriate resource names.


### Should we autoscale with host based metrics or use a diagnostics extension?

You can create an autoscale setting on a VM to use host-level metrics, or use guest-OS based metrics.

See this list of supported metrics: https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics. Here is a full sample for VMSS (in this case we used the host-level CPU metric and a message count metric)

https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets

### How can I set alert rules on a VM scale set?

You can create alerts on metrics on VMSS via PS or CLI. See:

https://azure.microsoft.com/en-us/documentation/articles/insights-powershell-samples/#create-alert-rules

https://azure.microsoft.com/en-us/documentation/articles/insights-cli-samples/#work-with-alerts

the TargetResourceId of the VMSS will look like: /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

You can choose any VM perf counter as the metric to alert on:

https://azure.microsoft.com/en-us/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-windows-vm-v2-as-a-guest-os

https://azure.microsoft.com/en-us/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-linux-vm-v2-as-a-guest-os

### How can I set up autoscale on a VM Scale Set using PowerShell?

See https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/

## Scale

### Why would you ever not create at least a two-VM scale set?

One reason to not create a two-VM VMSS would be to make use of the elastic properties of a scale set, for example to deploy a VMSS with zero VMs, where you’ve defined your infrastructure without paying VM running costs, and are ready to deploy by increasing the “capacity” of the VMSS to the production instance count.

Another reason is when you’re doing something with your scale set where you don’t care about availability in the same sense as using an availability set with discrete VMs. VM Scale Sets add a way to work with undifferentiated compute units which are fungible. This is a key differentiator for scale sets vs. availability sets. Many stateless workloads do not care about individual units, and can scale down to one compute unit if the workload drops, and back to many when it increases.

### How do you change the number of VMs in a scale set?

See: https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/

### How can you define custom alerts for when certain thresholds are reached?

You have some flexibility how you handle alerts, for example you can define customized webhooks like this..

   {
         "type": "Microsoft.Insights/autoscaleSettings",
	       "apiVersion": "[variables('insightsApi')]",
	             "name": "autoscale",
		           "location": "[parameters('resourceLocation')]",
			         "dependsOn": [
				         "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
					       ],
					             "properties": {
						             "name": "autoscale",
							             "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
								             "enabled": true,
									             "notifications": [
										               {
											                   "operation": "Scale",
													               "email": {
														                     "sendToSubscriptionAdministrator": true,
																                   "sendToSubscriptionCoAdministrators": true,
																		                 "customEmails": [
																				                 "youremail@address.com"
																						               ]
																							                   },
																									               "webhooks": [
																										                     {
																												                     "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
																														                     "properties": {
																																                       "key1": "custommetric",
																																		                         "key2": "scalevmss"
																																					                 }
																																							               }
																																								                   ]
																																										             }
																																											             ],

In this example, it goes to Pagerduty when a threshold is reached.


## Certificates

### How do you securely ship a certificate into the VM?  Is there an example of provisioning a VMSS to run a website where the SSL for the website is shipped securely from a certificate configuration?  The common certificate rotation operation would amount to a configuration update operation.

We support installing customer certificates directly into Windows certificate store from their key vault.

In the context of scale sets...

https://msdn.microsoft.com/en-us/library/mt589035.aspx

        "secrets": [ {
	          "sourceVault": {
		              "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
			                }
					          "vaultCertificates": [ {
						              "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
							                  "certificateStore": "certificateStoreName",
									            } ]
										            } ],

It supports both windows and Linux.

### Self signed certificate example:

#### Create a self-signed Cert in a KeyVault

One way to create a self-signed cert in a KeyVault is to use the instructions from this Service Fabric article here: https://azure.microsoft.com/en-us/documentation/articles/service-fabric-cluster-security/

The commands:

Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

Login-AzureRmAccount

Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\

The above command gives you the input for the ARM template.

#### Change ARM Template

Add this property to the "virtualMachineProfile” as part of the VMSS Resource:
 
"osProfile": {
            "computerNamePrefix": "[variables('namingInfix')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]",
            "secrets": [
              {
                "sourceVault": {
                  "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                  {
                    "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                    "certificateStore": "My"
                  }
                ]
              }
            ]
          },
 

### Is there a way to specify an SSH keypair that I want to use for SSH authentication with a Linux VMSS from an ARM template?  
A. The REST API for the osProfile looks similar to the ordinary VM case:
 
https://msdn.microsoft.com/en-us/library/azure/mt589035.aspx#linuxconfiguration
 
You should try something like the following:
 
"osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUserName')]",
          "linuxConfiguration": {
            "disablePasswordAuthentication": "true",
            "ssh": {
              "publicKeys": [
                {
                  "path": "[variables('sshKeyPath')]",
                  "keyData": "[parameters('sshKeyData')]"
                }
              ]
            }
          }
        },
 
This is contained in the following:
 
https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
Also look at the OS Profile on this template:
 
https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json

### How do I remove deprecated certificates? 

You must remove the old certificate from the vault certificates list (but leave all of the certificates that you want to remain on your machine. This will not remove the certificate from all your VMs, but it will not add the certificate to new VMs that are created in the scale set. To remove the certificate from existing VMs you must write a custom script extension which removes the certificates from your certificate store manually.
 
### How do I take an existing SSH public key and inject it into the VMSS SSH layer during provisioning?  I would like to store the SSH Public Key values in Azure Key Vault, and then utilize them in my ARM template.

If you are only providing the VMs with the public ssh key, there is no reason to put the public keys in the key vault (as a public key is not secret)
 
You can provide SSH public keys in plain text when you create a Linux VM.
An example can be found here:

https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
The documentation is found here:
Specifically:
"linuxConfiguration": {  
          "ssh": {  
            "publicKeys": [ {  
              "path": "path",
              "keyData": "publickey"
            } ]
          },
 
linuxConfiguration
Element |  name | Required | Type | Description
--- | --- | --- | --- |  ---
ssh | No | Collection | Specifies the ssh key configuration for a Linux OS.
path | Yes | String | Specifies the Linux file path that the ssh keys or certificate should be placed at.
keyData | Yes | String | Specifies a base64 encoded ssh public key.
 
### When I run Update-AzureRmVmss after more than one certificate from the same KeyVault, I get the following error:
 
Update-AzureRmVmss : List secrets contains repeated instances of /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, which is disallowed.
Why can’t I add two certificates from the same KeyVault?
 
This can happen if you're trying to add the same vault twice instead of a new vaultCertificate for the existing sourceVault. The Add-AzureRmVmssSecret does not work correctly for adding additional secrets.
 
If you want to add more secrets from the same key vault you should update the list $vmss.properties.osProfile.secrets[0].vaultCertificates
 
You can see the expected input structure here:
https://msdn.microsoft.com/en-us/library/azure/mt589035.aspx
 
You will need to find the secret in the VMSS object that has the same containing key vault, and add your certificate reference (the URL along with the secret store name) into the list associated with the vault.

Note: removing certificates from VMs through the VMSS APIs is not currently supported.
 
New VMs will not have the old cert, but ones that had the cert already deployed will still have the old certificate.
 
### is there a way to get certificates pushed on to the VMSS without providing the password when the certificate is in SecretStore currently?

You do not need to hardcode passwords in scripts, you can dynamically retrieve them with whatever permissions the deployment script you have runs with. If you have a script that is moving a cert from secret store the key vault, the secret store get certificate command also outputs the password of the pfx file.
 
### How does the secrets property of virtualMachineProfile.osProfile of a VMSS work? Why do you need sourceVault when you have to specify the absolute URI to a certificate with certificateUrl? 

A Win RM certificate reference must be present in the secrets property of the OS profile. 

The purpose of indicating the source vault is to be able to enforce ACL policies that exist in CSM. Without specifying this, users who do not have permissions to deploy/access secrets to a key vault would be able to through CRP. The ACLs exist even for resources that do not exist.

If you provided an incorrect sourceVault id but a valid key vault URL we would report an error when you poll the operation
 
### If I add secrets to an existing VMSS, does it inject them in existing instances, or only new ones? 

Certificates get added to all the VMs, even preexisting ones. If your scale set upgradePolicy property is set to “Manual” the certificate is added to the VM when you perform a manual update on the VM.
 
### Where do certificates go for Linux VMs?

See https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/
  
### How do you add a new vault certificate to a new certificate object?

If you want add a vault certificate to existing secret, which should be the only one secret object, you can do it like this:
 
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
 
### What happens to certificates if you reimage a VM?

If you reimage a VM the certificates will disappear, reimaging deletes the OS disk in its entirety. 
 
### What happens if you delete a certificate from the key vault?

If the secret is deleted in the key vault, and you stop deallocate all your VMs and start them again, you will encounter a failure due to CRP needing to retrieve the secrets from Key Vault, but not being able to. In this scenario, you can delete the certificates from the VM Scale Set model. 

The CRP component does not persist any customer secrets. If you stop deallocate all VMs in the scale set, then the cache is deleted. In this scenario, secrets are retrieved from key vault.

This issue is not hit on scale out because there is a cached copy of the secret in fabric (at least in the single fabric tenant model).
 
### Why do we have to specify the exact location for the Certificate URL, as referenced here: per https://azure.microsoft.com/en-us/documentation/articles/service-fabric-cluster-security/, 
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
 
Per KeyVault documentation, the get-secret REST API should return the latest version of the secret if version is not specified:
 
Method | URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Replace {secret-name} with the name and {secret-version} with the version of the secret you want to retrieve. Secret version may be excluded in which case the current version is retrieved.
  
### Why does the version have to be specified?

The reason for this is to make it clear to the user what certificate is deployed on their VMs.

If a user creates a VM, then updates their secret in the key vault that new certificate will not be downloaded to their VMs. But their VMs will appear to reference it, and new VMs will get the new secret. To avoid this confusion, it is required that users reference an explicit secret version.

### My team works with several certificates which are distributed to us as .cer public keys. What is the recommended approach is for deployment of these certs to a VMSS?

You can generate a pfx file that only contains .cer files, with X509ContentType = Pfx. E.g. load the .cer file as an x509Certificate2 object in C# or PowerShell and calling this method: https://msdn.microsoft.com/en-us/library/24ww6yzk(v=vs.110).aspx

### I do not see an option for users to pass in certificates as base64 strings that most other resource providers provide.

Users can extract the latest versioned URL within an ARM template emulate the behavior you describe. They can include the following in their ARM template
 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
   
 
### Do we have to wrap certs in JSON objects in keyvaults?

This is a VMSS/VM requirement. We do also support the content type application/x-pkcs12. Instructions found here:
http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/
 
We currently do not support .cer files, you must export your .cer files into pfx containers.

## Extensions

### How do you delete a VMSS extension?

E.g. with PowerShell..

$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 
$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"
Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
 
The extensionName could be found in $vmss.
   
### IS there a VMSS template example that integrates with OMS?

Look at the second example here:

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric
   
### Extensions seem to run in parallel on VM Scale Sets, causing my Custom Script extension to fail. What can I do about this?

See https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/ 
 
 
### How do I reset the password for VMSS VMs?

Use VM Access Extensions

E.g.using PowerShell:

$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName  VirtualMachineScaleSet $vmss
 
 
### How do I add an extension to all VMs in my scale set?
 - If update policy is set to automatic redeploying the template with the new extension properties will update every VM.
- If update policy is set to manual, update the extension with manual mode and manualUpdate on all instances. 
  
### If the extensions associated with an existing VMSS are updated, would they affect already existing VMs? (i.e. would the VMs show up as not matching the VMSS model)? Or would they be ignored? When an existing machine is service-healed / re-imaged / etc. would the scripts that are currently configured on the VMSS be executed or would the ones that were configured when the machine was first created be used?

- If the extension definition in the VMSS model is updated, it would update the VMs if upgradePolicy was set to automatic, and they would be flagged as not matching the model if upgradePolicy is set to manual. 

- If an existing VM is service healed it would appear like a reboot and the extensions would not re-run. If it is reimaged it would be like replacing the OS drive with the source image and any specialization from the latest model, such as extensions would run.
 
### How do I get a VMSS to join an AD Domain?

You could define an extension like this using the JsonADDomainExtension for example:
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "joindomain",
                                "properties": {
                                    "publisher": "Microsoft.Compute",
                                    "type": "JsonADDomainExtension",
                                    "typeHandlerVersion": "1.0",
                                    "settings": {
                                        "Name": "[parameters('domainName')]",
                                        "OUPath": "[variables('ouPath')]",
                                        "User": "[variables('domainAndUsername')]",
                                        "Restart": "true",
                                        "Options": "[variables('domainJoinOptions')]"
                                    },
                                    "protectedsettings": {
                                        "Password": "[parameters('domainJoinPassword')]"
                                    }
                                }
                            }
                        ]
                    }

 
### My VMSS extension is trying to install something that requires a reboot, e.g. 
"commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools"

You could use the the DSC extension. If the OS is 2012 R2, then ARM will pull in the WMF5.0 setup, reboot, and continue with the configuration. 
 
### How can I enable Antimalware on my VMSS?

Here's a PowerShell example:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# retrieve the most recent version number of the extension
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### I need to execute a custom script hosted on a private storage account. I have no problems when the storage is public but when I try to use a Shared Access Signature(SAS) it fails with the error: “Missing mandatory parameters for valid Shared Access Signature”. I know that link+SAS works just fine from my local browser.

You must setup protected settings with the storage account key and name for this to work.
See https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings


## Networking
 
### How do I do VIP swap for VM Scale Sets in the same subscription and same region?

See: https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/ 
 
  
### What’s the resourceGuid property on a NIC for? It’s not documented here.

It’s a unique ID. Lower layers will be logging this ID at some point in the future. 
 
### How do I specify a range of private IP addresses, for static private IP address allocation?

IPs are selected from a subnet that you specify. 

The allocation method of VMSS IPs is always “Dynamic”. It does not mean though that these IPs can change. It only means that you do not specify IP in PUT request. In other words, you specify the static set using the subnet. 
    
### How do I deploy a VMSS into an existing VNET? 

See https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet 
 
   
## Troubleshooting

### How do I enable boot diagnostics?

Create a storage account and put this in your scale set virtualMachineProfile and update the scale set:
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "http://yourstorageaccount.blob.core.windows.net"
        }
      }
Then when a new VM is created the InstanceView of the VM shows the details for the screenshot etc. E.g.
 
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  },
 
  

 


