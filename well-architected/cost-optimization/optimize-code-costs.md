---
title: Recommendations for optimizing code costs
description: Learn how to optimize costs related to code with specific recommendations.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing code costs

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

| [CO:11](checklist.md) | Optimize code costs. Evaluate and modify code to meet functional and nonfunctional requirements with fewer or cheaper resources. |
|---|---|

This guide describes the recommendations for optimizing code costs. Code optimization is the process of improving the efficiency, performance, and cost-effectiveness of application code. Effective code optimization involves making changes to the code to reduce resource consumption, minimize execution time, and improve overall performance.

By optimizing code, you can identify and eliminate inefficiencies that might lead to increased resource consumption and higher costs. You can reduce processing time, memory usage, and network overhead, which can lead to applications that are faster and more responsive. Improved performance enhances the user experience and enables your system to handle larger workloads efficiently.

**Definitions**

| Term | Definition  |
|---|---|
| Code instrumentation | The practice of adding code snippets or libraries to code that collect data and monitor code performance during runtime.|
| Concurrency | The execution of multiple processes at the same time. |
| Data serialization  | The process of converting data objects into a format that can be stored or transmitted and then reconstructing them back to their original form when needed. |
| Hot paths | Critical or frequently run sections of a program that require high performance and low latency. |

## Key design strategies

Cost optimizing code means improving code to achieve the same functionality with fewer per-instance resources, such as CPU cycles, memory, and storage. By reducing resource consumption, you can save money when applications handle large volumes of data or experience high traffic loads.

Code improvements are most effective when you're following other cost optimization efforts around scaling, rightsizing, redundancy, and throttling. After you take care of these foundational elements, you can consider code optimization.

You might not know if you have inefficient code. Serverless, autoscale, and reliability features can mask code inefficiencies. The following strategies can help you identify and fix application code that costs more than it should.

### Instrument your code

Instrumenting code is the practice of adding code snippets or libraries that collect data and monitor code performance during runtime. It allows developers to gather information about key metrics, such as resource consumption (CPU or memory usage) and execution time. By instrumenting code, developers can gain insights into code hot paths, identify performance bottlenecks, and optimize the code for better efficiency and cost-effectiveness.

In an ideal environment, you should do code analysis early in the software development lifecycle. The earlier you catch a code problem, the cheaper it's to fix.

Automate as much of this code analysis as possible. Use dynamic and static tools for code analysis to reduce the manual effort. However, keep in mind that this testing is still a simulation of production. Production provides the clearest understanding of code optimization.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Code monitoring tools are likely to increase costs.

### Identify and optimize hot paths

By instrumenting your code, you can measure the resource consumption of code paths. These measurements help you identify hot paths. Hot paths have a significant effect on performance and resource usage. They're critical or frequently run sections of a program that require high performance and low latency.

To identify hot paths, consider these tasks:

- *Analyze runtime data*: Collect and analyze runtime data to identify areas of the code that consume significant resources, such as CPU, memory, or I/O operations. Look for patterns or sections of code that are frequently run or take a long time to complete.

- *Measure performance*: Use profiling tools or performance testing frameworks to measure the execution time and resource consumption of code paths. This measurement helps identify bottlenecks and areas for improvement.

- *Consider business logic and user effect*: Evaluate the importance of code paths based on their relevance to the application's functionality or critical business operations. Determine which code paths are crucial for delivering value to users or meeting performance requirements.

  Review performance recommendations that are specific to the programming language you're working with. Evaluate your code against these recommendations to identify areas for improvement. Remove any unnecessary operations within the code path that might affect performance.

- *Remove unnecessary function calls*: Review your code. Identify any functions that aren't essential for the desired functionality and might negatively affect performance. For example, if a function call performs a validation that happened earlier in the code, you can remove that unnecessary function call.

- *Minimize logging operations*: Logging can be helpful for debugging and analysis, but excessive logging can affect performance. Evaluate the necessity of each logging operation and remove any unnecessary logging calls that aren't critical for performance analysis.

