# Publish a custom marketplace item (service administrator)

The Microsoft Azure Stack Marketplace is a catalog of organizational, open source, and community software applications, developer services, and data pre-configured for Microsoft Azure Stack. Tenants use these items to enable various scenarios.

These items are collected from resource providers every five minutes. Service administrators can also add custom items to the marketplace. Tenants can see custom items in the Custom node of the marketplace.

To see the Microsoft Azure Stack marketplace blade, click **New** in the portal.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## What is a marketplace item?

A marketplace item is a service, application, or data that a tenant can use. It contains three important things:

-   An Azure Resource Manager template for the resource provisioning.

-   Metadata, such as strings, icons, and other marketing collateral.

-   Content and parameters needed to render the portal experience.

Each item published to the marketplace uses a format called the Azure Gallery Package (azpkg). This file does not contain deployment or runtime resources (such as code, zip files with software, virtual machines, or disks). These resources are only referenced by the templates and should be hosted external to the Azure Gallery Package.
