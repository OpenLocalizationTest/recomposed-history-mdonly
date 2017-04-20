---
title: Application Insights Telemetry Data Model - Dependency Telemetry | Microsoft Docs
description: Application Insights data model for dependency telemetry
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov

ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz

---
# Dependency Telemetry

Dependency Telemetry represents an interaction of the monitored component with a remote component like SQL or an HTTP endpoint.

## Identity

### Name

Name of the command initiated with this dependency call. Low cardinality value. Examples are stored procedure name and URL path template.

### ID

Identifier of a dependency call instance. Used for correlation with the request telemetry item corresponding to this dependency call. For more information, see [correlation](/correlation.md) page.

### Data

Command initiated by this dependency call. Examples are SQL statement and HTTP URL with all query parameters.

### Type

Dependency type name. Low cardinality value for logical grouping of dependencies and interpretation of other fields like commandName and resultCode. Examples are SQL, Azure table, and HTTP.

### Target

Target site of a dependency call. Examples are server name, host address. For more information, see [correlation](/correlation.md) page.

## Result

### Duration

Request duration in format: `DD.HH:MM:SS.MMMMMM`. Must be less than `1000` days.

### Result code

Result code of a dependency call. Examples are SQL error code and HTTP status code.

### Success

Indication of successful or unsuccessful call.

## Extensibility

### Custom properties

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

### Custom measurements

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

