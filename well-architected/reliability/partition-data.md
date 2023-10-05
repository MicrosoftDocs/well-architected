---
title: Data partitioning recommendations for reliability
description: Learn how to partition data in a distributed system to improve scalability, reduce contention, and optimize performance.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for data partitioning

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|[RE:05](checklist.md)| Implement a timely and reliable scaling strategy at the application, data, and infrastructure levels. |
|---|---|

**Related guide:** [Scaling](scaling.md)

This guide describes the recommendations for designing a data partitioning strategy for the database and data storage technology that you deploy. This strategy helps you improve the reliability of your data estate.

## Key design strategies

In many large-scale solutions, *partitions* are used to divide data so that it can be managed and accessed separately. Partitioning data improves scalability, reduces contention, and optimizes performance. Implement data partitioning to divide data by usage pattern. For example, you can archive older data in inexpensive data storage. Choose your partitioning strategy carefully to maximize the benefits and minimize adverse effects.

> [!NOTE]
> In this article, the term *partitioning* means the process of physically dividing data into separate data stores. It differs from SQL Server table partitioning.

### Why partition data?

- **Improve scalability.** When you scale up a single database system, the database eventually reaches a physical hardware limit. If you divide data across multiple partitions, with each partition hosted on a separate server, you can scale out the system almost indefinitely.

- **Improve performance.** In each partition, data access operations are performed over a smaller volume of data compared to data that isn't partitioned. Partition data to make your system more efficient. Operations that affect more than one partition can run in parallel.

- **Improve security.** In some cases, you can separate sensitive and nonsensitive data into different partitions, and apply different security controls to the sensitive data.

- **Provide operational flexibility.** You can partition data to fine-tune operations, maximize administrative efficiency, and minimize cost. For example, you can define strategies for management, monitoring, backup and restore, and other administrative tasks based on the importance of the data in each partition.

- **Match the data store to the pattern of use.** You can deploy each partition on a different type of data store based on the cost and the built-in features that the data store offers. For example, you can store large binary data in blob storage, and store structured data in a document database. For more information, see [Understand data store models](/azure/architecture/guide/technology-choices/data-store-overview).

- **Improve availability.** To avoid a single point of failure, you can separate data across multiple servers. If one instance fails, only the data in that partition is unavailable. Operations continue in other partitions. This consideration is less relevant for managed platform as a service (PaaS) data stores because they have built-in redundancy.

### Design partitions

There are three typical strategies for partitioning data:

- **Horizontal partitioning** (often called *sharding*). In this strategy, each partition is a separate data store, but all partitions have the same schema. Each partition is known as a *shard* and holds a subset of the data, such as a set of customer orders.

- **Vertical partitioning.** In this strategy, each partition holds a subset of the fields for items in the data store. The fields are divided according to their pattern of use. For example, frequently accessed fields might be placed in one vertical partition and less frequently accessed fields in another.

- **Functional partitioning.** In this strategy, data is aggregated according to how each bounded context in the system uses the data. For example, an e-commerce system might store invoice data in one partition and product inventory data in another.

Consider combining these strategies when you design a partitioning scheme. For example, you might divide data into shards and then use vertical partitioning to further subdivide the data in each shard.

#### Horizontal partitioning (sharding)

The following image shows an example of horizontal partitioning, or sharding. This example divides product inventory data into shards that are based on the product key. Each shard holds the data for a contiguous range of shard keys (A-G and H-Z), organized alphabetically. When you perform sharding, it spreads the load over more computers, which reduces contention and improves performance.

:::image type="content" source="media/partition-data/partition-data-key.png" alt-text="Diagram that shows how to horizontally partition data into shards based on a product key." border="false" lightbox="media/partition-data/partition-data-key.png":::

The most important factor is the sharding key that you choose. It can be difficult to change the key after the system is in operation. The key must ensure that data is partitioned to spread the workload as evenly as possible across the shards.

The shards don't have to be the same size. It's more important to balance the number of requests. Some shards might be large, but each item in the shard has a low number of access operations. Other shards might be smaller, but each item in the shard is accessed more frequently. It's also important to ensure that a single shard doesn't exceed the scale limits, in terms of capacity and processing resources, of the data store.

