---
title: Recommendations for optimizing data performance
description: Learn how to optimize data access, retrieval, storage, and processing operations to enhance the overall performance of your workload.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing data performance

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:** 

|[PE:08](checklist.md)| Optimize data usage. Optimize data stores, partitions, and indexes for their intended and actual use in the workload.| 
|---|---| 

This guide describes the recommendations for optimizing data performance. At its core, optimizing data performance means refining the efficiency with which the workload processes and stores data. Every workload operation, transaction, or computation typically relies on the quick and accurate retrieval, processing, and storage of data. When data performance is optimized, the workload runs smoothly, but when it's compromised, it creates a domino effect of poor performance efficiency. Failure to optimize data performance results in response delays, heightened latency, and curtailed scalability. It jeopardizes the efficiency of the entire workload.

**Definitions**

|Term|Definition|
|---|---|
| CAP theorem |A framework that's used to consider consistency, availability, and partition tolerance to help explain the tradeoffs in data consistency.|
|  Database index rebuilding |A maintenance activity that drops and recreates an index.|
|  Database index reorganization |A maintenance activity that optimizes the current database index. |
| Data store| A resource, such as a database, object store, or file share, that stores data.|
|  Eventual consistency|A data synchronization model that allows for temporary inconsistency in data replicas before they eventually sync.|
|  Index|A database structure that provides quick access to items.|
| Online analytical processing (OLAP)|A technology that organizes large business databases, supports complex analysis, and performs complex analytical queries without negatively affecting transactional systems.|
|  Online transaction processing (OLTP)|A technology that records business interactions as they occur in day-to-day operations of an organization. |
| Optimistic concurrency|An approach for updating databases that uses snapshots to make updates instead of traditional locking mechanisms, improving performance and scalability. |
|  PACELC theorem | A framework that's used to consider partition tolerance, availability, consistency, and latency to help explain the tradeoffs in data consistency.|
|Partitioning|The process of physically dividing data into separate data stores.|
| Query tuning| A process that optimizes the speed of a database query.|
|  Read replica| A live copy of a primary database that enables you to offload read traffic from a write database.|

## Key design strategies

To optimize data usage, ensure that data stores, partitions, and indexes are optimized for their intended use and for their actual use in a workload. Optimized data usage can improve query performance, reduce resource consumption, and enhance overall system efficiency. Consider the following strategies:

- *Profile data.* Understand your data and ensure that your data model is well-suited for your workload. Consider factors such as data normalization, indexing strategies, and partitioning techniques. For efficient data retrieval, ensure that you select appropriate data types, define relationships between entities, and determine an optimal indexing strategy.

- *Fine-tune your data storage configuration.* Configure your data storage infrastructure to align with your workload requirements. Select an appropriate storage technology, for example relational databases, NoSQL databases, and data warehouses. Optimize storage settings, such as buffer size, caching mechanisms, and compression.

- *Optimize query performance.* Analyze and optimize queries that run in the workload. Use techniques such as query optimization, indexing, and caching. To identify bottlenecks, use query plans and performance monitoring tools, and then make necessary improvements.

- *Regularly monitor and tune the system.* Continuously monitor the performance of your workload and iterate on the data storage configuration and query optimizations. Based on performance tuning best practices, analyze system metrics, identify areas of improvement, and implement changes.

### Profile data

Data profiling involves examining the data from a source and gathering information about it. The objective is to understand the quality, structure, and characteristics of workload data. This process allows for the identification of issues such as missing values, duplicates, inconsistent formats, and other anomalies. For effective data profiling, consider the following strategies:

- *Understand the data structure.* Examine the structure of your data, including tables, columns, and relationships. Determine the data types, lengths, and constraints that are applied to each column. Data structure evaluation helps you understand how the data is organized and how it relates to other data elements.

- *Analyze the data volume.* Assess the volume of your data to understand the overall size and growth patterns. Determine the number of records or documents and the size of individual tables or collections. This information helps you estimate storage requirements and identify scalability issues.

- *Identify data relationships.* Explore the relationships between data elements, such as primary and foreign key relationships. Understand how data is connected, so you can determine how changes in one table or document might affect related data.

