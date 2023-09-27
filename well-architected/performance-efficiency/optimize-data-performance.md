---
title: Data performance optimization recommendations
description: Learn best practices for optimizing data performance.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing data performance

**Applies to: PE 08**

This guide describes the recommendations for optimizing workload data performance. Optimizing workload data performance refers to improving the efficiency and performance of data processing and storage within a workload. It involves optimizing data access, retrieval, storage, and processing operations to enhance the overall performance of the workload. Optimizing workload data performance is important for workload performance efficiency because it can have a significant effect on the overall performance of a system. Failure to optimize workload data performance leads to slower response times, increased latency, and degraded performance. It also hinders scalability and limits the ability to handle increasing workloads effectively.

**Definitions**

|Term|Definition|
|---|---|
| Data store| A resource that stores data such as a database, object store, file share, etc.|
|  Eventual consistency|A data synchronization model that allows for temporary inconsistency in data replicas before eventually becoming the same.|
| Optimistic concurrency|An approach for updating databases that uses snapshots to make updates instead of database locks. |
|  PACELC theorem | A framework that considers partition tolerance, availability, consistency, and latency to help explain the tradeoffs in data consistency.|
| CAP theorem |A framework that considers consistency, availability, and partition tolerance to help explain the tradeoffs in data consistency.|
|Partitioning|A database performance optimization strategy that divides data into different segments so it's more manageable.|
|  Index|A data structure that improves the speed of data retrieval by providing quick access to specific items in a database.|
|  Database index reorganization |A maintenance activity that optimizes the current database index. |
|  Database index rebuilding |A maintenance activity that drops and recreates the index.|
| Query tuning| Optimizing the speed of database queries|
| Online analytical processing (OLAP)|A technology that organizes large business databases, supports complex analysis, and performs complex analytical queries without negatively affecting transactional systems.|
|  Online transaction processing (OLTP)|A technology that records business interactions as they occur in the day-to-day operation of the organization, and supports querying of this data to make inferences|
|  Read replica| A live copy of the primary database that allows you to offload read traffic from a write database.|

## Key strategies

Optimizing data usage involves ensuring that data stores, partitions, and indexes are optimized for both their intended use and actual use in a workload. It's important for workload performance efficiency because optimized data usage can improve query performance, reduce resource consumption, and enhance overall system efficiency. Here are some high-level strategies:

- *Profile data*: Understand the data and ensure the data model is well-suited for the workload. Consider factors such as data normalization, indexing strategies, and partitioning techniques. Ensure you're selecting appropriate data types, defining relationships between entities, and determining the optimal indexing strategy for efficient data retrieval.
- *Fine-tune data storage configuration*: Configure the data storage infrastructure to align with the workload requirements. You should include selecting the appropriate storage technology (for example, relational databases, NoSQL databases, data warehouses) and optimizing storage settings such as buffer size, caching mechanisms, and compression.
- *Optimize query performance*: Analyze and optimize the queries executed in the workload. Use techniques such as query optimization, indexing, and caching. Use query execution plans and performance monitoring tools to identify bottlenecks and make necessary optimizations.
- *Regularly monitor and tune the system*: Continuously monitor the performance of the workload and iterate on the data storage configuration and query optimizations. Include analyzing system metrics, identifying areas of improvement, and implementing changes based on performance tuning best practices.

### Profile data

Data profiling involves analyzing and understanding the structure, volume, and relationships of your data. It helps you gain insights into your data and identify areas for optimization to actual usage. Here's an overview of how to perform data profiling:

- *Understand the data structure*. Examine the structure of your data, including tables, columns, and relationships. Determine the data types, lengths, and constraints applied to each column. It helps you understand how the data is organized and how it relates to other data elements.
- *Analyze data volume*. Assess the volume of your data to understand the overall size and growth patterns. Determine the number of records or documents, and the size of individual tables or collections. This information helps you estimate storage requirements and identify potential scalability issues.
- *Identify data relationships*. Explore the relationships between different data elements, such as primary and foreign key relationships. Knowing the relationship allows you to understand how data is connected and how changes in one table or document may affect related data.
- *Assess data quality*. Evaluate the quality of your data by examining factors such as completeness, accuracy, consistency, and uniqueness. Identify any data anomalies, missing values, or duplicate records that may affect data integrity and query performance. This step helps you identify areas for data cleansing and improvement.
- *Capture data distribution*. Analyze the distribution of values within each column to understand data patterns. Identify frequent and rare values, outliers, and data skews. Knowing the distribution helps you optimize query performance by choosing appropriate indexing strategies and query optimization techniques.