- *Optimize loops and conditionals*: Analyze loops and conditionals in your code. Identify any unnecessary iterations or conditions that you can eliminate. Simplifying and optimizing these structures can improve the performance of your code.

- *Reduce unnecessary data processing*: Review your code for any unnecessary data-processing operations, such as redundant calculations or transformations. Eliminate these unnecessary operations to improve the efficiency of your code.

- *Minimize network requests*: If your code makes network requests, minimize the number of requests and optimize their usage. Batch requests when possible and avoid unnecessary round trips to improve performance.

- *Minimize allocations*: Identify areas where excessive memory allocation occurs. Optimize the code by reducing unnecessary allocations and reusing existing resources when possible.

  By minimizing allocations, you can improve memory efficiency and overall performance. Use the appropriate memory management and garbage collection strategies for your programming language.

- *Reduce data structure size*: Assess the size of your data structures, such as classes, and identify areas where reduction is possible. Review the data requirements and eliminate any unnecessary fields or properties. Optimize memory usage by selecting appropriate data types and packing data efficiently.

- *Assess cross-cutting implementations*: Consider the effects of cross-cutting implementations, such as middleware or token checks. Assess whether they're negatively affecting performance.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Optimizing code and hot paths requires developer expertise in identifying code inefficiencies. These highly skilled individuals might need to spend time on other tasks.

### Evaluate the use of concurrency

Evaluating the use of concurrency involves assessing whether asynchronous processing, multithreading, or multiprocessing can maximize resource utilization and reduce expenses. By using asynchronous processing, multithreading, or multiprocessing, you can handle more tasks with the same resources. However, it's crucial to ensure proper implementation to avoid more overhead and to maintain cost-effectiveness.

To evaluate whether using concurrency is a good fit, you can follow these guidelines:

- *Asynchronous processing*: Asynchronous processing allows nonblocking execution. For example, you can start a process and then pause it to allow a second process to finish.

  Determine the code components or operations that you can run asynchronously. Identify the programming language or framework that you're using and understand the asynchronous programming model that it supports, such as `async`/`await` in .NET or promises in JavaScript.

  Restructure your code to use asynchronous programming constructs by enabling nonblocking execution of tasks. Decouple long-running or I/O-intensive operations from the main execution thread by using asynchronous methods or callbacks. Use asynchronous APIs or libraries that your programming language or framework provides to handle asynchronous workflows.

- *Multithreading*: In multithreading, you run multiple threads of a single process concurrently.

  Identify sections of your code that you can run concurrently and independently. Read documentation or guidelines that are specific to the programming language or framework you're using for multithreading best practices. Create multiple threads or thread pools to handle parallel execution of tasks.

  Implement synchronization mechanisms, such as locks, mutexes, or semaphores, to ensure thread safety and prevent race conditions when code accesses shared resources. Consider using higher-level abstractions, like thread pools or task-based parallelism libraries, to streamline the management of multiple threads and simplify concurrency control.

- *Multiprocessing*: Multiprocessing can have processes run in parallel. It can provide better utilization of multiple CPU cores than multithreading.

  Determine whether the workload or operations in your code lend themselves to parallel processing. Identify the programming language or framework that you're using and explore its multiprocessing capabilities. For example, consider the multiprocessing module in Python or parallel streams in Java. Design your code to split the workload into multiple independent tasks that can be processed concurrently.

  Use multiprocessing APIs or libraries to create and manage parallel processes. Distribute the workload among these APIs or libraries. To enable coordination and data sharing among multiple processes, implement communication mechanisms like interprocess communication (IPC), shared memory, or message passing, depending on your programming language or framework.

### Use the right SDKs

For cost optimization, select SDKs that are designed to optimize resource usage and improve performance. It's important to evaluate the features and capabilities of each SDK. Consider its compatibility with your programming language and development environment.

Here's guidance to help choose the best SDKs for your workload:

- *Conduct performance testing*: Compare the resource usage and performance of SDKs through performance testing. Choose the SDK that best meets your needs in terms of resource optimization and performance improvement. Integrate the chosen SDK into your codebase by following the provided documentation and guidelines.