- *Assess data quality.* Evaluate the quality of your data by examining factors such as completeness, accuracy, consistency, and uniqueness. Identify data anomalies, missing values, or duplicate records that might affect data integrity and query performance. This step helps you identify areas for data cleansing and improvement.

- *Capture data distribution.* Analyze the distribution of values within each column to determine data patterns. Identify frequent and rare values, outliers, and data skews. To optimize query performance, choose appropriate indexing strategies and query optimization techniques based on the distribution.

### Monitor data performance

Data performance monitoring is the practice of consistently tracking the efficiency of data stores, partitions, and indexes in real-time. It involves collecting and analyzing performance metrics specific to data operations, using tools tailored for system-level, database-specific, or third-party monitoring solutions. Effective data performance monitoring allows you to proactively identify and mitigate potential bottlenecks, ensuring that data-related processes and tasks are efficient. To monitor data performance, consider the following strategies:

- *Collect data-specific metrics.* Gather key metrics that directly relate to data performance such as query response times, data throughput, disk I/O related to data access, and the load times of specific data partitions.

- *Set up data alerts.* Set up alerts specifically for data metrics. Use predefined thresholds or anomalies in these metrics to trigger alerts. Alerts enable you to receive notifications when performance metrics exceed acceptable ranges or show abnormal behavior. For instance, if a database query takes longer than expected or if data throughput drops significantly, it would trigger an alert. You can set up these alerts using specialized monitoring tools or custom scripts.

- *Diagnose data performance issues.* Regularly review the collected data metrics to pinpoint potential performance bottlenecks or degradation in data operations. Visualization tools or dashboards can be invaluable in this process, helping to highlight trends, bottlenecks, and outliers in data performance. Once identified, delve into the root causes of these issues and strategize appropriate remediation steps.

### Partition data

If you have large datasets or high-volume workloads, consider partitioning your data across multiple storage resources. Partitioning can help distribute the workload and improve parallel processing. You can partition data vertically or horizontally. Horizontal partitioning is also called sharding.

| Strategy |Definition | Example | Use cases|
|---|---|---|---|
| **Vertical partitioning** | Divide a table into smaller tables by selecting specific columns or fields for each partition. Each partition represents a subset of the complete data.| If you have a table with columns A, B, C, and D, you could create one table with columns A and B and another with columns C and D. | - A table contains many columns, but queries don't access all columns together.<br>- Some columns are larger than others and separating them can boost I/O performance.<br>- Different data parts have diverse access patterns. |
| **Horizontal partitioning** | Split data based on rows or ranges of values (also known as sharding). Each partition contains a subset of rows with similar characteristics.| If you have a table with rows 1 to 1000, you might create one partition with rows 1 to 500 and another with rows 501 to 1000.| - A dataset is too large for a single location or server.<br>- Data is accessed based on specific ranges or filters.<br>- Need to distribute the workload across physical nodes or servers for enhanced performance.| 

To partition your data, consider the following steps:

- *Analyze data and queries.* Analyze data and query patterns to identify suitable partitioning or sharding strategies. Understand the nature of the data, access patterns, and distribution requirements.

- *Determine a key.* Choose a partitioning or sharding key to distribute data across partitions or shards. Carefully select the key based on data characteristics and query requirements.

- *Determine logic.* Determine a partitioning or sharding logic based on the chosen key. Consider dividing the data into ranges, applying hashing algorithms, or using other partitioning techniques.

- *Configure the infrastructure.* Configure the database system to support partitioning or sharding. Consider creating the necessary infrastructure, defining the partitions or shards, and configuring the data distribution.

For more information, see [Data partitioning guidance](/azure/architecture/best-practices/data-partitioning).

### Optimize queries

Optimize your database queries by rewriting, reordering, or restructuring them to improve efficiency. Use query optimization techniques, such as index hints, query caching, and query plan analysis. Optimize database queries by implementing the following techniques and considerations:

- *Rewrite queries.* Review and analyze complex queries to identify opportunities to rewrite them. Consider restructuring query logic, eliminating redundant operations, or simplifying query syntax.