### Monitor data

Continuously monitor the performance of your data stores, partitions, and indexes in real-time. Collect and analyze performance metrics to identify areas of improvement and potential performance degradation. Utilize tools and techniques to collect performance metrics from your data stores, partitions, and indexes. Tools include system-level monitoring tools, database-specific monitoring features, or third-party monitoring solutions. Collect metrics such as CPU usage, memory utilization, disk I/O, query response times, and data throughput.

- *Set up alerts*. Configure alerts based on predefined thresholds or anomalies in the performance metrics. Alerts enable you to receive notifications when performance metrics exceed acceptable ranges or show abnormal behavior. Alerts can be set up using monitoring tools or custom scripts that trigger actions when specific conditions are met.
- *Diagnose issues*. Regularly analyze the collected performance metrics to identify areas of improvement and potential performance degradation. Use visualization tools or dashboards to gain insights into performance trends, bottlenecks, and anomalies. Identify the root causes of performance issues and determine appropriate actions for resolution.

### Partition data

If you're dealing with large datasets or high-volume workloads, consider partitioning your data across multiple storage resources. Partitioning can help distribute the workload and improve parallel processing. Data partitioning can be vertical or horizontal (sharding).

*Vertical partitioning*. Vertical partitioning involves dividing a table into multiple smaller tables by selecting specific columns or fields to be placed in each partition. Each partition represents a subset of the complete data. This technique is useful when there are columns that are frequently accessed together, but not necessarily accessed together with other columns. Vertical partitioning is typically implemented when:

- The table contains a large number of columns, but not all columns are accessed together in most queries.
- Some columns have a larger size than others, and separating them can improve I/O performance.
- Different parts of the data have varying access patterns, and separating them can enable more efficient query execution.

*Horizontal partitioning (sharding)*. Horizontal partitioning involves splitting data into multiple partitions based on rows or range of values. Each partition contains a subset of rows with similar characteristics. Horizontal partitioning is commonly used with large datasets to distribute the data across different physical storage locations for improved performance and scalability. Horizontal partitioning is commonly implemented when:

- The dataset is too large to be stored in a single location or server.
- The data is accessed or queried based on specific ranges or filters, making it efficient to search within a smaller subset of partitions.
- The workload needs to be distributed evenly across multiple physical nodes or servers for improved performance and scalability.

To implement vertical or horizontal partitioning, consider the following steps:

- *Analyze data and queries*: Analyze the data and query patterns to identify suitable partitioning or sharding strategies. Understand the nature of the data, the access patterns, and the distribution requirements.
- *Determine key*: Choose a partitioning or sharding key to distribute the data across the partitions or shards. The key should be carefully selected based on the data characteristics and query requirements.
- *Design logic*: Determine the partitioning or sharding logic based on the chosen key. Consider dividing the data into ranges, applying hashing algorithms, or using other partitioning techniques.
- *Configure and implement*: Configure the database system to support partitioning or sharding. Consider creating the necessary infrastructure, defining the partitions or shards, and configuring the data distribution.

For more information, see [partitioning best practices](/azure/architecture/best-practices/data-partitioning).

### Optimize queries

Optimize your database queries by rewriting, reordering, or restructuring them to improve their efficiency. Use query optimization techniques such as index hints, query caching, and query plan analysis. Here are some techniques and considerations for optimizing database queries:

- *Rewrite queries*. Review and analyze complex queries to identify opportunities for rewriting them in a more efficient way. You should consider restructuring the query logic, eliminating redundant operations, or simplifying the query syntax.
- *Avoid N+1 query problem*. Minimize the number of round-trips to the database by using joins and batch fetching to retrieve related data efficiently.
- *Reorder joins*. The order in which tables are joined can affect query performance. Evaluate the query execution plan and consider rearranging the join order to minimize the number of rows involved in each join operation.
- *Use index hints*. Index hints allow the database engine to specify the use of specific indexes for query execution. Index hints guide the optimizer in selecting the most appropriate indexes for efficient data retrieval.
- *Cache queries*. Implementing query caching can improve performance by storing the results of frequently executed queries in memory. It avoids the need for redundant execution of the same query and reduces the overhead associated with query processing.
- *Optimize locking*. Efficient locking strategies can enhance query performance and concurrency. Avoid unnecessary or restrictive lock hints in queries, and apply optimized locking mechanisms provided by the database system. Analyze and adjust isolation levels as required to balance data consistency and query performance.
- *Monitor and tune*. Regularly monitor query performance metrics such as execution time, resource utilization, and query throughput. Use database profiling tools and monitoring functionalities to identify poorly performing queries and areas for optimization. Evaluate and fine-tune query execution plans based on collected performance data. Analyze query execution plans and WAIT STATS to identify potential bottlenecks and optimize query performance.

