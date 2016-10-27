<properties
    pageTitle="Get started with Relay Hybrid Connections | Microsoft Azure"
    description="How to write a C# console application for Hybrid Connections"
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="11/01/2016"
    ms.author="jotaub"/>

# Get started with Relay Hybrid Connections

[AZURE.INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## What will be accomplished

Since Hybrid Connections requires both a client and a server component, we will create two console applications in this tutorial.

1. Create a Relay namespace, using the Azure portal.

2. Create a Hybrid Connection, using the Azure portal.

3. Write a client console application to send messages.

4. Write a server console application to receive messages.

## Prerequisites

1. [Visual Studio 2013 or Visual Studio 2015](http://www.visualstudio.com). The examples in this tutorial use Visual Studio 2015.

2. An Azure subscription.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 1. Create a namespace using the Azure portal

If you already have a Relay namespace created, jump to the [Create a Hybrid Connection using the Azure portal](#2-create-a-hybrid-connection-using-the-azure-portal) section.

[AZURE.INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## 2. Create a Hybrid Connection using the Azure portal

If you already have a Hybrid Connection created, jump to the [Create a client application](#3-create-a-server-application) section.

[AZURE.INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## 3. Create a server application (listener)

To listen and receive messages from the Relay, we will write a C# console application using Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## 4. Create a client application (sender)

To send messages to the Relay, we will write a C# console application using Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

Congratulations, you have created an end-to-end Hybrid Connections application.