- *Avoid the N+1 query problem.* Minimize the number of roundtrips to the database by using joins and batch fetching to retrieve related data efficiently.

- *Reorder joins.* Evaluate the query plan and consider rearranging the join order to minimize the number of rows in each join operation. The order in which you join tables can affect query performance.

- *Use index hints.* Use index hints so a database engine can specify the use of indexes when it runs a query. Index hints guide the optimizer to select the most appropriate indexes.

- *Cache queries.* Store the results of frequently run queries in memory. Query caching eliminates the need for repeatedly running the same query, and it reduces query processing overhead.

- *Optimize locking.* Avoid unnecessary or restrictive lock hints in queries. Efficient locking strategies can enhance query performance and concurrency. Apply optimized locking mechanisms that the database system provides. Analyze and adjust isolation levels to balance data consistency and query performance.

- *Monitor and tune.* Monitor query performance metrics, such as runtime, resource utilization, and query throughput. Use database profiling tools and monitoring functionalities to identify poorly performing queries. Evaluate and fine-tune query plans based on collected performance data. Analyze query plans and wait statistics to identify bottlenecks. Use that information to optimize query performance.

### Optimize indexes

 Indexes increase the speed of data retrieval because the database can quickly locate data based on specific columns or fields. Optimize indexes to improve the efficiency of sorting and join operations, which decreases query runtimes.

 Efficient indexes reduce the number of disk I/O operations that are needed for running queries. Optimize queries to reduce the duration of locks and contention on data pages, which allows multiple users to concurrently access and modify data. When you eliminate redundant or unnecessary indexes, you can make better use of that storage space.

- *Analyze query patterns.* Understand the query patterns that run on your database. Identify the queries that run frequently and might degrade performance. Analyze query patterns to determine which indexes are beneficial for optimizing performance.

- *Evaluate existing indexes.* Review the existing indexes in your database. Evaluate their usage, performance effects, and relevance to the query patterns. Identify redundant or unused indexes that you can remove to improve write performance and reduce storage overhead.

- *Identify columns for indexing.* Identify columns that are frequently used in the *where*, *join*, and *order by* clauses of your queries. These columns are potential candidates for indexing because they can enable fast data retrieval.

- *Choose an appropriate index type.* Select an appropriate index type based on your database system. Common options include b-tree indexes for equality and range queries, hash indexes for exact match queries, and full-text indexes for text search operations. Choose an index type that best matches your query requirements.

- *Consider index column order.* When you create composite indexes, or indexes with multiple columns, consider the order of the columns. Place the columns that are most frequently used in queries at the beginning of the index. Column order helps ensure that your workload is effectively using indexes for a wide range of queries.

- *Balance index size.* Avoid creating indexes on columns with low cardinality, or columns that have a low number of distinct values. Such indexes can be inefficient and increase the size of your database. Instead, index columns that have a high selectivity.

- *Maintain index usage.* Continuously monitor the usage and performance of your indexes. Look for opportunities to create new indexes or modify existing indexes based on changes in query patterns or performance requirements. Remove or update indexes that are no longer beneficial. Indexes have maintenance overhead. As data changes, indexes can fragment and affect performance. Regularly perform index maintenance tasks, such as rebuilding or reorganizing indexes, to ensure optimal performance.

- *Test and validate.* Before you revise indexes in a production environment, perform thorough testing and validation. Measure the performance effect of index revisions by using representative workloads. Verify the improvements against predefined benchmarks.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: B-tree indexes might have high storage overhead, and exact-match queries might be slow. Hash indexes aren't suitable for range queries or comparison operators. Full-text indexes might have high storage requirements, and nontextual data queries might be slow.

### Consider data compression

Data compression is the process of reducing the size of data to optimize storage space and improve workload performance efficiency. Compressed data requires less storage space and less bandwidth for transmitting, which results in fast data transfer.

Compress data to reduce your storage footprint and improve data access times. When you compress data, it reduces I/O operations and network bandwidth requirements. Lossless compression and lossy compression are data compression algorithms. Lossless compression algorithms reduce the size of data without losing any information. Lossy compression algorithms achieve high compression ratios by removing less important or redundant information.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: To compress and decompress data, you need computational resources, like CPU and memory. The more data that you compress, the more resources you need.

