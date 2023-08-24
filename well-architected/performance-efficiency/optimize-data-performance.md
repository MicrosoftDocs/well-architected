---
title: Recommendations for optimizing data performance
description: Learn best practices for optimizing data performance.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing data performance: PE 08

This guide describes the best practices for optimizing data performance. Data performance optimization includes designing the right data solutions and maintaining their performance over time. Optimizing data performance helps ensure a workload is responsive to data requests which can be a bottleneck in workloads. Poor data performance results in slow response times, processing delays, and poor user experience.

## Definitions

|  Terms|                                  Definitions|
  |-|-|
  Data store                |             A resource that stores data such as a database, object store, file share, etc.|
|  Eventual consistency|                   A data synchronization model that allows for temporary inconsistency in data replicas before eventually becoming the same.|
|  Optimistic concurrency                 |An approach for updating databases that uses snapshots to make updates instead of database locks. |
|  PACELC theorem                        | A framework that considers partition tolerance, availability, consistency, and latency to help explain the tradeoffs in data consistency.|
|  CAP theorem                            |A framework that considers consistency, availability, and partition tolerance to help explain the tradeoffs in data consistency.|
|Partitioning|                           A database performance optimization strategy that divides data into different segments so it's more manageable.|
|  Index|                                  A data structure that improves the speed of data retrieval by providing quick access to specific items in a database.|
|  Database index reorganization          |A maintenance activity that optimizes the current database index. |
|  Database index rebuilding              |A maintenance activity that drops and recreates the index.|
| Query tuning|                           Optimizing the speed of database queries|
| Online analytical processing (OLAP)|    A technology that organizes large business databases, supports complex analysis, and performs complex analytical queries without negatively affecting transactional systems.|
|  Online transaction processing (OLTP)|   A technology that records business interactions as they occur in the day-to-day operation of the organization, and supports querying of this data to make inferences|
|  Read replica|                           A copy of the primary database that allows you to offload read traffic from a write database.|

## Key design strategy

**Understand actual data usage patterns.** The intended usage patterns and the actual usage pattern might vary. Data stores that are built around actual usage usually outperform a data design that is general purpose. You should build data stores around actual usage patterns.

**Understand normalization.** In terms of data normalization or data duplication, moving to a less normalized structure trades off flexibility with intentional design that can lead to significant performance gains at the expense of introducing complexities with this model. Your solution might benefit from mixing both relational and non-relational data stores to achieve performance targets.

**Understand the data store options.** There are many data stores available. You need to [understand data store types](/azure/architecture/guide/technology-choices/data-store-overview) and [pick the right data store](/azure/architecture/guide/technology-choices/data-store-decision-tree) for your data.

**Understand read replicas.** Many databases support multiple read replicas. Distributing read queries across read replicas and free up demand on the write master. Read replicas can individually take a subset of traffic and potentially improve performance.

**Adopt eventual consistency.** Eventual consistency uses asynchronous data replication to prioritize responses to client requests faster at the cost of consistency in replicas. Data store nodes don't need report data synchronize status before responding to requests. You should use eventual consistency where possible to facilitate sharding and data caching. It allows shards and caches to respond more immediately to requests.

**Adopt optimistic concurrency.** Optimistic concurrency allows multiple concurrent operations without locking transactions. It detects and resolves any conflicting changes using retries, merging, or notification on reconciliation failure. This pattern helps improve performance and responsiveness. You should adopt optimistic concurrency where it makes sense.

**Consider the proximity ofdata  to users.** If you need to bring data closer to a distributed user base, you should choose a solution that supports data synchronization across regions.

**Understand tradeoffs in distributed data systems.** When designing a workload that contains multiple data replicas that are expected to stay in sync it's helpful to model this distributed system using PACELC theorem to capture the latency vs constancy tradeoff choice in the non-partitioned state of the system in addition to standard CAP theorem in the case of portioned state. Classifying workload as AP or CP and EL or EC can help you choose a database engine and data sync strategy that best addresses the system in both a partitioned and non-partitioned state.

**Design partitions for intended use.** Partitioning is a fundamental part of system design. There are three types of partitioning strategies: horizontal (sharding), vertical, and functional partitioning. You can use one or a combination of partitioning strategies. You should create a partitioning as early as possible and should follow general [partitioning best practices](/azure/architecture/best-practices/data-partitioning) and [partitioning strategies by service](/azure/architecture/best-practices/data-partitioning-strategies).

**Design indexes for intended use.** Different database solutions offer different types of indexes (B-tree, hash, bitmap). You need to understand the use case for the available indexes and pick the right ones for your workload. For relational databases, you should follow the [index design guide](/sql/relational-databases/sql-server-index-design-guide#General_Design). For more information, see [SQL Server indexes](/sql/relational-databases/indexes/indexes) and [Azure Cosmos DB indexes.](/azure/cosmos-db/index-overview)

**Separate OLTP and OLAP systems.** OLTP and OLAP have different resource needs. [OLTP](/azure/architecture/data-guide/relational-data/online-transaction-processing) systems need faster CPUs and better I/O to handle the high number of transactions. [OLAP](/azure/architecture/data-guide/relational-data/online-analytical-processing) systems need more memory to process larger datasets. You should separate OLTP and OLAP systems and customize the resources they use.

**Make sure to tune.** You should base tuning on coded CRUD usage patterns. Indexes fragment over time and degrade query performance. In SQL databases, you should [reorganize or rebuild](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) indexes on a regular basis. You need to identify slow queries and tune them to improve performance. Many database engines have query tuning features that you should use. For more information, see [tune queries in non-relational databases](/azure/cosmos-db/nosql/query-metrics#best-practices-for-query-performance).

## Azure facilitation

-   Azure SQL Database supports [read replicas](/azure/azure-sql/database/read-scale-out) to improve the performance of read-heavy workloads

-   Azure SQL Database allows you to [dynamically scale](/azure/azure-sql/database/scale-resources) to improve database performance when indexing and query tuning won't work. You can increase compute capacity with minimal downtime.

-   Azure SQL Database performs [automatic tuning](/azure/azure-sql/database/automatic-tuning-overview) for queries to improve their performance.

-   [Azure SQL](/azure/azure-sql/database/sql-insights-overview) and [Cosmos DB](/azure/cosmos-db/insights-overview) have an Insights feature that monitors your database. It allows you to diagnose and tune database performance issues.

-   Cosmos DB has [default indexing policy](/azure/cosmos-db/index-policy) that indexes every property of every item and enforces range indexes for any string or number. This allows you to get good query performance without having to think about indexing and index management upfront.

## Tradeoff

-   Designing data solutions for their intended use might delay software development at the onset.

-   Partitioning adds management complexity that can hinder operational efficiency.

-   Maintain indexes, queries, and data shape increases the load on the operations team.