### Optimize indexes

Optimizing an index refers to the process of improving the performance and efficiency of an index in a database. Indexes speed up data retrieval by allowing the database to quickly locate the desired data based on specific columns or fields.

Index optimization improves the efficiency of sorting and join operations, leading to faster query execution times. Efficient indexes reduce the amount of disk input/output (I/O) operations needed for query execution. They reduce the duration of locks and contention on data pages, allowing multiple users to access and modify data concurrently. Well-planned indexing strategies can also reduce the storage space required by eliminating redundant or unnecessary indexes.

- *Analyze query patterns*. Understand the query patterns that are executed on your database. Identify the queries that are frequently executed and affect performance. Analyzing query patterns helps you determine which indexes are most beneficial for optimizing performance.
- *Evaluate existing indexes*. Review the existing indexes in your database. Evaluate their usage, performance effects, and relevance to the query patterns. Identify any redundant or unused indexes that can be removed to improve write performance and reduce storage overhead.
- *Identify columns for indexing*. Identify the columns that are frequently used in the WHERE, JOIN, and ORDER BY clauses of your queries. These columns are potential candidates for indexing as they can improve query execution by enabling faster data retrieval.
- *Choose the right index type*. Select the appropriate index type based on your database system. Common options include B-tree indexes for equality and range queries, hash indexes for exact match queries, and full-text indexes for text search operations. Choose the index type that best matches your query requirements.
- *Consider index column order*. When creating composite indexes (indexes with multiple columns), consider the order of the columns. Place the columns most frequently used in queries at the beginning of the index. It helps ensure that the index is effectively used for a wider range of queries.
- *Balance index size*. Avoid creating indexes on columns with low cardinality (where the number of distinct values is low). Such indexes can be inefficient and increase the size of your database. Instead, focus on indexing columns that have a higher selectivity.
- *Maintain index usage*. Continuously monitor the usage and performance of your indexes. Look for opportunities to create new indexes or modify existing ones based on changes in query patterns or performance requirements. Remove or update indexes that are no longer beneficial. Keep in mind the maintenance overhead associated with indexes. As data changes, indexes can become fragmented and affect performance. Regularly perform index maintenance tasks such as rebuilding or reorganizing indexes to ensure optimal performance.
- *Test and validate*. Before implementing any index changes in a production environment, perform thorough testing and validation. Measure the performance effect of index changes using representative workloads and verify the improvements against predefined benchmarks.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: B-tree indexes may have higher storage overhead, slower for exact match queries.
Hash indexes aren't suitable for range queries or comparison operators.
Full-text indexes may have higher storage requirements, slower for nontextual data.

### Consider data compression

Data compression is the process of reducing the size of data to optimize storage space and improve workload performance efficiency. It's important for workload performance efficiency because compressed data takes up less storage space and requires less bandwidth to transmit, resulting in faster data transfer and improved overall performance.

Apply data compression techniques to reduce the storage footprint and improve data access times. Compressing data can enhance performance by reducing I/O operations and network bandwidth requirements. There are different techniques and algorithms used for data compression, including lossless and lossy compression. Lossless compression algorithms reduce the size of data without losing any information, while lossy compression algorithms achieve higher compression ratios by removing some less important or redundant information.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Compressing and decompressing data requires computational resources, such as CPU and memory. The more compressed the data, the more resources needed for compression and decompression.

### Archive and purge data

Implement data archiving and purging strategies to remove obsolete or infrequently accessed data. It helps reduce storage costs and improves the performance of data operations. Data archiving and purging can improve workload performance efficiency in the following ways:

- *Reduced data volume*: Optimizing performance by reducing the amount of data that needs to be processed.
- *Faster data access*: With a smaller dataset to search through, queries and data access operations can be performed faster.
- *Reduced backup and recovery*: By reducing the amount of data that needs to be backed up and restored, you can significantly reduce the time required for these operations, leading to improved efficiency.

### Optimize storage load

Optimizing data store load involves various strategies to reduce the processing burden on the data store and improve overall performance. Here are some approaches to optimize data store load:

**Use caching**. Caching involves storing frequently accessed data in a high-speed data storage layer. Caching increases read throughput and improves client response times for hot data scenarios. It's most effective for scenarios where the data being cached is relatively static or doesn't change frequently.

Implementing caching requires careful consideration of various factors. These factors include cache expiration policies, cache eviction strategies, and cache size management. You may need to tune caching parameters, such as Time to Live (TTL), to maximize the benefits and minimize any drawbacks. Here are some caching techniques and when to use them:

- *In-Memory caching*: In-memory caching stores frequently accessed data in memory for faster retrieval. It's often used to cache application data that is expensive to compute or retrieve from a database. In-memory caching is useful when the data is read frequently and doesn't frequently change.
- *Database query caching*: In this technique, the results of database queries are cached to avoid executing the same query multiple times. It's beneficial when dealing with complex and time-consuming database queries. Caching the results allows subsequent requests for the same query to return faster.
- *Content delivery network (CDN) caching*: CDN caching involves caching web content on distributed network servers to reduce latency and improve content delivery. This technique is effective for static content like images, CSS files, and JavaScript files. CDNs store copies of content in multiple locations worldwide, allowing users to access the content from a server that is geographically closer to them.

**Use read replicas**. Many databases support multiple read replicas. Distributing read queries across replicas and free up demand on the write database. Read replicas can individually take a subset of traffic and potentially improve performance.

When you have a workload with multiple data replicas that are expected to stay in sync, it's helpful to model this distributed system using PACELC theorem. The PACELC theorem helps you understand the latency vs constancy tradeoff choice in the nonpartitioned state of the system. Classifying workload can help you choose a database engine and data sync strategy that best addresses the system in both a partitioned and nonpartitioned state.

For more information, see the [CQRS pattern](/azure/architecture/patterns/cqrs).

**Use eventual consistency**. Eventual consistency is a consistency model that allows for updates to be propagated to different replicas or nodes in a distributed workload over time. It means that while there may be a temporary inconsistency between different replicas, the workload eventually converges to a consistent state.

Eventual consistency helps in optimizing data updates by allowing increased availability and scalability. Rather than enforcing strict consistency where every update has to be immediately visible on all replicas, eventual consistency provides a trade-off between consistency and performance. It allows updates to be processed asynchronously, reducing the delay and increasing the throughput of data updates.

### Optimize data updates

Optimistic concurrency is a technique used to handle concurrent updates to the same data. Instead of locking the data and preventing other updates, optimistic concurrency allows multiple users or processes to work concurrently and assumes that conflicts are rare.

With optimistic concurrency, each update operation includes a version or timestamp that represents the state of the data at the time of the update. When a conflicting update is detected, the system can resolve the conflict by either rejecting the update or merging the changes.

Optimistic concurrency helps in optimizing data updates by minimizing contention and allowing concurrent updates to proceed without unnecessary locking. It improves performance by reducing the time spent waiting for resources and allows for higher overall throughput.

### Optimize data movement and processing

Optimizing data movement and processing involves improving the efficiency and performance of operations related to data extraction, transformation, loading, and processing. Here are some key aspects of optimizing data movement and processing:

- *ETL optimization*: Extract, Transform, Load (ETL) processes can be optimized to minimize processing time. ETL optimization includes streamlining the extraction process, implementing efficient transformation algorithms, and optimizing the loading process. By reducing the time taken for each step and optimizing the overall workflow, the ETL process can be made more efficient.
- *Parallel processing*: Utilizing parallel processing techniques can significantly improve data processing performance. By distributing data processing tasks across multiple threads or nodes, the workload can be divided and processed concurrently, leading to faster processing times and improved overall performance.
- *Batch processing*: Implementing batch processing involves grouping similar tasks together to reduce overhead from repeated operations. Processing multiple tasks in a batch can reduce the overall processing time.

### Optimize storage design

By carefully designing the data storage structure and utilizing appropriate storage technologies, you can optimize data access, retrieval, and manipulation, resulting in improved performance and efficiency.

**Use close proximity**. Choose a solution that supports data synchronization across regions if you need to bring data closer to a distributed user base.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: If the underlying data changes frequently, you need to implement cache invalidation mechanisms to ensure the cached data remains up to date.

**Use polyglot persistence**. Polyglot persistence refers to the practice of using multiple data storage technologies to store and manage different types of data within an application or system. It recognizes that different types of databases or storage solutions serve different data requirements.

