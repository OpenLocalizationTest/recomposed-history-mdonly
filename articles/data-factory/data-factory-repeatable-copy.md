---
title: Repeatable copy in Azure Data Factory| Microsoft Docs
description: 'Learn how to avoid duplicates even though a slice that copies data is run more than once.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: 

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: jingwang

---

# Repeatable copy in Azure Data Factory

## Introduction
When copying data to Azure SQL/SQL Server from other data stores, you need to keep repeatability in mind to avoid unintended outcomes. 

When copying data to Azure SQL/SQL Server Database, the copy activity appends data to the sink table by default. Say, you are copying data from a CSV (comma separated values) file containing two records to the following table in an Azure SQL/SQL Server Database. When a slice runs, the two records are copied to the SQL table. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suppose you found errors in source file and updated the quantity of Down Tube from 2 to 4. If you re-run the data slice for that period manually or the slice re-runs based on the retry policy of the dataset automatically, you’ll find two new records appended to Azure SQL/SQL Server Database. This example assumes that none of the columns in the table has the primary key constraint.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

To avoid this, you will need to specify UPSERT semantics by leveraging one of the following two mechanisms:

### Mechanism 1: using sqlWriterCleanupScript
You can use the **sqlWriterCleanupScript** property to cleanup data from the sink table before inserting the data when a slice is run. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

When a slice runs, the cleanup script is run first to delete data that corresponds to the slice from the SQL table. The copy activity subsequently inserts data into the SQL Table. If the slice is now re-run, then you will find the quantity is updated as desired.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suppose the Flat Washer record is removed from the original csv. Then re-running the slice would produce the following result: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

The copy activity ran the cleanup script to delete the corresponding data for that slice. Then it read the input from the csv (which then contained only 1 record) and inserted it into the Table. 

### Mechanism 2: using sliceIdentifierColumnName
> [!IMPORTANT]
> sliceIdentifierColumnName is not supported for Azure SQL Data Warehouse at this time. 

The second mechanism to achieve repeatability is by having a dedicated column (**sliceIdentifierColumnName**) in the target Table. This column would be used by Azure Data Factory to ensure the source and destination stay synchronized. This approach works when there is flexibility in changing or defining the destination SQL Table schema. 

This column would be used by Azure Data Factory for repeatability purposes and in the process Azure Data Factory will not make any schema changes to the Table. Way to use this approach:

1. Define a column of type **binary (32)** in the destination SQL Table. There should be no constraints on this column. Let's name this column as AdfSliceIdentifier for this example.


	Source table:

	```sql
	CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
	)
	```

	Destination table: 

	```sql
	CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
	)
	```

2. Use it in the copy activity as follows:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory populates this column as per its need to ensure the source and destination stay synchronized. The values of this column should not be used outside of this context. 

Similar to mechanism 1, Copy Activity automatically cleans up the data for the given slice from the destination SQL Table. It then inserts data from source in to the destination table. 

## Next steps
Review the following connector articles that for complete JSON examples: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)