Avoid creating *hot* partitions that can affect performance and availability. For example, if you use the first letter of a customer's name, it can create an unbalanced distribution because some letters are more common than others. Instead, use a customer identifier hash to distribute data evenly across partitions.

Choose a sharding key that minimizes the future need to split large shards, combine small shards into larger partitions, or change the schema. These operations are time-consuming and might require you to take one or more shards offline.

If shards are replicated, you can keep some of the replicas online while others are split, merged, or reconfigured. However, the system might limit the operations that can be performed during the reconfiguration. For example, the data in the replicas might be marked as read-only to prevent data inconsistences.

For more information, see [Sharding pattern](/azure/architecture/patterns/sharding).

#### Vertical partitioning

The most common use for vertical partitioning is to reduce the I/O and performance costs that are associated with fetching frequently accessed items. The following image shows an example of vertical partitioning. In this example, different properties of an item are stored in different partitions. One partition holds data that's accessed more frequently, including product name, description, and price. Another partition holds inventory data, including the stock count and the last ordered date.

:::image type="content" source="media/partition-data/partition-by-pattern.png" alt-text="Diagram that shows how to vertically partition data by its pattern of use." border="false" lightbox="media/partition-data/partition-by-pattern.png":::

In this example, the application regularly queries the product name, description, and price when it displays the product details to customers. The stock count and last ordered date are in a separate partition because these two items are commonly used together.

See the following advantages of vertical partitioning:

- You can separate relatively slow-moving data (product name, description, and price) from more dynamic data (stock level and last ordered date). Slow-moving data is a good candidate for an application to cache in memory.

- You can store sensitive data in a separate partition with added security controls.

- Vertical partitioning can reduce the amount of concurrent access that's needed.

Vertical partitioning operates at the entity level within a data store, partially normalizing an entity to break it down from a *wide* item to a set of *narrow* items. It's ideally suited for column-oriented data stores, such as HBase and Cassandra. If the data in a collection of columns is unlikely to change, consider using column stores in SQL Server.

#### Functional partitioning

When a bounded context can be identified for each distinct business area in an application, functional partitioning can improve isolation and data access performance. Another common use for functional partitioning is to separate read-write data from read-only data. The following image shows an overview of functional partitioning that has inventory data separated from customer data.

:::image type="content" source="media/partition-data/partition-context-or-subdomain.png" alt-text="Diagram that shows how to functionally partition data bounded by context or subdomain." border="false" lightbox="media/partition-data/partition-context-or-subdomain.png":::

This partitioning strategy can help reduce data access contention across different parts of a system.

### Design partitions for scalability

It's vital to consider the size and workload for each partition. Balance them so that data is distributed to achieve maximum scalability. However, you must also partition the data so that it doesn't exceed the scaling limits of a single partition store.

Follow these steps when you design partitions for scalability:

1. Analyze the application to understand the data access patterns, such as the size of the result set that each query returns, frequency of access, inherent latency, and server-side compute processing requirements. In many cases, a few major entities demand most of the processing resources.

1. Use this analysis to determine the current and future scalability targets, such as the data size and workload. Then distribute the data across the partitions to meet the scalability target. For horizontal partitioning, choose the right shard key to ensure even distribution. For more information, see [Sharding pattern](/azure/architecture/patterns/sharding).

1. Ensure that each partition has enough resources to handle the scalability requirements in terms of the data size and throughput. Depending on the data store, there might be a limit for each partition on the amount of storage space, processing power, or network bandwidth. If the requirements are likely to exceed these limits, you might need to refine your partitioning strategy or split data out further. You might need to combine two or more strategies.

1. Monitor the system to verify that data is distributed as expected and that the partitions can handle the load. Actual usage doesn't always match what an analysis predicts. You might have to rebalance the partitions or redesign some parts of the system to yield the required balance.

Some cloud environments allocate resources based on infrastructure boundaries. Ensure that the limits of your selected boundary provide enough room for anticipated growth in data volume, data storage, processing power, and bandwidth.

For example, if you use Azure Table Storage, there's a limit to the volume of requests that a single partition can handle in a particular period of time. For more information, see [Scalability and performance targets for standard storage accounts](/azure/storage/storage-scalability-targets). A busy shard might require more resources than a single partition can handle. You might need to repartition the shard to spread the load. If the total size or throughput of these tables exceeds the capacity of a storage account, you might need to create more storage accounts and spread the tables across these accounts.