The goal of polyglot persistence is to use the strengths of each data storage technology to ensure optimal performance and scalability for different types of data and use cases. For example, a relational database may be used to store structured, transactional data, while a NoSQL database may be used to store unstructured or semi-structured data.

Design the schema for each data storage technology based on the specific requirements of the data. For relational databases, the design may involve creating normalized tables with appropriate relationships. For NoSQL databases, the design may involve defining document structures or key-value pairs. Develop the necessary components to interact with each data storage technology, such as APIs, data access layers, or data integration pipelines. Ensure that the application can read and write data to the appropriate data stores.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: A less normalized data structure can improve performance but introduce complexities.

**Separate OLTP and OLAP systems**. Separating [OLTP](/azure/architecture/data-guide/relational-data/online-transaction-processing) (Online Transaction Processing) and [OLAP](/azure/architecture/data-guide/relational-data/online-analytical-processing) (Online Analytical Processing) systems involves designing and deploying distinct systems for transactional processing and analytical processing tasks. This separation allows you to optimize each system for its specific workload and characteristics.

OLTP systems are for real-time transactional processing. They provide efficient and reliable handling of individual transactions. They're typically used for day-to-day operational tasks such as online order processing, inventory management, and customer data management. OLTP systems prioritize responsiveness, consistency, and concurrency.

OLAP systems for complex analytical processing and reporting. They handle large volumes of data and perform intensive calculations and aggregations. OLAP systems are used for tasks such as business intelligence, data mining, and decision support. They prioritize query performance, data aggregation, and multidimensional analysis.

By separating the two, you can allocate appropriate resources and optimize each system for its specific workload, resulting in improved performance. Separation allows you to apply different data modeling techniques to each system. OLTP systems typically use normalized schemas for efficient transactional processing, while OLAP systems may use denormalized schemas or data warehousing techniques to optimize query performance.

## Azure facilitation

**Profile data**: Azure provides various tools and services for data profiling, such as Azure Data Factory, Azure Purview, and Azure Synapse Analytics. These tools enable you to extract, transform, and load data from various sources, perform data quality checks, and gain insights into the data.

**Monitor data**: To monitor data performance, you can use Azure Monitor's capabilities such as collecting and analyzing infrastructure metrics, logs, and application data. Azure Monitor integrates with other services like Application Insights, which provides application performance monitoring and supports multiple platforms.

Additionally, you can use Log Analytics to correlate usage and performance data collected by Application Insights with configuration and performance data across Azure resources.

[Azure SQL](/azure/azure-sql/database/sql-insights-overview) and [Cosmos DB](/azure/cosmos-db/insights-overview) have an Insights feature that monitors your database. It allows you to diagnose and tune database performance issues.

**Partition**: Azure provides various partitioning strategies for different data stores. Each data store may have different considerations and configuration options for data partitioning. For more information, see [partitioning strategies by service](/azure/architecture/best-practices/data-partitioning-strategies).

**Optimize queries and indexes**: Use Query Performance Insights and Performance Recommendations to optimize your queries, tables, and databases.

For relational databases, you should follow the [index design guide](/sql/relational-databases/sql-server-index-design-guide#General_Design), [SQL Server indexes](/sql/relational-databases/indexes/indexes), and [Azure Cosmos DB indexes.](/azure/cosmos-db/index-overview). Azure SQL Database performs [automatic tuning](/azure/azure-sql/database/automatic-tuning-overview) for queries to improve their performance. It also has a Query Performance Insight feature to identify and troubleshoot query performance issues.

In SQL databases, you should [reorganize or rebuild](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) indexes regularly. You need to identify slow queries and tune them to improve performance. Many database engines have query tuning features that you should use. For more information, see [tune queries in nonrelational databases](/azure/cosmos-db/nosql/query-metrics#best-practices-for-query-performance).

Cosmos DB has [default indexing policy](/azure/cosmos-db/index-policy) that indexes every property of every item and enforces range indexes for any string or number. It allows you to get good query performance without having to think about indexing and index management upfront.

**Choose a data store**. Azure offers many different data store options to fit your workload needs. There's guidance to help you [understand data store types](/azure/architecture/guide/technology-choices/data-store-overview) and [pick the right data store](/azure/architecture/guide/technology-choices/data-store-decision-tree) for your data.

**Read replicas**. Many Azure database services support read replicas. The availability and configuration of read replicas may vary depending on the specific database service in Azure. It's recommended to refer to the official documentation for each service to understand the details and options available.

## Related links

## Community links

## Next steps