- *Monitor resource usage and optimize code*: Monitor resource usage with the implemented SDK. Gather insights from monitoring and analysis to optimize your code.

### Choose the right operating system

Most coding languages can run on various operating systems, so it's important to evaluate your operating system against cheaper alternatives. If an alternative operating system supports the same or similar functionality at less cost, it's worth considering. By choosing a cheaper operating system, you can potentially reduce the cost of licensing fees and infrastructure costs.

The right operating system can contribute to overall cost optimization for your workload. To choose the right operating system for your workload, try these activities:

- *Evaluate your requirements*: Understand the specific needs of your workload, including the coding languages and frameworks that you're using. Consider any dependencies or integrations with other systems.

- *Consider compatibility*: Ensure the operating system you choose is compatible with your coding languages, frameworks, and any third-party libraries or tools you use. Check the documentation and community support for the operating system to ensure that it has good compatibility with your technology stack.

- *Assess functionality*: Determine if the alternative operating system supports the same or similar functionality as your current operating system. Evaluate whether it provides the necessary features and capabilities that your workload requires.

- *Compare costs*: Compare the costs associated with operating systems. Consider factors such as licensing fees, support costs, and infrastructure requirements. Look for cheaper alternatives that can meet your workload's requirements without compromising functionality.

- *Consider performance and optimization*: Evaluate the performance and optimization capabilities of the alternative operating system. Look for benchmarks, case studies, or performance comparisons to understand how it performs in real-world scenarios.

- *Review security and stability*: Assess the security and stability of the alternative operating system. Look for security updates, patches, and community support to ensure that the operating system is actively maintained and is secure and stable overall.

- *Consider vendor support*: Evaluate the level of vendor support that's available for the alternative operating system. Check if there are official support channels, documentation, and a community of users who can provide assistance if you need it.

### Optimize network traversal

Optimizing network traversal is about minimizing network traffic between workload components. Data transfer often has an associated cost. By minimizing network traffic, you can reduce the amount of data that needs to be transferred while lowering costs.

Analyze your workload and identify any unnecessary data transfers between components. Avoid transferring redundant or duplicate data, and transmit only essential information. For example, if a component repeatedly requests the same data from another component, it's a candidate for optimization. You can refactor your code to reduce unnecessary calls or to batch requests, minimizing the data transferred. Applications might send entire objects or data structures when only a few fields are needed. By optimizing the code to send only the required data, you minimize the size of each data transfer.

#### Optimize network protocols

Network protocols play a crucial role in the efficiency of network communication. By optimizing network protocols, you can improve the overall efficiency of data transfer and reduce resource consumption.

Consider these suggestions:

- *Choose efficient protocols*: Select protocols that are known for their efficiency in terms of data transfer speed and minimizing overhead. For example, consider using protocols like HTTP/2 over HTTP/1.1. These protocols are designed to improve performance by reducing latency and optimizing data transfer. Use libraries and frameworks in your application to use these protocols.

- *Support compression*: Implement compression mechanisms in your network protocols to reduce the size of data being transferred. Compression can significantly reduce the amount of data transmitted over the network, leading to improved performance and reduced bandwidth usage. Server-side compression is typically enabled in the application code or server configuration.

- *Utilize connection pooling*: Connection pooling allows for the reuse of established network connections to reduce the overhead of establishing new connections for each request. Connection pooling can improve the efficiency of network communication by avoiding the overhead of connection setup and teardown. Choose a connection pooling library or framework and configure to meet workload needs.

- *Implement other optimizations*: Explore other optimizations that are specific to your workload and network environment. For example, you can use content caching, load balancing, and traffic shaping to further optimize network traversal.

#### Minimize network overhead

Minimize the amount of network traffic and data transfer between components of your workload. By reducing network overhead, you can lower costs associated with data egress and ingress and improve overall network performance.

Consider these techniques:

- *Reduce redundant requests*: Analyze the code to identify any duplicate or unnecessary requests. Instead of making multiple requests for the same data, you can modify the code to retrieve the data once and reuse it as needed.

