---
title: Recommendations for optimizing data performance
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
| Data store| A resource that stores data such as a database, object store, or file share.|
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

To optimize data usage, ensure that data stores, partitions, and indexes are optimized for their intended use and for their actual use in a workload. Optimized data usage can improve query performance, reduce resource consumption, and enhance overall system efficiency. Consider the following strategies:

- **Profile data**. Understand your data and ensure that your data model is well-suited for the workload. Consider factors such as data normalization, indexing strategies, and partitioning techniques. For efficient data retrieval, ensure that you select appropriate data types, define relationships between entities, and determine the optimal indexing strategy.

- **Fine-tune your data storage configuration**. Configure your data storage infrastructure to align with your workload requirements. Select an appropriate storage technology, for example relational databases, NoSQL databases, and data warehouses. Optimize storage settings, such as buffer size, caching mechanisms, and compression.

- **Optimize query performance**. Analyze and optimize the queries that run in the workload. Use techniques such as query optimization, indexing, and caching. To identify bottlenecks, use query execution plans and performance monitoring tools, and make necessary improvements.

- **Regularly monitor and tune the system**. Continuously monitor the performance of the workload and iterate on the data storage configuration and query optimizations. Based on performance tuning best practices, analyze system metrics, identify areas of improvement, and implement changes.

### Profile data

To profile data, analyze and understand the structure, volume, and relationships of your data. You can gain insights into your data and identify areas that need improvement. Consider the following overview for performing data profiling:

- **Understand the data structure**. Examine the structure of your data, including tables, columns, and relationships. Determine the data types, lengths, and constraints that are applied to each column. Data structure evaluation helps you understand how the data is organized and how it relates to other data elements.

- **Analyze data volume**. Assess the volume of your data to understand the overall size and growth patterns. Determine the number of records or documents and the size of individual tables or collections. This information helps you estimate storage requirements and identify scalability issues.

- **Identify data relationships**. Explore the relationships between different data elements, such as primary and foreign key relationships. Understand how data is connected so you can determine how changes in one table or document might affect related data.

- **Assess data quality**. Evaluate the quality of your data by examining factors such as completeness, accuracy, consistency, and uniqueness. Identify data anomalies, missing values, or duplicate records that might affect data integrity and query performance. This step helps you identify areas for data cleansing and improvement.

- **Capture data distribution**. Analyze the distribution of values within each column to understand data patterns. Identify frequent and rare values, outliers, and data skews. To optimize query performance, choose appropriate indexing strategies and query optimization techniques based on the distribution.

### Monitor data

Continuously monitor the performance of your data stores, partitions, and indexes in real time. To identify areas of improvement and performance degradation, collect and analyze performance metrics from your data stores, partitions, and indexes. Use tools like system-level monitoring tools, database-specific monitoring features, or third-party monitoring solutions. Collect metrics such as CPU usage, memory utilization, disk I/O, query response times, and data throughput.

- *Set up alerts*. Configure alerts based on predefined thresholds or anomalies in the performance metrics. Alerts enable you to receive notifications when performance metrics exceed acceptable ranges or show abnormal behavior. You can set up alerts by using monitoring tools or custom scripts that trigger actions when specific conditions are met.
- *Diagnose issues*. Regularly analyze the collected performance metrics to identify areas of improvement and performance degradation. Use visualization tools or dashboards to gain insights into performance trends, bottlenecks, and anomalies. Identify the root causes of performance issues and determine appropriate actions for resolution.

### Partition data

If you have large datasets or high-volume workloads, consider partitioning your data across multiple storage resources. Partitioning can help distribute the workload and improve parallel processing. You can partition data vertically or horizontally. Horizontal partitioning is also called sharding.

**Vertical partitioning**. To perform vertical partitioning, divide a table into multiple smaller tables by selecting the columns or fields that you want to place in each partition. Each partition represents a subset of the complete data. This technique is useful when there are columns that are frequently accessed together, but not necessarily accessed together with other columns. Implement vertical partitioning if:

- A table contains a large number of columns, but not all columns are accessed together in most queries.

- Some columns are larger than others and separating them can improve I/O performance.

- Different parts of the data have varying access patterns, and if you separate them, queries run more efficiently.

**Horizontal partitioning (sharding)**. To perform horizontal partitioning, split data into multiple partitions based on rows or range of values. Each partition contains a subset of rows with similar characteristics. Horizontal partitioning is commonly used with large datasets to distribute the data across different physical storage locations. Horizontal partitioning improves performance and scalability. Implement horizontal partitioning if:

- A dataset is too large to store in a single location or server.

- Data is accessed or queried based on specific ranges or filters, which makes it efficient to search within a smaller subset of partitions.

- You need to distribute your workload evenly across multiple physical nodes or servers to improve performance and scalability.

To implement vertical or horizontal partitioning:

- *Analyze data and queries*: Analyze the data and query patterns to identify suitable partitioning or sharding strategies. Understand the nature of the data, access patterns, and distribution requirements.

- *Determine a key*: Choose a partitioning or sharding key to distribute the data across the partitions or shards. Carefully select the key based on the data characteristics and query requirements.

- *Design logic*: Determine the partitioning or sharding logic based on the chosen key. Consider dividing the data into ranges, applying hashing algorithms, or using other partitioning techniques.

- *Configure and implement*: Configure the database system to support partitioning or sharding. Consider creating the necessary infrastructure, defining the partitions or shards, and configuring the data distribution.

For more information, see [Partitioning best practices](/azure/architecture/best-practices/data-partitioning).

### Optimize queries

Optimize your database queries by rewriting, reordering, or restructuring them to improve their efficiency. Use query optimization techniques such as index hints, query caching, and query plan analysis. Optimize database queries by implementing the following techniques and considerations:

- *Rewrite queries*. Review and analyze complex queries to identify opportunities to rewrite them. Consider restructuring the query logic, eliminating redundant operations, or simplifying the query syntax.

- *Avoid the N+1 query problem*. Minimize the number of roundtrips to the database by using joins and batch fetching to retrieve related data efficiently.

- *Reorder joins*. The order in which you join tables can affect query performance. Evaluate the query execution plan and consider rearranging the join order to minimize the number of rows involved in each join operation.

- *Use index hints*. Use index hints to enable the database engine to specify the use of specific indexes for query execution. Index hints guide the optimizer to select the most appropriate indexes for efficient data retrieval.

- *Cache queries*. To implement query caching, store the results of frequently run queries in memory. This method eliminates the need for repeatedly running the same query, and it reduces query processing overhead.

- *Optimize locking*. Efficient locking strategies can enhance query performance and concurrency. Avoid unnecessary or restrictive lock hints in queries. Apply optimized locking mechanisms that the database system provides. Analyze and adjust isolation levels to balance data consistency and query performance.

- *Monitor and tune*. Regularly monitor query performance metrics such as runtime, resource utilization, and query throughput. Use database profiling tools and monitoring functionalities to identify poorly performing queries. Evaluate and fine-tune query execution plans based on collected performance data. Analyze query execution plans and wait statistics to identify bottlenecks. Use that information to optimize query performance.

### Optimize indexes

 Indexes increase the speed of data retrieval because the database can quickly locate the desired data based on specific columns or fields. Optimize indexes to improve the efficiency of sorting and join operations, which leads to fast query runtimes.

 Efficient indexes reduce the amount of disk I/O operations that are needed for running queries. Optimize queries to reduce the duration of locks and contention on data pages, which allows multiple users to concurrently access and modify data. When you eliminate redundant or unnecessary indexes, you can make better use of that storage space.

- *Analyze query patterns*. Understand the query patterns that run on your database. Identify the queries that run frequently and might degrade performance. Analyze query patterns to determine which indexes are beneficial for optimizing performance.

- *Evaluate existing indexes*. Review the existing indexes in your database. Evaluate their usage, performance effects, and relevance to the query patterns. Identify redundant or unused indexes that you can remove to improve write performance and reduce storage overhead.

- *Identify columns for indexing*. Identify columns that are frequently used in the WHERE, JOIN, and ORDER BY clauses of your queries. These columns are potential candidates for indexing because they can improve query execution by enabling faster data retrieval.

- *Choose an appropriate index type*. Select an appropriate index type based on your database system. Common options include B-tree indexes for equality and range queries, hash indexes for exact match queries, and full-text indexes for text search operations. Choose the index type that best matches your query requirements.

- *Consider index column order*. When you create composite indexes, or indexes with multiple columns, consider the order of the columns. Place the columns that are most frequently used in queries at the beginning of the index. Column order helps ensure that the your workload is effectively using indexes for a wide range of queries.

- *Balance index size*. Avoid creating indexes on columns with low cardinality, or columns that have a low number of distinct values. Such indexes can be inefficient and increase the size of your database. Instead, index columns that have a higher selectivity.

