## Sessions and Requests
Since SQL Data Warehouse is a distributed MPP system session, each session and request is handled as multiple sessions and requests throughout the distributed system. 

Sessions and requests are represented logically by one identifier that starts with SID or QID respectively.

| Identifier | Example value |
| :--------- | :------------ |
| Session ID | SID123456     |
| Request ID | QID123456     |

## Concurrency and Workload Management
SQL Data Warehouse supports up to 32 concurrent queries. By default, each query counts as one single unit of concurrency regardless of whether the query itself is a single threaded or multi-threaded. The concurrency units are also called concurrency slots.

Each query also receives an allocation of resources from a resource class. The most important resource in this context is memory since most data warehouse tables use in-memory clustered columnstore indexes. Also, each distributed query uses multiple threads.

To allocate more memory to a query, you can assign a query to run with more than one concurrency slot. This reduces the number of queries that can run concurrently. The additional resources can be extremely useful, especially for data management operations.

### Example: Display the database roles
Workload management is implemented as a series of **database roles**. Each role represents a **resource class**. The roles are:

- smallrc
- mediumrc
- largerc
- xlargerc

This query displays the roles:

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

### Example: Add and remove users from resource classes
By default, all users belong to the smallrc resource class. This is the default configuration. You can increase your memory (and reduce warehouse concurrency) by adding a database user to one of the other roles.

To add or remove a user to a resource class, use the `sp_addrolemember` and `sp_droprolemember` procedures. This requires  `ALTER ROLE` permission.

> [AZURE.NOTE] A simple way to manage users and resource classes is to create different logins and database users for each resource class. This way you can easily give more resources to an operation by submitting it through a different user.


| Resource Class    | Concurrency Slots Used | Memory Available (per distribution) | Query Importance |
| :---------------- | :--------------------- | :---------------------------------- | :--------------- |
| smallrc (default) | 1                      | 133 MB                              | Medium           |
| mediumrc          | 3                      | xxx MB                              | Medium           |
| largerc           | 7                      | xxx MB                              | High             |
| xlargerc          | 22                     | xxx MB                              | High             |

The higher the resource class assigned to the user the greater the concurrency slot consumption.  Remember there is a maximum of 32 concurrency slots available in SQLDW. Consequently, while a query is being executed by a user assigned to the extra large resource class there are only 9 slots remaining for other queries. These can be made up of any combination of the remaining resources classes. However, it is not possible to have two queries being executed by users at the same time in the xlargerc resource class. The second query would be queued up and would have to wait for the first one to complete to attain sufficient concurrency slots prior to execution.