### Design partitions for query performance

You can boost query performance by using small datasets and running parallel queries. Each partition should contain a small proportion of the entire dataset. This reduction in volume can improve the performance of queries. However, partitioning isn't an alternative to appropriate database design and configuration. Ensure that you implement the necessary indexes.

Follow these steps when you design partitions for query performance:

1. Examine the application requirements and performance.

    - Use business requirements to determine the critical queries that must always perform quickly.

    - Monitor the system to identify queries that perform slowly.

    - Determine the queries that perform most frequently. Even if a single query has a minimal cost, cumulative resource consumption can be significant.

1. Partition the data that's causing slow performance.

    - Limit the size of each partition so that the query response time is within target.

    - If you use horizontal partitioning, design the shard key so that the application can easily select the appropriate partition. This specification prevents the query from scanning every partition.

    - Consider the location of a partition. Try to keep data in partitions that are geographically close to the applications and users that access it.

1. If an entity has throughput and query performance requirements, use functional partitioning that's based on that entity. If this allocation still doesn't satisfy the requirements, you can add horizontal partitioning. A single partitioning strategy is usually adequate, but in some cases, it's more efficient to combine both strategies.

1. Run queries in parallel across partitions to improve performance.

### Design partitions for availability

Partition data to improve the availability of applications. Partitioning ensures that the entire dataset doesn't have a single point of failure, and you can independently manage individual subsets of the dataset.

Consider the following factors that affect availability:

**Determine the criticality of the data.** Identify the critical business data, such as transactions, and the less critical operational data, such as log files.

- Store critical data in highly available partitions, and create an appropriate backup plan.

- Establish separate management and monitoring procedures for different datasets.

- Place data that has the same level of criticality in the same partition so that it can be backed up at the same frequency. For example, you might need to back up partitions that hold transaction data more often than partitions that hold logging or trace information.

**Manage individual partitions.** Design partitions to support independent management and maintenance. This practice provides several advantages, for example:

- If a partition fails, it can be recovered independently without applications that access data in other partitions.

- Partitioning data by geographic area allows scheduled maintenance tasks to occur at off-peak hours for each location. Ensure that partitions aren't so large that they prevent planned maintenance from finishing during this period.

**Replicate critical data across partitions.** This strategy improves availability and performance but can also introduce consistency issues. It takes time to synchronize changes with every replica. During synchronization, different partitions contain different data values.

### Application design considerations

Partitioning adds complexity to the design and development of your system. Partition data as a fundamental part of your system design even if the system initially only contains a single partition. If you address partitioning as an afterthought, it's challenging because you already have a live system to maintain. You might:

- Have to modify data access logic.

- Have to migrate large quantities of existing data to distribute it across partitions.

- Run into challenges because users expect to continue using the system during the migration.

In some cases, partitioning isn't important because the initial dataset is small and a single server can easily handle it. Some workloads can go without partitions, but many commercial systems need to expand as the number of users increase.

Some small data stores also benefit from partitioning. For example, hundreds of concurrent clients might access a small data store. If you partition the data in this situation, it can help to reduce contention and improve throughput.

Consider the following points when you design a data partitioning scheme:

**Minimize cross-partition data access operations.** Try to keep data for the most common database operations together in a partition to minimize cross-partition data access operations. It can be more time-consuming to query across partitions rather than querying within a single partition. But optimizing partitions for one set of queries might adversely affect other sets of queries. If you must query across partitions, minimize query time by running parallel queries and aggregating the results within the application. In some cases, you can't use this approach, for example if the result from one query is used in the next query.

**Replicate static reference data.** If queries use relatively static reference data, such as postal code tables or product lists, consider replicating this data in all the partitions to reduce separate lookup operations in different partitions. This approach can also reduce the likelihood of the reference data becoming a *hot* dataset with heavy traffic from across the entire system. There are extra costs associated with synchronizing changes to the reference data.

**Minimize cross-partition joins.** Where possible, minimize requirements for referential integrity across vertical and functional partitions. In these schemes, the application is responsible for maintaining referential integrity across partitions. Queries that join data across multiple partitions are inefficient because the application typically performs consecutive queries that are based on a key and then a foreign key. Instead, consider replicating or de-normalizing the relevant data. If cross-partition joins are necessary, run parallel queries over the partitions and join the data within the application.