- *Maintain index usage*. Continuously monitor the usage and performance of your indexes. Look for opportunities to create new indexes or modify existing indexes based on changes in query patterns or performance requirements. Remove or update indexes that are no longer beneficial. Indexes have maintenance overhead. As data changes, indexes can become fragmented and affect performance. Regularly perform index maintenance tasks such as rebuilding or reorganizing indexes to ensure optimal performance.

- *Test and validate*. Before you revise indexes in a production environment, perform thorough testing and validation. Measure the performance effect of index revisions by using representative workloads. Verify the improvements against predefined benchmarks.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: B-tree indexes might have higher storage overhead, slower for exact match queries.
Hash indexes aren't suitable for range queries or comparison operators.
Full-text indexes might have higher storage requirements, slower for nontextual data.

### Consider data compression

Data compression is the process of reducing the size of data to optimize storage space and improve workload performance efficiency. Compressed data requires less storage space and less bandwidth for transmitting, which results in faster data transfer.

Perform data compression to reduce the storage footprint and improve data access times. Compressing data reduces I/O operations and network bandwidth requirements. Lossless compression and lossy compression are data compression algorithms. Lossless compression algorithms reduce the size of data without losing any information. Lossy compression algorithms achieve high compression ratios by removing less important or redundant information.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: To compress and decompress data, you need computational resources, such as CPU and memory. The more you compress the data, the more resources you need.

### Archive and purge data

Implement data archiving and purging strategies to remove obsolete or infrequently accessed data. These strategies can help reduce storage costs and improve data operation performance by:

- *Reducing data volume*: Eliminate data that you don't need to reduce the amount of data that you need to process.

- *Increasing data access speed*: With a smaller dataset to search through, queries and data access operations can be performed faster.

- *Reducing backup and recovery*: By reducing the amount of data that you need to back up and restore, you can save time and improve efficiency.

### Optimize storage load

Implement strategies that reduce the processing burden on the data store and improve overall performance. To optimize data store load:

**Use caching**. Cache data by storing frequently accessed data in a high-speed data storage layer. For hot data scenarios, caching increases read throughput and improves client response times. Caching is most effective for scenarios in which data is relatively static or doesn't change frequently.

Carefully consider factors like cache expiration policies, cache eviction strategies, and cache size management. To maximize the benefits of caching, you might need to tune caching parameters, such as time to live (TTL). Consider the following caching techniques:

- *In-memory caching*: Perform in-memory caching to store frequently accessed data in memory for fast retrieval. You can use this technique for application data that's expensive to compute or retrieve from a database. In-memory caching is useful for data that's read frequently but doesn't frequently change.

- *Database query caching*: Use this technique to cache the results of database queries to avoid running the same query multiple times. Database query caching is useful for complex and time-consuming database queries. When you cache the results of a query, subsequent requests for the same query return quickly.

- *Content delivery network (CDN) caching*: Use this technique to cache web content on distributed network servers to reduce latency and improve content delivery. CDN caching is effective for static content, like images, CSS files, and JavaScript files. CDNs store copies of content in multiple locations worldwide, so users can access the content from a server that's near them geographically.

**Use read replicas**. Many databases support multiple read replicas. Distribute read queries across replicas to minimize the demand on the write database. Read replicas can individually take a subset of traffic and potentially improve performance.

When you have a workload with multiple data replicas that you expect to stay in sync, it's helpful to model this distributed system by using the PACELC theorem. The PACELC theorem helps you understand the latency versus constancy tradeoff choice in the nonpartitioned state of the system. Classifying workload can help you choose a database engine and data sync strategy that best addresses the system in a partitioned and nonpartitioned state.

For more information, see the [CQRS pattern](/azure/architecture/patterns/cqrs).

**Use eventual consistency**. Eventual consistency is a consistency model. Over time, updates are propagated to replicas or nodes in a distributed workload. There might be temporary inconsistencies between replicas, but the workload eventually converges to a consistent state.

Eventual consistency helps optimize data updates because it increases availability and scalability. With strict consistency, every update has to be immediately visible on all replicas. With eventual consistency, there's a tradeoff between consistency and performance. Updates can be processed asynchronously, which reduces the delay and increases the throughput of data updates.

### Optimize data updates

You can use optimistic concurrency to handle concurrent updates to the same data. Instead of locking data and preventing other updates, optimistic concurrency allows multiple users or processes to work concurrently and assumes that conflicts are rare.

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

We recommend that you review the Performance Efficiency checklist to explore other concepts.

> [!div class="nextstepaction"]
> [Performance Efficiency checklist](checklist.md)
