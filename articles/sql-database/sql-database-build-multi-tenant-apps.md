# Builds Multi-tenant Apps

![build-multi-tenant-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## Leverage elastic pools and build more efficient multi-tenant apps

If you're a SaaS dev writing a multi-tenant app and handling many customers, you often make tradeoffs in customer performance, management, and security. With Azure SQL Database Elastic Database Pools, you no longer have to make that compromise. These Pools help you manage and monitor multi-tenant apps and gain isolation benefits of one-customer-per-database.

## Auto-scaling you control

Pools automatically scale performance and storage capacity for elastic databases on the fly. You can control the performance assigned to a pool, add or remove elastic databases on demand, and define performance of elastic databases without affecting the overall cost of the pool. This means you don't have to worry about managing the usage of individual databases.

[Read the documentation](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool-guidance/)

## Intelligent management of your environment

Built-in sizing recommendations proactively identify databases that would benefit from pools. These recommendations allow "what-if" analysis for quick optimization to meet your performance goals. Rich performance monitoring and troubleshooting dashboards help you visualize historical pool utilization.

[Read the documentation](http://go.microsoft.com/fwlink/?LinkID=787583&clcid=0x409)

## Performance and price to meet your needs

Basic, Standard, and Premium pools provide you a broad spectrum of performance, storage and pricing options. Pools can contain up to 400 elastic databases. Elastic databases can auto-scale up to 1000 elastic database transaction units (eDTU).

[Read the documentation](https://azure.microsoft.com/en-us/pricing/details/sql-database/?b=16.50)

## Elastic tools

In addition to elastic pools, there are SQL Database features to help manage operational activities across multiple databases:

** Perform cross-database queries and reporting. **  
[Elastic database query](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-query-overview/) enables you to run queries or reports across databases in your elastic pool and access remote data stored in many databases of your pool at once.

** Run cross database transactions. **  
[Elastic database transactions](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-transactions-overview/) allow you to run transactions that span several databases in SQL Databases and perform operations (i.e. when processing financial transactions across databases, or when updating inventory in one database and orders).

** Execute the same operations on several databases. **  
[Elastic database jobs](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-jobs-overview/) execute administrative operations such as rebuilding indexes or updating schemas across each database in your elastic pool.

Go to the homepage to see what else SQL Database has to offer.
[Check it out]() 