### Archive and purge data

Archiving and purging are strategies that streamline data storage. Archiving relocates older, less-frequently accessed data to a more cost-effective storage. Purging data permanently removes redundant data. They contribute to performance efficiency by reducing data volume, increases data access speed, and reducing backup and recovery times:

- *Reducing data volume*: Less data means faster processing times, ensuring quick responses to user requests.
  
- *Increasing data access speed*: A trimmed dataset allows for swifter queries and data retrieval, optimizing system responsiveness.

- *Reducing backup and recovery times*: Smaller datasets expedite backup and restoration processes, minimizing downtime and ensuring consistent performance.

Archiving and purging are instrumental in maintaining peak performance efficiency in data-driven systems.

### Optimize storage load

Optimizing storage load efers to the process of reducing unnecessary or redundant requests to the storage system, streamlining data retrieval processes, and ensuring that the storage workload isn't overwhelmed with excessive demands. This optimization ensures that the storage system can respond to legitimate requests and maintain a high-performance level. Implement strategies that reduce the processing burden on the data store. To optimize data store load, consider the following strategies:

#### Use caching

Caching is a technique where you store frequently accessed data in a high-speed storage layer. It provices for quicker retrieval compared to fetching it from the primary data source. It plays a pivotal role in data performance optimization by reducing data access times and minimizing unnecessary repeated data fetches. Particularly in hot data scenarios, caching can amplify read throughput and enhance client response times. This method proves most effective when dealing with data that remains static or undergoes infrequent changes.

It's crucial to weigh factors such as cache expiration policies, cache eviction strategies, and cache size management. Fine-tune parameters like the time to live (TTL). To use a cache to optimize storage load, consider the following strategies:

- *In-memory caching*: Perform in-memory caching to store frequently accessed data in memory for fast retrieval. You can use this technique for application data that's expensive to compute or retrieve from a database. In-memory caching is useful for data that's read frequently but doesn't frequently change.

- *Database query caching*: Use this technique to cache the results of database queries to avoid running the same query multiple times. Database query caching is useful for complex and time-consuming database queries. When you cache the results of a query, subsequent requests for the same query are returned quickly.

- *Content delivery network caching*: Use this technique to cache web content on distributed network servers to reduce latency and improve content delivery. Content delivery network caching is effective for static content, like images, CSS files, and JavaScript files. Content delivery networks store copies of content in multiple locations worldwide, so users can access the content from a server that's near them geographically.

#### Use read replicas

Many databases support multiple read replicas. Distribute read queries across replicas to minimize the demand on the write database. Each read replica can serve a subset of traffic, which can improve performance.

When you have a workload with multiple data replicas that you expect to stay in sync, it's helpful to model this distributed system by using the PACELC theorem. The PACELC theorem helps you understand latency versus constancy tradeoff choices in the nonpartitioned state of the system. Use this information to help you choose a database engine and data sync strategy that best addresses the system in a partitioned and nonpartitioned state. For more information, see [Command and Query Responsibility Segregation (CQRS) pattern](/azure/architecture/patterns/cqrs).

#### Optimize data consistency

In a distributed workload, where data resides across multiple nodes or locations, the level of consistency you select determines how quickly changes in one location reflect in others. Opting for stricter consistency consumes more compute resources and can negatively affect performance efficiency. On the other hand, a less strict consistency level, like eventual consistency introduces temporary inconsistencies among nodes but can boost performance efficiency. 

Eventual consistency strikes a balance between data accuracy and workload performance. Changes spread gradually instead of instantly, boosting workload responsiveness and data processing speed. Although this introduces short-lived inconsistencies, the workload eventually presents consistent data across all nodes. Choosing eventual consistency can elevate a workload's performance and further enhance its availability and scalability.

### Optimize data updates

You can use optimistic concurrency to handle concurrent updates to the same data. Instead of locking data and preventing other updates, optimistic concurrency allows multiple users or processes to work concurrently and assumes that conflicts are rare.

