---
title: 'Azure AD Connect sync:  Understanding the Azure AD Sync Encryption Key | Microsoft Docs'
description: This topic document describes the encryption key and how to abandon it after the password is changed.
services: active-directory
keywords: Azure AD sync service account, password
documentationcenter: ''
author: cychua
manager: femila
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: billmath

---
# Azure AD Sync Encryption Key

Azure AD Connect, as part of the Synchronization Services uses an encryption key to store the passwords of the AD DS and Azure AD service accounts.  These accounts are encrypted before they are stored in the database. 

The encryption key used is secured using [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI protects the encryption key using the **password of the Azure AD Connect sync service account**. 

## Azure ADSync Key Management Utility

![Azure AD Connect Sync Encryption Key Utility](media/active-directory-aadconnectsync-encryption-key/key1.png)

You can use the miiskmu.exe utility located in **Program Files\Microsoft Azure AD Sync\Bin** to do the following:

- **Export Key Set** - allows you to export and backup the keys used to encrypt data in Azure AD Connect to file.
- **Add new key to key set** -  allows you to create a new encryption key and add it to the Azure AD Connect keyset.  The new data will be encrypted using this key.  Existing data will continue to be encrypted with the old keys.
- **Abandon key set** - allows you to delete all encrypted data.  

### Command-line options
Miiskmu.exe can be run from a command-line using the following:

#### Syntax
**miiskmu [/eFileName/u:UserName {Password | *} [/q]] | [/bBatchSize] **

|Parameter|Description|
| ----- | ----- |
|**/e**|Exports the key set to a file.| 
|**FileName**|Specifies the file name, including the path.| 
|**/u:**|Specifies the Microsoft Forefront Identity Manager 2010 R2 service account credentials.| 
|**UserName**|The Azure AD Connect service account name.|
|**Password**|Specifies the password for the Azure AD Connect service account. Use * to prompt for the password.| 
|**/q**|Specifies quiet mode| 
|**/b**|Processes the objects in batches. If this parameter is not specified, all objects will be processed in one batch.| 
|**BatchSize**|The number of objects to process in each batch, specified as an integer.| 
|**/a**|Used to abandon the key.|
|**/?**|Displays help at the command prompt.|

![Azure AD Connect Sync Encryption Key Utility](media/active-directory-aadconnectsync-encryption-key/key4.png)

 
#### Remarks


- Miiskmu.exe is located in the **Program Files\Microsoft Azure AD Sync\Bin** folder. 
- Local Administrator privileges are required to run Miiskmu.exe. 
- Encryption keys are only accessible by the Azure AD Connect service account, so you must specify the Azure AD Connect service account credentials. 
- If you modify the encryption keys (that is, create new ones), it is strongly recommended that you update your backup copy of the encryption keys. 
- The current key set ID can be found by typing miiskmu /?. 
- If you are calling Miiskmu.exe from a batch file, prefix the command with cmd /c to have the ERRORLEVEL set to the success/error code of the operation. 

## Changing the Azure AD Connect sync service account password
If you change the  Azure AD Connect sync dservice account password, the Synchronization Service can no longer start correctly.  You may see the following:

To resolve the issue you must abandon the encryption key.  You cannot simply change the password.  To do this, follow the procedures in [Abandoning the Azure AD Connect Sync encryption key](#abandoning-the-azure-ad-connect-sync-encryption-key)

## Abandoning the Azure AD Connect Sync encryption key
>[!IMPORTANT]
>The following procedures only apply to Azure AD Connect build 1.1.443.0 or older.

Under specific conditions, if the password is updated, the Synchronization Service can no longer retrieve the encryption key via DPAPI. Without the encryption key, the Synchronization Service cannot decrypt the passwords required to synchronize to/from on-premises AD and Azure AD.

When this occurs, you may see one or more of the following:

- Under Windows Service Control Manager, if you try to start the Synchronization Service and it cannot retrieve the encryption key, it fails with error “Windows could not start the Microsoft Azure AD Sync on Local Computer. For more information, review the System Event log. If this is a non-Microsoft service, contact the service vendor, and refer to service-specific error code -21451857952.”
- Under Windows Event Viewer, the application event log contains an error with Event ID 6028 and error message *“The server encryption key cannot be accessed.”*
- If you try to start the Synchronization Service in Windows Service Control Manager, you will receive the error "Windows could not start the Microsoft Azure AD Sync service on Local Computer. Error 1069: The service did not start due to a logon failure."
- Under Windows Event Viewer, the system event log contains an error with Event ID 7038 and message “The ADSync service was unable to log on as with the currently configured password due to the following error: The user name or password is incorrect."

### What to do if you need to abandon the encryption key

If you need to abandon the encryption key, use the following procedures below to accomplish this.

1. [Abandon the existing encryption key](#abandon-the-existing-encryption-key)

2. [Provide the password of the AD DS account](#provide-the-password-of-the-ad-ds-account)

3. [Reinitialize the password of the Azure AD sync account](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Start the Synchronization Service](#start-the-synchronization-service)

#### Abandon the existing encryption key
Abandon the existing encryption key so that new encryption key can be created:

1. Log in to your Azure AD Connect Server as administrator.

2. Start a new PowerShell session.

3. Navigate to folder: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Run the command: `./miiskmu.exe /a`

![Azure AD Connect Sync Encryption Key Utility](media/active-directory-aadconnectsync-encryption-key/key5.png)

#### Provide the password of the AD DS account
As the existing passwords stored inside the database can no longer be decrypted, you need to provide the Synchronization Service with the password of the AD DS account. The Synchronization Service encrypts the passwords using the new encryption key:

1. Start the Synchronization Service Manager (START → Synchronization Service).
</br>![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  
2. Go to the **Connectors** tab.
3. Select the **AD Connector** that corresponds to your on-premises AD. If you have more than one AD connector, repeat the following steps for each of them.
4. Under **Actions**, select **Properties**.
5. In the pop-up dialog, select **Connect to Active Directory Forest**:
6. Enter the password of the AD DS account in the **Password** textbox. If you do not know its password, you must set it to a known value before performing this step.
7. Click **OK** to save the new password and close the pop-up dialog.
![Azure AD Connect Sync Encryption Key Utility](media/active-directory-aadconnectsync-encryption-key/key6.png)

#### Reinitialize the password of the Azure AD sync account
You cannot directly provide the password of the Azure AD service account to the Synchronization Service. Instead, you need to use the cmdlet **Add-ADSyncAADServiceAccount** to reinitialize the Azure AD service account. The cmdlet resets the account password and makes it available to the Synchronization Service:

1. Start a new PowerShell session on the Azure AD Connect server.
2. Run cmdlet `Add-ADSyncAADServiceAccount`.
3. In the pop-up dialog, provide the Azure AD Global admin credentials for your Azure AD tenant.
![Azure AD Connect Sync Encryption Key Utility](media/active-directory-aadconnectsync-encryption-key/key7.png)
4. If it is successful, you will just see the PowerShell command prompt.

#### Start the Synchronization Service
Now that the Synchronization Service has access to the encryption key and all the passwords it needs, you can restart the service in the Windows Service Control Manager:


1. Go to Windows Service Control Manager (START → Services).
2. Select **Microsoft Azure AD Sync** and click Restart.

## Next steps
**Overview topics**

* [Azure AD Connect sync: Understand and customize synchronization](active-directory-aadconnectsync-whatis.md)

* [Integrating your on-premises identities with Azure Active Directory](active-directory-aadconnect.md)