---
ms.assetid: 
title: Key Vault soft delte | Microsoft Docs
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/09/2017
---
# Soft delete feature overview

In this preview version of Azure Key Vault (Azure Key Vault endpoint v = VVVV) we’ll describe recoverable deletion of Key Vaults and Key Vault objects, known as soft-delete. Specifically, we’ll addresses the following scenarios:

- Support for recoverable deletion of a key vaults
- Support for recoverable deletion of key vault objects (ex. keys, secrets, certificates)

## Scenarios

Azure Key Vaults are tracked resources, managed by ARM (Azure Resource Manager). ARM also specifies a well-defined behavior for deletion, which requires that a successful DELETE operation must result in that resource not being accessible anymore. The soft-delete feature addresses the recovery of the deleted object, whether the deletion was accidental or intentional.

1. In the typical scenario, a user may have inadvertently deleted a key vault or a key vault object; if that key vault or key vault object were to be recoverable for a predetermined period, the user may undo the deletion and recover their data.

2. In a different scenario, a rogue user may attempt to delete a key vault or a key vault object, such as a key inside a vault, to cause a business disruption. Separating the deletion of the key vault or key vault object from the actual deletion of the underlying data can be used as a safety measure by, for instance, restricting permissions on data deletion to a different, trusted role. This effectively requires quorum for an operation which might otherwise result in an immediate data loss.

### Soft-delete behavior

With this preview, the DELETE operation on a key vault or key vault object is a soft-delete, effectively holding the resources for a given retention period, while giving the appearance that the object is deleted. The service further provides a mechanism for recovering the deleted object, essentially undoing the deletion. Soft-delete is an optional Key Vault behavior and is not enabled by default in this release.

### Key vault recovery

Upon deleting a key vault, the service will create a proxy resource under its namespace, adding sufficient metadata for recovery. The proxy resource is a stored object, available in the same location as the deleted key vault. 

### Key vault object recovery

Upon deleting a key vault object, such as a key, the service will place the object in a deleted state, thus making it inaccessible to any retrieval operations. While in this state, the key vault object can only be listed, recovered or forcefully/permanently deleted. 
At the same time, Key Vault will schedule the deletion of the underlying data corresponding to the deleted key vault or key vault object for execution after a predetermined retention interval. The DNS record corresponding to the vault is also retained for the duration of the retention interval.
Enabling Key Vault’s soft delete behavior is done through ----. You may also query Key Vault to know the state of the soft-delete feature via ____.

### Retention period

During the retention interval, the following apply:

- You may list all of the key vaults and key vault objects in the soft-delete state for your subscription as well as access deletion and recovery information about them.
- A key vault with the same name cannot be created in the same location; correspondingly, a key vault object cannot be created in a given vault if that key vault contains an object with the same name and which is in a deleted state 
- Only a specifically privileged user may restore a key vault or key vault object by issuing a recover command on the corresponding proxy resource.
- Only a specifically privileged user may forcibly delete a key vault or key vault object by issuing a delete command on the corresponding proxy resource.

Unless a key vault or key vault object is recovered, at the end of the retention interval the service will perform a purge of the soft-deleted key vault or key vault object and its content. Resource deletion may not be rescheduled.

### Permissioned purge

Permanently deleting a key vault is possible via a DELETE operation on the proxy resource, and requires special privileges. Only members of the ‘vault owner’ role have this permission. An appropriately privileged user issuing a DELETE request against a proxy resource corresponding to a deleted vault will trigger the immediate and irrecoverable deletion of that vault. An exception to this is the case when the Azure subscription has been marked as **undeletable** – only the service may then perform the actual deletion, and will do so as a scheduled process. 