- *Optimize data size*: Review the data being transmitted between components or systems, and look for opportunities to minimize its size. Consider techniques such as compressing the data before transmission or using more efficient data formats. By reducing the data size, you can decrease network bandwidth usage and improve overall efficiency.

- *Batch requests*: If applicable, consider batching multiple smaller requests into a single larger request. Batching reduces the overhead of establishing multiple connections and decreases the overall data transmission.

- *Use data serialization*: Data serialization is the process of converting complex data structures or objects into a format that can be easily transmitted over a network or stored in a persistent storage system. This strategy involves representing the data in a standardized format, so the data can be efficiently transmitted, processed, and reconstructed at the receiving end.

  Select a serialization format that's compact, fast, and suitable for your workload's requirements.

  | Serialization format | Description |
  |----|---|
  | Protocol Buffers (protobuf) | A binary serialization format that offers efficient encoding and decoding of structured data. It uses typed definition files to define message structures.|
  | MessagePack| A binary serialization format for compact transmission over the wire. It supports various data types and provides fast serialization and deserialization performance.|
  | JavaScript Object Notation (JSON) | A widely used data serialization format that's human-readable and easy to work with. JSON is text based and has broad cross-platform support.|
  | Binary JSON (BSON)| A binary serialization format that's similar to JSON but designed for efficient serialization and deserialization. BSON includes extra data types that aren't available in JSON.|

  Depending on the serialization format, you need to implement logic to serialize objects or data structures into the chosen format and deserialize them back into their original form. You can implement this logic by using libraries or frameworks that provide serialization capabilities for the format.

### Optimize data access

Optimizing data access refers to streamlining the patterns and techniques for retrieving and storing data, to minimize unnecessary operations. When you optimize data access, you can save costs by reducing resource usage, reducing data retrieval, and improving the efficiency of data processing. Consider techniques such as data caching, efficient data querying, and data compression.

#### Use caching mechanisms

Caching involves storing frequently accessed data closer to the components that require it. This technique reduces the need for network traversal by serving the data from the cache instead of fetching it over the network.

Consider these caching mechanisms:

- *Use an external cache*: One popular caching solution is a content delivery network. It helps minimize latency and reduce network traversal by caching static content closer to consumers.

- *Tune caching parameters*: Configure caching parameters, such as time to live (TTL), to optimize the benefit of caching while minimizing potential drawbacks. Setting an appropriate TTL ensures that cached data remains fresh and relevant.

- *Use in-memory caching*: In addition to external caching solutions, consider implementing in-memory caching in your application. In-memory caching can help utilize idle compute resources and increase the compute density of allocated resources.

#### Optimize database traffic

You can enhance the efficiency of application communication to the database. Here are some key considerations and techniques for optimizing database traffic:

- *Create indexes*: Indexing is the process of creating data structures that improve the speed of data retrieval. By creating indexes on frequently queried columns, you can significantly reduce the time it takes to run queries. For example, if you have a table of users with a column for usernames, you can create an index on the username column to speed up queries that search for specific usernames.

  Identify the most frequently accessed columns and create indexes on those columns to speed up data retrieval. Regularly analyze and optimize the existing indexes to ensure that they're still effective. Avoid over-indexing because it can negatively affect insert and update operations.

- *Optimize queries*: Design efficient queries by considering the specific data requirements and minimizing unnecessary data retrieval. Start by using appropriate join types (for example, inner join and left join), based on the relationship between tables. Use query optimization techniques such as query hints, query plan analysis, and query rewriting to improve performance.

- *Cache query results*: You can store the results of frequently run queries in memory or a cache. Subsequent executions of the same query can then be served from the cache, which eliminates the need for expensive database operations.

- *Use an object-relational mapping (ORM) framework:* Use ORM features such as lazy loading, caching, and batch processing to optimize data retrieval and minimize database round trips. Use ORM frameworks such as Entity Framework for C# or Hibernate for Java.