**Embrace eventual consistency.** Evaluate whether strong consistency is a requirement. A common approach in distributed systems is to implement eventual consistency. The data in each partition is updated separately, and the application logic ensures that the updates finish successfully. The application logic also handles the inconsistencies that arise from querying data while an eventually consistent operation runs.

**Consider how queries locate the correct partition.** If a query must scan all partitions to locate the required data, it significantly affects performance, even when multiple parallel queries run. With vertical and functional partitioning, queries can specify the partition. On the other hand, horizontal partitioning can make locating an item difficult because every shard has the same schema. A typical solution is to maintain a map that's used to look up the shard location of items. Implement this map in the sharding logic of the application. It can also be maintained by the data store if the data store supports transparent sharding.

**Rebalance shards periodically.** With horizontal partitioning, rebalancing shards can help evenly distribute the data by size and workload. Rebalance shards to minimize hotspots, maximize query performance, and work around physical storage limitations. This task is complex and often requires a custom tool or process.

**Replicate partitions.** Replicate each partition to provide added protection against failure. If a single replica fails, queries are directed to a working copy.

**Extend scalability to a different level.** If you reach the physical limits of a partitioning strategy, you might need to extend the scalability to a different level. For example, if partitioning is at the database level, you might need to locate or replicate partitions in multiple databases. If partitioning is already at the database level, and there are physical limitations, you might need to locate or replicate partitions in multiple hosting accounts.

**Avoid transactions that access data in multiple partitions.** Some data stores implement transactional consistency and integrity for operations that modify data but only when the data is located in a single partition. If you need transactional support across multiple partitions, implement it as part of your application logic because most partitioning systems don't provide native support.

All data stores require some operational management and monitoring activity. These tasks include loading data, backing up and restoring data, reorganizing data, and ensuring that the system performs correctly and efficiently.

Consider the following factors that affect operational management:

- **Implement appropriate management and operational tasks when the data is partitioned.** These tasks might include backup and restore, archiving data, monitoring the system, and other administrative tasks. For example, it can be challenging to maintain logical consistency during backup and restore operations.

- **Load data into multiple partitions, and add new data that comes from other sources.** Some tools and utilities might not support sharded data operations, such as loading data into the correct partition.

- **Archive and delete data regularly.** To prevent the excessive growth of partitions, archive and delete data every month. You might need to transform the data to match a different archive schema.

- **Locate data integrity problems.** Consider running a periodic process to locate data integrity problems, such as data in one partition that references missing information in another. The process can either automatically attempt to fix these issues or generate a report for manual review.

### Rebalance partitions

As a system matures, you might have to adjust the partitioning scheme. For example, individual partitions might start receiving a disproportionate volume of traffic and become hot, leading to excessive contention. Or you might have underestimated the volume of data in some partitions, which causes the partitions to approach capacity limits.

Some data stores, such as Azure Cosmos DB, can automatically rebalance partitions. In other cases, you can rebalance partitions in two stages:

1. Determine a new partitioning strategy.

   - Which partitions need to be split or combined?

   - What's the new partition key?

1. Migrate data from the old partitioning scheme to the new set of partitions.

You might need to make partitions unavailable while you relocate data, which is called *offline migration*. Depending on the data store, you might migrate data between partitions while they're in use. This technique is called *online migration*.

#### Offline migration

Offline migration reduces the chance of contention occurring. To perform offline migration:

1. Mark the partition as offline. You can mark a partition as read-only so that applications can still read the data while you move it.

1. Split-merge and move the data to the new partitions.

1. Verify the data.

1. Bring the new partitions online.

1. Remove the old partition.

#### Online migration

Online migration is more complex but less disruptive compared to offline migration. The process is similar to offline migration, but you don't mark the original partition as offline. Depending on the granularity of the migration process, for example item by item versus shard by shard, the data access code in the client applications might have to read and write data that's in two locations, the original partition and the new partition.

## Azure facilitation

The following sections describe recommendations for partitioning data that's stored in Azure services.

### Partition in Azure SQL Database

A single SQL database has a limit to the volume of data that it can contain. Throughput is constrained by architectural factors and the number of concurrent connections that it supports.

