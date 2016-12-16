---
title: Quick reference for frequently used commands for import jobs | Microsoft Docs
description: Command reference for Azure Import/Export tool commands frequently used for import jobs
author: renashahmsft
manager: aungoo
editor: tysonn
services: storage
documentationcenter: ''

ms.assetid:
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: renash

---
# Quick Reference for Frequently Used Commands for Import Jobs

This section provides a quick reference for some frequently used commands. For detailed usage, see [Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import-v1.md).

For the first session:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Second session:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

Abort the latest session:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

Resume the latest interrupted session:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

Add drives to the latest session:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## See Also

[Sample workflow to prepare hard drives for an import job](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.mdf)