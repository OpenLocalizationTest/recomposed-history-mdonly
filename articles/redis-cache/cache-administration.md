<properties 
	pageTitle="How to administer Azure Redis Cache | Microsoft Azure"
	description="Learn how to perform administration tasks such as reboot and schedule updates for Azure Redis Cache"
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="douge"
	editor="tysonn" />
<tags 
	ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="06/28/2016"
	ms.author="sdanie" />

# How to administer Azure Redis Cache

This topic describes how to perform administration tasks such as rebooting and scheduling updates for your Azure Redis Cache instances.

>[AZURE.IMPORTANT] The settings and features described in this article are only available for Premium tier caches.


## Administration settings

The Azure Redis Cache **Administration** settings allow you to perform the following administrative tasks for your premium cache. To access them, click **Settings** or **All settings** from the Redis Cache blade.

![Administration](./media/cache-administration/redis-cache-administration.png)

-	[Reboot](#reboot)
-	[Schedule updates](#schedule-updates)

## Reboot

The **Reboot** blade allows you to reboot one or more nodes of your cache. This enables you to test your application for resiliency in the event of a failure.

![Reboot](./media/cache-administration/redis-cache-reboot.png)

If you have a premium cache with clustering enabled, you can select which shards of the cache to reboot.

![Reboot](./media/cache-administration/redis-cache-reboot-cluster.png)

To reboot one ore more nodes of your cache, select the desired nodes and click **Reboot**. If you have a premium cache with clustering enabled, select the shard(s) to reboot and then click **Reboot**. After a few minutes, the selected node(s) reboot, and are back online a few minutes later.



>[AZURE.IMPORTANT] Reboot is only available for Premium tier caches.

## Reboot FAQ

-	[Which node should I reboot to test my application?](#which-node-should-i-reboot-to-test-my-application)
-	[Can I reboot the cache to clear client connections?](#can-i-reboot-the-cache-to-clear-client-connections)
-	[Will I lose data from my cache if I do a reboot?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-	[Can I reboot my cache using PowerShell, CLI, or other management tools?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-	[What pricing tiers can use the Reboot functionality?](#what-pricing-tiers-can-use-the-reboot-functionality)


### Which node should I reboot to test my application?

To test the resiliency of your application against failure of the primary node of your cache, reboot the **Master** node. To test the resiliency of your application against failure of the secondary node, reboot the **Slave** node. To test the resiliency of your application against total failure of the cache, reboot **Both** nodes.

### Can I reboot the cache to clear client connections?

Yes, if you reboot the cache all client connections are cleared. This can useful in the case where all client connections are used up, for example due to a logic error or a bug. Each pricing tier has different client connection limits for the various sizes, and once these limits are reached, no more client connections are accepted. Rebooting the cache provides a way to clear all client connections.

### Will I lose data from my cache if I do a reboot?

If you reboot both the **Master** and **Slave** nodes all data in the cache (or in that shard if you are using a premium cache with clustering enabled) is lost. If you reboot just one of the nodes, data is not typically lost, but it still may be.

### Can I reboot my cache using PowerShell, CLI, or other management tools?

At this time you can't, but this functionality is coming soon.

### What pricing tiers can use the Reboot functionality?

Reboot is available only in the premium pricing tier.

## Schedule updates

The **Schedule updates** blade allows you to designate a maintenance window for your cache. When the maintenance window is specified, any Redis server updates are made during this window. Note that the maintenance window applies only to Redis server updates, and not to any Azure updates or updates to the operating system of the VMs that host the cache.

![Schedule updates](./media/cache-administration/redis-schedule-updates.png)

To specify a maintenance window, check the desired days and specify the maintenance window start hour for each day, and click **OK**. Note that the maintenance window time is in UTC. 

## Schedule updates FAQ

-	[When do updates occur if I don't use the schedule updates feature?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-	[What type of updates are made during the scheduled maintenance window?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-	[What pricing tiers can use the schedule updates functionality?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### When do updates occur if I don't use the schedule updates feature?

If you don't specify a maintenance window, updates can be made at any time.

### What type of updates are made during the scheduled maintenance window?

Only Redis server updates are made during the scheduled maintenance window. The maintenance window does not apply to Azure updates or updates to the VM operating system.

### What pricing tiers can use the schedule updates functionality?

Schedule updates is available only in the premium pricing tier.