With optimistic concurrency, each update operation includes a version or timestamp that represents the state of the data at the time of the update. When a conflicting update is detected, the system resolves the conflict by rejecting the update or merging the changes.

Optimistic concurrency minimizes contention and allows concurrent updates to proceed without unnecessary locking. It reduces wait time for resources and provides high throughput.

### Optimize data movement and processing

Optimizing data movement and processing involves improving the efficiency and performance of operations related to data extraction, transformation, loading, and processing. Consider the following key aspects of optimizing data movement and processing:

- *Extract, transform, and load (ETL) optimization*: Optimize ETL processes to minimize processing time. You can streamline the extraction process, implement efficient transformation algorithms, and optimize the loading process. When you make each step efficient, you can optimize the overall workflow.

- *Parallel processing*: Utilize parallel processing techniques to improve performance. When you distribute data processing tasks across multiple threads or nodes, you can divide and process the workload concurrently, which results in fast processing.

- *Batch processing*: Group similar tasks together to reduce overhead that's caused by repeated operations. Process multiple tasks in a batch to reduce overall processing time.

### Optimize storage design

Optimizing storage design entails crafting a precise data storage architecture and selecting appropriate storage technologies. A streamlined storage design enhances data access, retrieval, and manipulation. Through strategic storage design, a workload achieves improved response times and overall functionality.

#### Design for data proximity

Data proximity refers to the strategic placement of data closer to the users or services that access it most frequently. By reducing the physical or logical distance between data and its users, data proximity ensures faster data access and improved responsiveness. To optimize design for close proximity, consider these strategies:

- *Evaluate data access patterns*: Assess your workload's access patterns and frequently accessed data. This analysis can help determine where to place data for maximum benefit.
 
- *Choose solutions that support data relocation*: Consider solutions that offer dynamic data relocation based on changing access patterns, ensuring optimal data positioning at all times.
 
- *Choose solutions that support data synchronization*: If catering to a distributed user base, opt for solutions that facilitate data synchronization across various regions, ensuring that data replicas are available in proximity to users.


> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: If underlying data changes frequently, implement a cache invalidation mechanism to ensure that the cached data remains up to date.

#### Use polyglot persistence

Polyglot persistence is the practice of using multiple data storage technologies to store and manage different types of data within an application or system. Different types of databases or storage solutions serve different data requirements.

Polyglot persistence takes advantage of the benefits of each data storage technology to ensure optimal performance and scalability for each type of data. For example, you might use a relational database to store structured, transactional data. And you might use a NoSQL database to store unstructured or semi-structured data.

Design a schema for each data storage technology based on the requirements of the data. For relational databases, you might create normalized tables with appropriate relationships. For NoSQL databases, you might define document structures or key-value pairs. Develop the necessary components to interact with each data storage technology, such as APIs, data access layers, or data integration pipelines. Ensure that the application can read and write data to the appropriate data stores.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: A data structure that has low normalization can improve performance but introduce complexities.

#### Separate OLTP and OLAP systems

To separate [OLTP](/azure/architecture/data-guide/relational-data/online-transaction-processing) and [OLAP](/azure/architecture/data-guide/relational-data/online-analytical-processing) systems, design and deploy distinct systems for transactional processing and analytical processing tasks. This separation allows you to optimize each system for its specific workload and characteristics.

OLTP systems are used for real-time transactional processing. They efficiently and reliably handle individual transactions. OLTP systems are typically used to perform day-to-day operational tasks, such as online order processing, inventory management, and customer data management. OLTP systems prioritize responsiveness, consistency, and concurrency.

OLAP systems are used for complex analytical processing and reporting. They handle large volumes of data and perform intensive calculations and aggregations. OLAP systems are used for tasks such as business intelligence, data mining, and decision support. OLAP systems prioritize query performance, data aggregation, and multidimensional analysis.

When you separate OLTP and OLAP systems, you can allocate appropriate resources and optimize each system for its specific workload. Separation allows you to apply different data modeling techniques to each system. OLTP systems typically use normalized schemas for efficient transactional processing. OLAP systems might use denormalized schemas or data warehousing techniques to optimize query performance.