- *Optimize stored procedures*: Analyze and optimize the logic and performance of stored procedures. The goal is to avoid unnecessary computations or redundant queries in stored procedures. Optimize the use of temporary tables, variables, and cursors to minimize resource consumption.

#### Organize data

Organizing data for efficient access and retrieval involves structuring and storing data in a way that maximizes performance and minimizes resource consumption. It can improve query response times, reduce data transfer costs, and optimize storage utilization.

Here are some techniques for organizing data efficiently:

- *Partition*: Partitioning involves dividing a large dataset into smaller, more manageable subsets called partitions. You can store each partition separately to allow for parallel processing and improved query performance. For example, you can partition data based on a specific range of values or by distributing data across servers. This technique can enhance scalability, reduce contention, and optimize resource utilization.

- *Shard*: Sharding is a technique of horizontally dividing data across multiple database instances or servers. Each shard contains a subset of the data, and queries can be processed in parallel across these shards. Sharding can improve query performance by distributing the workload and reducing the amount of data that each query accesses.

- *Compress*: Data compression involves reducing the size of data to minimize storage requirements and improve the efficiency of data transfer. Because compressed data takes up less disk space, it allows for savings in storage costs. Compressed data can also be transferred more quickly over networks and reduce data transfer costs.

For example, consider a scenario where you have a large dataset of customer information. By partitioning the data based on customer regions or demographics, you can distribute the workload across multiple servers and improve query performance. You can also compress the data to reduce storage costs and improve the efficiency of data transfer.

### Optimize architecture

Evaluate your workload architecture to identify opportunities for resource optimization. The goal is to use the right services for the right job.

To reach this goal, you might need to redesign parts of the architecture to use fewer resources. Consider serverless or managed services, and optimize resource allocation. By optimizing your architecture, you can meet the functional and nonfunctional requirements while consuming fewer per-instance resources.

#### Use design patterns

Design patterns are reusable solutions that help developers solve recurring design problems. They provide a structured approach to designing code that's efficient, maintainable, and scalable.

Design patterns help optimize the use of system resources by providing guidelines for efficient resource allocation and management. For example, the Circuit Breaker pattern helps prevent unnecessary resource consumption by providing a mechanism to handle and recover from failures in a controlled manner.

Design patterns can help cost optimize code in the following ways:

- *Reduced development time*: Design patterns provide proven solutions to common design problems, which can save development time. By following established patterns, developers can avoid repetitive work and focus on implementing the specific requirements of their applications.

- *Improved maintainability*: Design patterns promote modular and structured code that's easier to understand, modify, and maintain. They can lead to cost savings in terms of reduced debugging and maintenance efforts.

- *Scalability and performance*: Design patterns help in designing scalable and performant systems. Patterns like the Cache-Aside pattern can improve performance by caching frequently accessed data to reduce the need for expensive computations or external calls.

To implement design patterns, developers need to understand the principles and guidelines of each pattern and apply them in the code. Consider identifying the appropriate pattern for a problem, understanding its structure and components, and integrating the pattern into the overall design.

Various resources are available, such as documentation, tutorials, and sample code. These resources can help developers learn and implement design patterns effectively.

#### Change configurations

Regularly review and update your workload configuration to ensure that it aligns with your current requirements. Consider adjusting resource sizing and configuration settings based on workload demands. By optimizing configurations, you can effectively allocate resources and avoid overprovisioning to save costs.

#### Refactor architecture

Evaluate your workload architecture and identify opportunities for refactoring or redesigning components to optimize resource consumption. Consider techniques such as adopting a microservices architecture, implementing the Circuit Breaker pattern, and using serverless computing. By optimizing your architecture, you can achieve better resource utilization and cost efficiency.

#### Modify resource sizes

Continuously monitor and analyze the resource utilization of your workload. Based on the observed patterns and trends, adjust resource sizing and configuration settings to optimize resource consumption.

Consider rightsizing virtual machines, adjusting memory allocation, and optimizing storage capacity. By rightsizing resources, you can avoid unnecessary costs associated with underutilization or overprovisioning.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Reworking code and architecture might not fit with current project schedules and could lead to schedule and cost slippage.

