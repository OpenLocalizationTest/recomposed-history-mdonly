# Azure AD Connect sync: Handling LargeObject errors caused by userCertificate attribute

Azure AD enforces a maximum limit of **15** certificate values on the **userCertificate** attribute. If Azure AD Connect exports to Azure AD an object with more than 15 values, Azure AD will return a **LargeObject** error with message *"The provisioned object is too large. Trim the number of attribute values on this object. The operation will be retried in the next synchronization cycle..."*

The LargeObject error may be caused by other AD attributes. To confirm it is indeed caused by the userCertificate attribute, you need to verify against the object in on-premises AD or in the [Synchronization Service Manager Metaverse Search](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

To obtain the list of objects in your tenant with LargeObject errors, use one of the following methods:

 * If your tenant is enabled for Azure AD Connect Health for sync, you can refer to the [Synchronization Error Report](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync#object-level-synchronization-error-report-preview) provided.
 
 * The notification email for directory synchronization errors that is sent at the end of each sync cycle has the list of objects with LargeObject errors.
 
 * The [Synchronization Service Manager Operations tab](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) displays the list of objects with LargeObject errors if you click on the latest Export to Azure AD operation.
 
## Mitigation options
Until the LargeObject error is resolved, other attribute changes to the same object will not be exported to Azure AD. To resolve the error, you can consider the following options:

 * Implement an **outbound sync rule** in Azure AD Connect that will export a **null** value instead of the actual values for objects with more than 15 certificate values**. This option is suitable if you do not require any of the certificate values to be exported to Azure AD for objects with more than 15 values. For details on how to implement this sync rule, refer to next section [Implementing sync rule to limit export of userCertificate attribute](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Reduce the number of certificate values on the on-premises AD object to 15 or less by removing values that are no longer in use by your organization. This is suitable if the attribute bloat is caused by expired or unused certificates. You can use the [PowerShell script available here](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) to help find, backup and delete expired certificates in your on-premises AD. Before deleting the certificates, it is recommended that you verify with the validity of the certificate values with the Public-Key-Infrastructure administrators in your organization.

 * Configure Azure AD Connect to exclude the userCertificate attribute from being exported to Azure AD. In general, we do not recommend this option since the attribute may be used by Microsoft Online Services to enable specific scenarios. In particular:

    * The userCertificate attribute on the User object is used by Exchange Online and Outlook clients for message signing and encryption. To learn more about this feature, please refer to article [MIME for message signing and encryption](https://technet.microsoft.com/en-us/library/dn626158(v=exchg.150).aspx).

    * The userCertificate attribute on the Computer object is used by Azure AD to allow Windows 10 domain-joined devices to connect to Azure AD. To learn more about this feature, please refer to article [Connect domain-joined devices to Azure AD for Windows 10 experiences](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## Implementing sync rule to limit export of userCertificate attribute
To avoid the LargeObject error caused by the userCertificate attribute, you can implement an outbound sync rule in Azure AD Connect that will export a **null value instead of the actual values for objects with more than 15 certificate values**. This section describes the steps required to implement the sync rule for **User** objects. The steps can be adapted for **Contact** and **Computer** objects.

> [!IMPORTANT]
> Exporting null value will remove certificate values previously exported successfully to Azure AD.

The steps can be summarized as:

  1. Disable sync scheduler.

  2. Verify there is no synchronization in progress.

  3. Find the existing outbound sync rule for userCertificate attribute.

  4. Create the outbound sync rule required.

  5. Verify the new sync rule on an existing object with LargeObject error.

  6. Apply the new sync rule to remaining objects with LargeObject error.

  7. Verify there are no unexpected changes waiting to be exported to Azure AD.

  8. Export the changes to Azure AD.

  9. Re-enable sync scheduler.

### Step 1.	Disable sync scheduler
Ensure no synchronization takes place while you are in the middle of implementing a new sync rule to avoid unintended changes being exported to Azure AD. To disable the built-in sync scheduler:


1. Start PowerShell session on the Azure AD Connect server.
 
  2. Disable scheduled synchronization by running cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`

  3. Verify scheduled synchronization is indeed disabled by running cmdlet `Get-ADSyncScheduler` and checking the returned SyncCycleEnabled attribute is false.

> [!Note]
> The steps above are only applicable to newer versions (1.1.xxx.x) of Azure AD Connect with the built-in scheduler. If you are using older versions (1.0.xxx.x) of Azure AD Connect that leverages Windows Task Scheduler, or you are using your own custom scheduler (not common) to trigger periodic synchronization, you need to disable them accordingly.

### Step 2.	Verify there is no synchronization in progress
Verify there is no synchronization is in progress which has been started before the sync scheduler was disabled:

  1. Start the **Synchronization Service Manager** by going to START → Synchronization Service.

  2. Go to the Operations tab. Look through the most recent operations to make sure there is no operation whose status is *“in progress”*.

### Step 3.	Find the existing outbound sync rule for userCertificate attribute
There should be an existing sync rule that is configured to export userCertificate attribute for User objects to Azure AD. Locate this sync rule to find out its precedence and scoping filter configuration. These will be used as input for creating the new sync rule.

> [!Note]
> The new sync rule to be created must have the same **scoping filter** and **higher precedence** than the existing sync rule. Having the same scoping filter ensures that the new sync rule is applied to the same set of objects currently handled by the existing sync rule. Having a higher precedence allows that the new sync rule to override the existing sync rule for exporting the userCertificate attribute.

  1. Start the **Synchronization Rules Editor** by going to START → Synchronization Rules Editor.

  2. Configure the search filters with the following values:
  
      * Direction = **Outbound**
      * MV Object Type = **person**
      * Connector = ***name of your Azure AD connector***
      * onnector Object Type = **user**
      * MV attribute = **userCertificate**
      3. If you are using OOB (out-of-box) sync rules to Azure AD connector to export userCertficiate attribute for User objects, you should get back the *“Out to AAD – User ExchangeOnline”* rule. 

  4. Note down the **precedence** value of this sync rule.
  
  5. Select the sync rule and click **Edit**.
  
  6. In the *“Edit Reserved Rule Confirmation”* pop-up dialog, click **No**. (Don’t worry, we are not going to make any change to this sync rule).
  
  7. In the edit screen, select the **Scoping filter** tab.
  
  8. Note down the scoping filter configuration. If you are using the OOB sync rule, there should exactly be one scoping filter group containing two rules, including:
      * *“sourceObjectType” EQUAL “User”*
      * *“cloudMastered” NOTEQUAL “True”*

### Step 4. Create the outbound sync rule required
To create the outbound sync rule that will export a null value instead of the actual values for objects with more than 15 certificate values:

  1. In the Synchronization Rules Editor, click Add new rule button.

  2. Under **Description tab**, provide the following configuration:
      * Name = *Provide a name, e.g., “Out to AAD – Custom override for userCertificate”*
      * Description = *Provide a description, e.g., “If userCertificate attribute has more than 15 values, export NULL.”*
      * Connected System = *Select the Azure AD Connector*.
      * Connected System Object Type = **user**
      * Metaverse Object Type = **person**
      * Link Type = **Join**
      * Precedence = *Select a value which meets all the following criteria:*
        * It must be between 1 and 99.
        * It must not be used by any existing sync rules or you will not be able to create the rule.
        * It must have higher precedence that the existing sync rule. The lower the value, the higher the precedence.

  3. Click on **Scoping filter** tab:
      1. Click Add group.
      2. Implement the same scoping filter as the existing sync rule. If the existing sync rule is the OOB sync rule “Out to AAD – User ExchangeOnline”, there should be one group with two rules:
        * “sourceObjectType” EQUAL “User”
        * “cloudMastered” NOTEQUAL “True”.

  4. Skip the **Join rules** tab.
  
  5. Click on **Transformations** tab to add a new transformation using following configuration:
    * Flow Type = **Expression**
    * Target Attribute = **userCertificate**
    * Source Attribute = *Use the following expression*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))`
    
    
    






### Step 5.	Verify the new sync rule on an existing object with LargeObject error
### Step 6.	Apply the new sync rule to remaining objects with LargeObject error
### Step 7.	Verify there are no unexpected changes waiting to be exported to Azure AD
### Step 8.	Export the changes to Azure AD
### Step 9.	Re-enable sync scheduler