## Azure facilitation

**Profile data.** Azure offers tools and services that you can use to profile data, such as Azure Data Factory, Azure Purview, and Azure Synapse Analytics. These tools enable you to extract, transform, and load data from various sources, perform data quality checks, and gain insights into the data.

**Monitor data.** To monitor data performance, you can use Azure Monitor to collect and analyze infrastructure metrics, logs, and application data. You can integrate Monitor with other services like Application Insights, which provides application performance monitoring and supports many platforms.

Application Insights collects usage and performance data. You can use Log Analytics to correlate that data with configuration and performance data across Azure resources.

You can use the insights feature of [Azure SQL](/azure/azure-sql/database/sql-insights-overview) and [Azure Cosmos DB](/azure/cosmos-db/insights-overview) to monitor your database. This feature enables you to diagnose and tune database performance issues.

**Partition data.** Azure offers various partitioning strategies for different data stores. Each data store might have different considerations and configuration options for data partitioning. For more information, see [Data partitioning strategies](/azure/architecture/best-practices/data-partitioning-strategies).

**Optimize queries and indexes.** Use the query performance insight feature of Azure SQL Database to optimize queries, tables, and databases. You can use this feature to identify and troubleshoot query performance issues.

For relational databases, you should follow the [index design guidelines](/sql/relational-databases/sql-server-index-design-guide#General_Design), [SQL Server index guidance](/sql/relational-databases/indexes/indexes), and [Azure Cosmos DB index guidance](/azure/cosmos-db/index-overview). Use SQL Database to perform [automatic tuning](/azure/azure-sql/database/automatic-tuning-overview) for queries to improve their performance.

In SQL databases, you should regularly [reorganize or rebuild indexes](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes). Identify slow queries and tune them to improve performance. Many database engines have query-tuning features. For more information, see [Best practices for query performance](/azure/cosmos-db/nosql/query-metrics#best-practices-for-query-performance).

Azure Cosmos DB has a [default indexing policy](/azure/cosmos-db/index-policy) that indexes every property of every item and enforces range indexes for any string or number. This policy provides you with efficient query performance, and you don't have to manage indexes upfront.

**Choose a data store.** Azure offers many different data stores to fit your workload needs. [Understand data store types](/azure/architecture/guide/technology-choices/data-store-overview) and [select an Azure data store for your application](/azure/architecture/guide/technology-choices/data-store-decision-tree).

**Read replicas.** Many Azure database services support read replicas. The availability and configuration of read replicas vary depending on the Azure database service. Refer to the official documentation for each service to understand the details and options.

## Related links

- [Automatic tuning in SQL Database](/azure/azure-sql/database/automatic-tuning-overview)
- [Azure Cosmos DB](/azure/cosmos-db/insights-overview)
- [Azure Cosmos DB index guidance](/azure/cosmos-db/index-overview)
- [Azure SQL](/azure/azure-sql/database/sql-insights-overview)
- [Best practices for query performance](/azure/cosmos-db/nosql/query-metrics#best-practices-for-query-performance)
- [CQRS pattern](/azure/architecture/patterns/cqrs)
- [Data partitioning guidance](/azure/architecture/best-practices/data-partitioning)
- [Data partitioning strategies](/azure/architecture/best-practices/data-partitioning-strategies)
- [Default indexing policy](/azure/cosmos-db/index-policy)
- [Index design guidance](/sql/relational-databases/sql-server-index-design-guide#General_Design)
- [OLAP overview](/azure/architecture/data-guide/relational-data/online-analytical-processing)
- [OLTP overview](/azure/architecture/data-guide/relational-data/online-transaction-processing)
- [Partitioning best practices](/azure/architecture/best-practices/data-partitioning)
- [Reorganize or rebuild indexes](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)
- [Select an Azure data store for your application](/azure/architecture/guide/technology-choices/data-store-decision-tree)
- [SQL Server index guidance](/sql/relational-databases/indexes/indexes)
- [Understand data store types](/azure/architecture/guide/technology-choices/data-store-overview)

## Performance Efficiency checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Performance Efficiency checklist](checklist.md) 