## Azure facilitation

**Instrumenting code**: Azure provides monitoring and logging tools like [Azure Monitor](/azure/azure-monitor/overview), [Application Insights](/azure/azure-monitor/app/app-insights-overview), and [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview). You can use these tools to track and analyze the performance and behavior of your code in real time.

**Identifying hot and optimize paths**: Application Insights and [Application Insights Profiler](/azure/azure-monitor/profiler/profiler-overview) help identify and optimize the hot paths in your code by analyzing execution times and resource usage. You can minimize unnecessary memory allocations and optimize memory usage with Profiler.

**Using the right SDKs**: Azure offers [SDKs](https://azure.microsoft.com/downloads/) in multiple programming languages, optimized for performance and ease of use. These SDKs provide prebuilt functions and libraries that interact with Azure services to reduce the need for custom implementation.

**Optimizing network traversal**: Various Azure services support high-speed network protocols like [HTTP/2](/azure/application-gateway/features#websocket-and-http2-traffic) and [QUIC](/azure/storage/files/storage-files-networking-overview#smb-over-quic) for efficient communication between services and applications.

Azure services, such as [Azure Database for PostgreSQL - Flexible Server](/azure/postgresql/flexible-server/concepts-connection-pooling-best-practices), support [connection pooling](/azure/app-service/troubleshoot-intermittent-outbound-connection-errors#modify-the-application-to-use-connection-pooling).

Azure supports batch processing in various services, so you can group multiple operations together and run them in a single request. Batch processing can significantly improve efficiency and reduce network overhead.

Regarding data serialization, Azure supports various serialization formats, including JSON and XML. Choose the appropriate serialization format based on data size, performance requirements, and interoperability needs.

**Optimizing data access**: Azure provides caching services like Azure Cache for Redis. You can use caching to store frequently accessed data closer to the application, which results in faster retrieval and reduced back-end load.

- *Indexing and query optimization:* Azure services like [Azure SQL Database](/azure/azure-sql/database/automatic-tuning-overview) and [Azure Cosmos DB](/azure/cosmos-db/index-policy) provide indexing capabilities to optimize query performance. By choosing the right indexing strategy and optimizing queries, you can improve the overall efficiency of data retrieval.

- *Object-relational mapping (ORM):* Azure supports ORM frameworks like Entity Framework. These frameworks simplify data access and mapping between object-oriented code and relational or NoSQL databases.

- *Optimizing stored procedures:* You can use Azure services like [Azure SQL Database](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) to create and optimize stored procedures. Stored procedures can enhance performance by reducing network round trips and precompiling SQL statements.

- *Partitioning and sharding:* Azure offers partitioning and sharding capabilities in services like [Azure Cosmos DB](/azure/cosmos-db/partitioning-overview) and [Azure SQL Database](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes). You can use partitioning to distribute data across multiple nodes for scalability and performance optimization.

- *Compressing data:* Azure services support data compression techniques like GZIP and DEFLATE.

**Optimizing architecture**: Azure provides architectural guidance and design patterns for designing scalable, resilient, and performant applications. For more information, see [Design patterns](/azure/architecture/patterns/).

## Related links

- [Azure Monitor](/azure/azure-monitor/overview)
- [Application Insights](/azure/azure-monitor/app/app-insights-overview)
- [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview)
- [Application Insights Profiler](/azure/azure-monitor/profiler/profiler-overview)
- [Connection pooling](/azure/app-service/troubleshoot-intermittent-outbound-connection-errors#modify-the-application-to-use-connection-pooling)
- [Azure Database for PostgreSQL - Flexible Server connection pooling](/azure/postgresql/flexible-server/concepts-connection-pooling-best-practices)
- [Azure SQL Database index tuning](/azure/azure-sql/database/automatic-tuning-overview)
- [Azure Cosmos DB indexing policies](/azure/cosmos-db/index-policy)
- [Azure Cosmos DB partitioning](/azure/cosmos-db/partitioning-overview)
- [Azure SQL Database partitioning](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes)

## Cost Optimization checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