[Elastic pools](/azure/sql-database/sql-database-elastic-pool) support horizontal scaling for a SQL database. Use elastic pools to partition your data into shards that are spread across multiple SQL databases. You can also add or remove shards as the volume of data grows and shrinks. Elastic pools can also help reduce contention by distributing the load across databases.

Each shard is implemented as a SQL database. A shard can hold more than one dataset. Each dataset is called a *shardlet*. Each database has metadata that describes the shardlets that it contains. A shardlet can be a single data item or a group of items that share the same shardlet key. For example, in a multitenant application, the shardlet key can be the tenant ID, and all data for a tenant can be in the same shardlet.

Applications are responsible for associating a dataset with a shardlet key. A separate SQL database acts as a global shard map manager. This database has a list of all the shards and shardlets in the system. The application connects to the shard map manager database to obtain a copy of the shard map. It caches the shard map locally and uses the map to route data requests to the appropriate shard. This functionality is hidden behind a series of APIs that are contained in the [client library of the Elastic Database feature of SQL Database](/azure/sql-database/sql-database-elastic-database-client-library), which is available for Java and .NET.

For more information about elastic pools, see [Scaling out with SQL Database](/azure/sql-database/sql-database-elastic-scale-introduction).

To reduce latency and improve availability, you can replicate the global shard map manager database. With the premium pricing tiers, you can configure active geo-replication to continuously copy data to databases in different regions.

Alternatively, use [SQL Data Sync for SQL Database](/azure/sql-database/sql-database-sync-data) or [Azure Data Factory](/azure/data-factory) to replicate the shard map manager database across regions. This form of replication runs periodically and is more suitable if the shard map changes infrequently and doesn't require the premium tier.

Elastic Database provides two schemes for mapping data to shardlets and storing them in shards:

- A **list shard map** associates a single key with a shardlet. For example, in a multitenant system, the data for each tenant can be associated with a unique key and stored in its own shardlet. To guarantee isolation, each shardlet can be held within its own shard.

   :::image type="content" source="media/partition-data/point-shardlet.svg" alt-text="Diagram that shows shardlets held in their own shard." border="false" lightbox="media/partition-data/point-shardlet.svg":::

   *Download a [Visio file](https://arch-center.azureedge.net/data-partitioning-strategies.vsdx) of this diagram.*

- A **range shard map** associates a set of contiguous key values with a shardlet. For example, you can group the data for a set of tenants, each with their own key, within the same shardlet. This scheme is less expensive than a list shard map because tenants share data storage, but it provides less isolation.

   :::image type="content" source="media/partition-data/range-shardlet.svg" alt-text="Diagram that shows sets of tenants within the same shardlets." border="false" lightbox="media/partition-data/range-shardlet.svg":::

   *Download a [Visio file](https://arch-center.azureedge.net/data-partitioning-strategies.vsdx) of this diagram*

A single shard can contain the data for several shardlets. For example, you can use list shardlets to store data for different non-contiguous tenants in the same shard. You can also mix range shardlets and list shardlets in the same shard, but then they are addressed via different maps. The following diagram shows this approach:

:::image type="content" source="media/partition-data/multiple-shard-maps.svg" alt-text="Diagram that shows shardlets within the same shard that are addressed via different maps." border="false" lightbox="media/partition-data/multiple-shard-maps.svg":::

*Download a [Visio file](https://arch-center.azureedge.net/data-partitioning-strategies.vsdx) of this diagram.*

With elastic pools, you can add and remove shards as the volume of data grows and shrinks. Client applications can create and delete shards dynamically and transparently update the shard map manager. However, removing a shard is a destructive operation that also requires deleting all the data in that shard.

If an application needs to split a shard into two separate shards or combine shards, use the [split-merge tool](/azure/sql-database/sql-database-elastic-scale-overview-split-and-merge). This tool runs as an Azure web service and migrates data safely between shards.

The partitioning scheme can significantly affect the performance of your system. It can also affect the rate at which shards have to be added or removed, or that data must be repartitioned across shards. Consider the following points:

- Group data that's used together in the same shard, and avoid operations that access data from multiple shards. A shard is a SQL database in its own right, and cross-database joins must be performed on the client side when operations access multiple shards.

    Although SQL Database doesn't support cross-database joins, you can use Elastic Database tools to perform [multi-shard queries](/azure/sql-database/sql-database-elastic-scale-multishard-querying). A multi-shard query sends individual queries to each database and merges the results.

- Design a system that doesn't have dependencies between shards. Referential integrity constraints, triggers, and stored procedures in one database can't reference objects in another.

- Consider replicating data across shards if you have reference data that's frequently used by queries. This approach can eliminate the need to join data across databases. Ideally, such data should be static or slow-moving to minimize the replication effort and reduce the chance of it becoming stale.

- Use the same schema for shardlets that belong to the same shard map. This guidance isn't enforced by SQL Database, but data management and querying is complex if each shardlet has a different schema. Instead, create separate shard maps for each schema. You can store data that belongs to different shardlets in the same shard.

- Store data in the same shard or implement eventual consistency if your business logic needs to perform transactions. Transactional operations are only supported for data that's in a shard, and not across shards. Transactions can span shardlets if they're part of the same shard.

- Place shards close to the users that access the data in those shards. This strategy helps reduce latency.

- Avoid having a combination of highly active and relatively inactive shards. Try to spread the load evenly across shards. You might have to hash the sharding keys. If you're geo-locating shards, ensure that the hashed keys map to shardlets held in shards that are stored close to the users that access that data.

### Partition in Azure Blob Storage

With Blob Storage, you can store large binary objects. Use block blobs in scenarios that require you to quickly upload or download large volumes of data. Use page blobs for applications that require random, rather than serial, access to parts of the data.

Each block blob or page blob is held in a container in an Azure storage account. Use containers to group related blobs that have the same security requirements. This grouping is logical rather than physical. Inside a container, each blob has a unique name.

The partition key for a blob is the account name, the container name, and the blob name. The partition key is used to partition data into ranges. These ranges are load balanced across the system. Blobs can be distributed across many servers to scale out access to them. A single blob can only be served by a single server.

If your naming scheme uses timestamps or numerical identifiers, it can lead to excessive traffic to one partition. It prevents the system from effectively load balancing. For instance, if you have daily operations that use a blob object with a timestamp, such as *yyyy-mm-dd*, all the traffic for that operation goes to a single partition server. Instead, prefix the name with a three-digit hash. For more information, see [Partition naming convention](/azure/storage/common/storage-performance-checklist#partitioning).

The actions of writing a single block or page are atomic, but operations that span blocks, pages, or blobs aren't. If you need to ensure consistency when write operations are performed across blocks, pages, and blobs, take out a write lock by using a blob lease.

## Tradeoffs

Data partitioning introduces some challenges and complexities that you need to consider.

- Data synchronization between the partitions might become a challenge. Ensure that updates or changes to one partition are propagated to the other partitions in a timely and consistent manner.

- Failover and disaster recovery processes become complex when you need to coordinate the backup and restore of multiple partitions. Data integrity issues can arise if some partitions or their backups are corrupted or unavailable.

- Data partitioning can affect performance and reliability if you need to query across partitions, and when you rebalance the partitions if the data grows unevenly.

## Related links

- [Building scalable cloud databases](/azure/azure-sql/database/elastic-database-client-library)
- [Data Factory](/azure/data-factory)
- [Index Table pattern](/azure/architecture/patterns/index-table)
- [Materialized View pattern](/azure/architecture/patterns/materialized-view)
- [Moving data between scaled-out cloud databases](/azure/azure-sql/database/elastic-scale-overview-split-and-merge)
- [Multi-shard querying using elastic database tools](/azure/azure-sql/database/elastic-scale-multishard-querying)
- [Partition naming](/azure/storage/blobs/storage-performance-checklist#partitioning)
- [Review your data options](/azure/architecture/guide/technology-choices/data-options)
- [Scalability and performance targets for standard storage accounts](/azure/storage/storage-scalability-targets)
- [Scaling out with SQL Database](/azure/azure-sql/database/elastic-scale-introduction)
- [Sharding pattern](/azure/architecture/patterns/sharding)
- [Understand data store models](/azure/architecture/guide/technology-choices/data-store-overview)
- [Use elastic pools to manage and scale multiple databases in SQL Database](/azure/azure-sql/database/elastic-pool-overview)
- [What is SQL Data Sync for Azure?](/azure/azure-sql/database/sql-data-sync-data-sql-server-sql-database) 

## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md) 