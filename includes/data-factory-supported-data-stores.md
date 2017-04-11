Copy Activity in Data Factory copies data from a source data store to a sink data store. Data Factory supports the following data stores. Data from any source can be written to any sink. Click a data store to learn how to copy data to and from that store.

> [!NOTE] 
> If you need to move data to/from a data store that Copy Activity doesn't support, use a **custom activity** in Data Factory with your own logic for copying/moving data. For details on creating and using a custom activity, see [Use custom activities in an Azure Data Factory pipeline](../articles/data-factory/data-factory-use-custom-activities.md).

| Category | Data store | Supported as a source | Supported as a sink |
|:--- |:--- |:--- |:--- |
| **Azure** |[Azure Blob storage](../articles/data-factory/data-factory-azure-blob-connector.md) |✓ |✓ |
| &nbsp; |[Azure Data Lake Store](../articles/data-factory/data-factory-azure-datalake-connector.md) |✓ |✓ |
| &nbsp; |[Azure DocumentDB](../articles/data-factory/data-factory-azure-documentdb-connector.md) |✓ |✓ |
| &nbsp; |[Azure SQL Database](../articles/data-factory/data-factory-azure-sql-connector.md) |✓ |✓ |
| &nbsp; |[Azure SQL Data Warehouse](../articles/data-factory/data-factory-azure-sql-data-warehouse-connector.md) |✓ |✓ |
| &nbsp; |[Azure Search Index](../articles/data-factory/data-factory-azure-search-connector.md) | |✓ |
| &nbsp; |[Azure Table storage](../articles/data-factory/data-factory-azure-table-connector.md) |✓ |✓ |
| **Databases** |[Amazon Redshift](../articles/data-factory/data-factory-amazon-redshift-connector.md) |✓ | |
| &nbsp; |[DB2](../articles/data-factory/data-factory-onprem-db2-connector.md)* |✓ | |
| &nbsp; |[MySQL](../articles/data-factory/data-factory-onprem-mysql-connector.md)* |✓ | |
| &nbsp; |[Oracle](../articles/data-factory/data-factory-onprem-oracle-connector.md)* |✓ |✓ |
| &nbsp; |[PostgreSQL](../articles/data-factory/data-factory-onprem-postgresql-connector.md)* |✓ | |
| &nbsp; |[SAP Business Warehouse](../articles/data-factory/data-factory-sap-business-warehouse-connector.md)* |✓ | |
| &nbsp; |[SAP HANA](../articles/data-factory/data-factory-sap-hana-connector.md)* |✓ | |
| &nbsp; |[SQL Server](../articles/data-factory/data-factory-sqlserver-connector.md)* |✓ |✓ |
| &nbsp; |[Sybase](../articles/data-factory/data-factory-onprem-sybase-connector.md)* |✓ | |
| &nbsp; |[Teradata](../articles/data-factory/data-factory-onprem-teradata-connector.md)* |✓ | |
| **NoSQL** |[Cassandra](../articles/data-factory/data-factory-onprem-cassandra-connector.md)* |✓ | |
| &nbsp; |[MongoDB](../articles/data-factory/data-factory-on-premises-mongodb-connector.md)* |✓ | |
| **File** |[Amazon S3](../articles/data-factory/data-factory-amazon-simple-storage-service-connector.md) |✓ | |
| &nbsp; |[File System](../articles/data-factory/data-factory-onprem-file-system-connector.md)* |✓ |✓ |
| &nbsp; |[FTP](../articles/data-factory/data-factory-ftp-connector.md) |✓ | |
| &nbsp; |[HDFS](../articles/data-factory/data-factory-hdfs-connector.md)* |✓ | |
| &nbsp; |[SFTP](../articles/data-factory/data-factory-sftp-connector.md) |✓ | |
| **Others** |[Generic HTTP](../articles/data-factory/data-factory-http-connector.md) |✓ | |
| &nbsp; |[Generic OData](../articles/data-factory/data-factory-odata-connector.md) |✓ | |
| &nbsp; |[Generic ODBC](../articles/data-factory/data-factory-odbc-connector.md)* |✓ | |
| &nbsp; |[Salesforce](../articles/data-factory/data-factory-salesforce-connector.md) |✓ | |
| &nbsp; |[Web Table (table from HTML)](../articles/data-factory/data-factory-web-table-connector.md) |✓ | |
| &nbsp; |[GE Historian](../articles/data-factory/data-factory-odbc-connector.md#ge-historian-store)* |✓ | | |

> [!NOTE]
> Data stores with * can be on-premises or on Azure IaaS, and require you to install [Data Management Gateway](../articles/data-factory/data-factory-data-management-gateway.md) on an on-premises/Azure IaaS machine.
>
>
