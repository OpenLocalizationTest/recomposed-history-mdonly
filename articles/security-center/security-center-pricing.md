<properties
   pageTitle="Security Center pricing | Microsoft Azure"
   description="This article provides information on pricing for Azure Security Center."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/06/2016"
   ms.author="terrylan"/>

# Azure Security Center pricing

Azure Security Center helps you prevent, detect, and respond to threats with increased visibility into and control over the security of your Azure resources. It provides integrated security monitoring and policy management across your Azure subscriptions, helps detect threats that might otherwise go unnoticed, and works with a broad ecosystem of security solutions.

## Pricing tiers

Security Center is offered in two tiers:

- The **Free tier** is automatically enabled on all Azure subscriptions and provides visibility into the security state of your Azure resources, basic security policy, security recommendations, and integration with security products and services from partners.
- The **Standard tier** adds advanced threat detection capabilities, including threat intelligence, behavioral analysis, anomaly detection, security incidents, and threat assessment reports. A **90 day free trial** is available for the Standard tier.

See the Security Center [pricing page](https://azure.microsoft.com/pricing/details/security-center/) for additional information.

> [AZURE.NOTE] Security Center uses Azure storage to save security data generated from your protected nodes. Costs associated with this storage are not included in the price of the service and will be charged separately at regular [Azure storage rates](https://azure.microsoft.com/pricing/details/storage/blobs/). Storage charges apply even during the trial.

## Try Security Center Standard free for 90 days

A 90 day free trial is available for the Standard tier. To get the free trial of the Standard tier, select the **Policy** tile on the **Security Center** blade.  Select the subscription that you want to upgrade to Standard. On the **Security policy** blade, select **Pricing tier**. On the **Choose your pricing tier** blade, select **Standard – Free Trial**.

![Free trial][1]

At the end of 90 days, should you choose to continue using the service, we will automatically start charging for usage.

## Upgrade to Standard to add advanced threat detection

To get the Standard tier, select the **Policy** tile on the **Security Center** blade.  Select the subscription that you want to upgrade to Standard. On the **Security policy** blade, select **Pricing tier**. On the **Choose your pricing tier** blade, select **Standard**.

![Standard tier][2]

## Why upgrade to Standard?

The Standard tier of Security Center provides all features of the Free tier plus advanced threat detection. Advanced threat detection helps identify active threats targeting your Azure resources and provides you with the insights needed to respond quickly.

Security Center employs advanced security analytics, which go far beyond signature-based approaches. Breakthroughs in big data and machine learning technologies are leveraged to evaluate events across the entire cloud fabric – detecting threats that would be impossible to identify using manual approaches and predicting the evolution of attacks.

Let’s look at the security analytics that come with the Standard tier:

- **Threat intelligence** - Looks for known bad actors by leveraging global threat intelligence from Microsoft products and services, the Microsoft Digital Crimes Unit (DCU), the Microsoft Security Response Center (MSRC), and external feeds
- **Behavioral analysis** - Applies known patterns to discover malicious behavior
- **Anomaly detection** - Uses statistical profiling to build a historical baseline. It alerts on deviations from established baselines that conform to a potential attack vector

Below is an incident that includes multiple alerts.

![Security incident][3]

## Don’t forget to enable data collection

To enable virtual machine behavioral analytics, data collection must be turned on. You may have to enable data collection when you first access Security Center or subsequently when you are creating a security policy.

To validate that data collection is enabled, select the **Policy** tile.  The **Security policy** blade will open listing your Azure subscription(s). Select a subscription. If **Data collection** is off, change it to on and save the change. See [Enable data collection in Azure Security Center](security-center-enable-data-collection.md).

## See also

In this document, you were introduced to pricing for Security Center. To learn more, see the following:

- [Security Center pricing](https://azure.microsoft.com/pricing/details/security-center/) – Get details on pricing for Security Center.
- [Azure Security Center detection capabilities](security-center-detection-capabilities.md) – Learn how Security Center’s advanced detection capabilities provides you with the insights needed to respond quickly.
- [Setting security policies in Azure Security Center](security-center-policies.md) - Learn how to configure security policies.
- [Managing security recommendations in Azure Security Center](security-center-recommendations.md) - Learn how recommendations help you protect your Azure resources.
- [Security health monitoring in Azure Security Center](security-center-monitoring.md) - Learn how to monitor the health of your Azure resources.
- [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) - Learn how to manage and respond to security alerts.
- [Handling Security Incident in Azure Security Center](security-center-incident.md) – Learn how to use security alert capability to assist you handling security incidents.
- [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md) - Learn how to monitor the health status of your partner solutions.
- [Azure Security Center FAQ](security-center-faq.md) - Find frequently asked questions about using the service.

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
