---
title: Recommendations for optimizing code
description: Learn how to optimize costs related to code.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing code: CO 11

This guide describes the recommendations for optimizing costs related to code. Code optimization is the process of improving the efficiency, performance, and cost-effectiveness of application code. It involves making changes to the code to reduce resource consumption, minimize execution time, and improve overall performance. By optimizing code, you can identify and eliminate inefficiencies that may lead to increased resource consumption and higher costs. You can reduce processing time, memory usage, and network overhead, which can lead to faster and more responsive applications. Improved performance not only enhances the user experience but also enables your system to handle larger workloads efficiently.

**Definitions**

| Term | Definition  |
|---|---|
| Code instrumentation | The practice of adding code snippets or libraries to code that collect data and monitor code performance during runtime.|
| Hot paths | Critical or frequently executed sections of a program that require high performance and low latency. |
| Concurrency | The execution of multiple processes at the same time. |
| Data serialization  | The process of converting data objects into a format that can be stored or transmitted and then reconstructing them back to their original form when needed. |

## Key strategies

Optimizing code for cost optimization means improving code to achieve the same functionality with less. The goal is to use less per-instance resources, such as CPU cycles, memory, and storage. By reducing resource consumption, you can save money when applications handle large volumes of data or experience high traffic loads.

Code improvements are most effective when following other cost optimization efforts around scaling, right-sizing, redundancy, and throttling. Once these foundational elements are taken care of, then you can consider code optimization. You might not know if you have inefficient code. Serverless, autoscale, and reliability features can mask code inefficiencies. The following strategies can help you uncover and fix code that is costing you more than it should.

### Instrument your code

Instrumenting code refers to the practice of adding code snippets or libraries to code that collect data and monitor code performance during runtime. Code instrumentation allows developers to gather information about key metrics such as resource consumption (CPU, memory usage) and execution time. By instrumenting code, developers can gain insights into code hot paths, identify performance bottlenecks, and optimize the code for better efficiency and cost-effectiveness.

In an ideal environment, you should do code analysis early in the software development lifecycle. The earlier you catch a code issue, the cheaper it's to fix it. You want to automate as much of this code analysis as possible. Use dynamic and static code analysis tools to reduce the manual effort. However, keep in mind that this testing is still a simulation of production. Production provides the clearest understanding of code optimization.

:::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** Code monitoring tools are likely to increase costs.

### Identify and optimize hot paths

By instrumenting your code, you can measure the resource consumption for different code paths. These measurements help you identify hot paths. Hot paths have a significant effect on performance and resource usage. They are critical or frequently executed sections of a program that require high performance and low latency. To identify code hot paths, consider these steps:

- *Analyze runtime data*: Collect runtime data and analyze it to identify areas of the code that consume significant resources, such as CPU, memory, or I/O operations. Look for patterns or sections of code that are frequently executed or take a long time to complete.
- *Measure performance*: Use profiling tools or performance testing frameworks to measure the execution time and resource consumption of different code paths. It helps identify bottlenecks and areas for improvement.
- *Consider business logic and user effect*: Evaluate the importance of different code paths based on their relevance to the application's functionality or critical business operations. Determine which code paths are crucial for delivering value to users or meeting performance requirements.

Review the performance recommendations specific to the programming language you're working with. Evaluate your code against these recommendations to identify areas for improvement. You should remove any unnecessary operations within the code path that may affect performance.

- *Remove unnecessary function calls*: Review your code and identify any functions that aren't essential for the desired functionality and may affect performance negatively. For example, if a function call performs a validation that has already been done earlier in the code, you can remove that unnecessary validation function call.
- *Minimize logging operations*: Logging can be helpful for debugging and analysis, but excessive logging can affect performance. Evaluate the necessity of each logging operation and remove any unnecessary logging calls that aren't critical for performance analysis.
- *Optimize loops and conditionals*: Analyze loops and conditionals in your code and identify any unnecessary iterations or conditions that can be eliminated. Simplifying and optimizing these structures can improve the performance of your code.
- *Reduce unnecessary data processing*: Review your code for any unnecessary data processing operations, such as redundant calculations or transformations. Eliminate these unnecessary operations to improve the efficiency of your code.
- *Minimize network requests*: If your code involves making network requests, minimize the number of requests and optimize their usage. Batch requests when possible and avoid unnecessary round trips to improve performance.
- *Minimize allocations*: Identify areas where excessive memory allocation occurs. Optimize the code by reducing unnecessary allocations and reusing existing resources when possible. By minimizing allocations, you can improve memory efficiency and overall performance. Use the appropriate memory management and garbage collection strategies for your programming language.
- *Reduce data structure size*: Assess the size of your data structures, such as classes, and identify areas where reduction is possible. Review the data requirements and eliminate any unnecessary fields or properties. Optimize memory usage by selecting appropriate data types and packing data efficiently.
- *Cross-cutting implementation*: Consider the effects of cross-cutting implementations, such as middleware or token checks, and assess if they're affecting performance negatively.

:::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** Optimizing code and hot paths requires developer expertise in identifying code inefficiencies is subjective and might be highly skilled individual required for other tasks.

### Consider concurrency

Evaluate if using asynchronous processing, multithreading, or multiprocessing can improve performance in your specific scenario. Identify code segments that can be transformed into asynchronous operations to allow parallel execution and better resource utilization. Be cautious of thread-safety and ensure proper synchronization when working with shared resources. To implement asynchronous processing, multithreading, or multiprocessing, you can follow these guidelines:

- *Asynchronous processing*: Determine the components or operations in your code that can be executed asynchronously. Identify the programming language or framework you're using and understand the asynchronous programming model it supports, such as async/await in .NET or promises in JavaScript. Restructure your code to use asynchronous programming constructs, enabling nonblocking execution of tasks. Decouple long-running or I/O-intensive operations from the main execution thread by using asynchronous methods or callbacks. Utilize asynchronous APIs or libraries provided by your programming language or framework to handle asynchronous workflows.
- *Multithreading*: Identify sections of your code that can be executed concurrently and independently. Read the documentation or guidelines specific to the programming language or framework you're using for multithreading best practices. Create multiple threads or thread pools to handle parallel execution of tasks. Implement synchronization mechanisms, such as locks, mutexes, or semaphores, to ensure thread safety and prevent race conditions when accessing shared resources. Consider using higher-level abstractions, like thread pools or task-based parallelism libraries, to streamline the management of multiple threads and simplify concurrency control.
- *Multiprocessing*: Determine whether the workload or operations in your code lend themselves to parallel processing. Identify the programming language or framework you're using and explore its multiprocessing capabilities, such as the multiprocessing module in Python or parallel streams in Java. Design your code to split the workload into multiple independent tasks that can be processed concurrently. Use multiprocessing APIs or libraries to create and manage parallel processes, distributing the workload among them. Implement communication mechanisms, such as interprocess communication (IPC), shared-memory, or message passing, depending on your programming language or framework, to enable coordination and data sharing between multiple processes.

### Use the correct software development kits (SDKs)

Picking the correct SDKs for cost optimization involves selecting software development kits that are designed to optimize resource usage and improve performance. It's important to evaluate the features and capabilities of each SDK, considering compatibility with your programming language and development environment. Here's guidance to help pick the best SDKs for your workload:

- *Research and identify available SDKs*: Explore the SDKs designed to optimize resource usage and improve performance. SDKs that cloud service providers offer a standardized way to interact with cloud services. Look for SDKs that minimize CPU, memory, and network resource consumption, leading to cost savings and improved performance. Check for built-in resiliency features in the SDKs, such as automatic retries and error handling mechanisms. Take advantage of regular updates to the SDKs, which include new features, bug fixes, and performance improvements.
- *Evaluate features and capabilities*: Assess the features and capabilities of each SDK to ensure they align with your specific requirements. Consider compatibility with your programming language and development environment. Utilize documentation and community support to ensure you have the necessary resources for implementation and troubleshooting.
- *Conduct performance testing*: Compare the resource usage and performance of different SDKs through performance testing. Choose the SDK that best meets your needs in terms of resource optimization and performance improvement. Integrate the chosen SDK into your codebase following the documentation and guidelines provided.
- *Monitor resource usage and optimize code*: Monitor the resource usage with the implemented SDK. Gather insights from monitoring and analysis to optimize your code.

### Choose the right operating system

Most coding languages can run on different operating systems, so it's important to evaluate your operating system against cheaper alternatives. If an alternative operating system supports the same or similar functionality, it's worth considering using the cheaper option. By choosing a cheaper operating system, you can potentially reduce the cost of licensing fees and infrastructure costs associated with the operating system. The right operating system can contribute to overall cost optimization for your workload. To choose the right operating system for your workload, you can follow these steps:

- *Evaluate your requirements*: Understand the specific needs of your workload, including the coding languages and frameworks you're using. Consider any dependencies or integrations with other systems.
- *Consider compatibility*: Ensure that the operating system you choose is compatible with your coding languages, frameworks, and any third-party libraries or tools you rely on. Check the documentation and community support for your chosen operating system to ensure it has good compatibility with your technology stack.
- *Assess functionality*: Determine if the alternative operating system supports the same or similar functionality as your current operating system. Evaluate if it provides the necessary features and capabilities required by your workload.
- *Compare costs*: Compare the costs associated with different operating systems. Consider factors such as licensing fees, support costs, and infrastructure requirements. Look for cheaper alternatives that can meet your workload's requirements without compromising functionality.
- *Consider performance and optimization*: Evaluate the performance and optimization capabilities of the alternative operating system. Look for benchmarks, case studies, or performance comparisons to understand how it performs in real-world scenarios.
- *Review security and stability*: Assess the security and stability of the alternative operating system. Look for security updates, patches, and community support to ensure that it's actively maintained and has a good track record in terms of security and stability.
- *Consider vendor support*: Evaluate the level of vendor support available for the alternative operating system. Check if there are official support channels, documentation, and a community of users who can provide assistance if needed.

### Optimize network traversal

Optimizing network traversal is about minimizing network traffic between workload components. Data transfer often has an associated cost. By minimizing network traffic, you can reduce the amount of data that needs to be transferred while lowering costs. You can reduce unnecessary data transfers, optimize network protocols, and apply caching mechanisms. You should analyze your workload and identify any unnecessary data transfers between components. Avoid transferring redundant or duplicate data and only transmit essential information.

**Optimize network protocols.** Network protocols play a crucial role in the efficiency of network communication. By optimizing network protocols, you can improve the overall efficiency of data transfer and reduce resource consumption. You should choose network protocols that minimize overhead and maximize data transfer speed. Consider using protocols that support compression, connection pooling, and other optimizations.

- *Choose efficient protocols*: Select protocols that are known for their efficiency in terms of data transfer speed and minimizing overhead. For example, consider using protocols like HTTP/2 or QUIC. These protocols are designed to improve performance by reducing latency and optimizing data transfer.
- *Support compression*: Implement compression mechanisms in your network protocols to reduce the size of data being transferred. Compression can significantly reduce the amount of data transmitted over the network, leading to improved performance and reduced bandwidth usage.
- *Utilize connection pooling*: Connection pooling allows for the reuse of established network connections, reducing the overhead of establishing new connections for each request. Connection pooling can improve the efficiency of network communication by avoiding the overhead of connection setup and teardown.
- *Implement other optimizations*: Explore other optimizations specific to your workload and network environment. For example, you can use techniques like content caching, load balancing, and traffic shaping to further optimize network traversal.

**Minimize network overhead.** Minimize the amount of network traffic and data transfer between different components of your workload. Consider techniques such as batching requests, using efficient data serialization formats. By reducing network overhead, you can lower costs associated with data egress and ingress and improve overall network performance.

- *Reduce redundant requests*: Analyze the code to identify any duplicate or unnecessary requests being made. Instead of making multiple requests for the same data, you can modify the code to fetch the data once and reuse it as needed.
- *Optimize data size*: Review the data being transmitted between different components or systems and look for opportunities to minimize its size. Consider techniques such as compressing the data before transmission or using more efficient data formats. By reducing the data size, you can decrease network bandwidth usage and improve overall efficiency.
- *Batch*: If applicable, consider batching multiple smaller requests into a single larger request. Batching reduces the overhead of establishing multiple connections and decreases the overall data transmission.
- *Use data serialization*: Data serialization refers to the process of converting complex data structures or objects into a format that can be easily transmitted over a network or stored in a persistent storage system. It involves representing the data in a standardized format, allowing it to be efficiently transmitted, processed, and reconstructed at the receiving end. Select a serialization format that is compact, fast, and suitable for your workload's requirements.

| Serialization format | Description |
|----|---|
| Protocol Buffers (protobuf) | A binary serialization format that offers efficient encoding and decoding of structured data. It uses typed definition files to define message structures.|
| MessagePack| A binary serialization format designed for compact transmission over the wire. It supports various data types and provides fast serialization and deserialization performance.|
| JSON (JavaScript Object Notation) | A widely used data serialization format that is human-readable and easy to work with. JSON is text-based and has broad cross-platform support.|
| BSON (Binary JSON)| A binary serialization format similar to JSON but designed for efficient serialization and deserialization. BSON includes extra data types not available in JSON.|

Depending on the chosen serialization format, you need to implement the necessary logic to serialize objects or data structures into the chosen format and deserialize them back into their original form. You can do this using libraries or frameworks that provide serialization capabilities for the selected format.

### Optimize data access

Optimizing data access refers to streamlining the patterns and techniques used to retrieve and store data to minimize unnecessary operations and improve efficiency. By optimizing data access, you can reduce resource usage, data retrieval, and improve data processing efficiency, resulting in cost savings. Consider techniques such as data caching, efficient data querying, and data compression.

**Use caching mechanisms.** Caching involves storing frequently accessed data closer to the components that require it, reducing the need for network traversal by serving the data from the cache instead of fetching it over the network.

- *Use an external cache*: Implement a caching solution that allows you to store frequently accessed data closer to the components that require it. A popular caching solution is a Content Delivery Network (CDN), which helps minimize latency and reduce network traversal by caching static content closer to consumers.
- *Tune caching parameters*: Configure caching parameters, such as Time to Live (TTL), to optimize the benefit of caching while minimizing potential drawbacks. Setting an appropriate TTL ensures that cached data remains fresh and relevant.

- *Use in-memory caching*: In addition to external caching solutions, consider implementing in-memory caching within your application. In-memory caching can help utilize idle compute resources and increase the compute density of allocated resources.

**Optimize database traffic.** Optimizing database traffic uses techniques to enhance the efficiency of application communication to the database. Here are some key considerations and techniques for optimizing database traffic:

- *Index*: Indexing is the process of creating data structures that improve the speed of data retrieval operations. By creating indexes on frequently queried columns, you can significantly reduce the time it takes to execute queries. For example, if you have a "users" table with a column for usernames, you can create an index on the "username" column to speed up queries that search for specific usernames. Identify the most frequently accessed columns and create indexes on those columns to speed up data retrieval. Regularly analyze and optimize the existing indexes to ensure they're still effective. Avoid over-indexing because it can negatively affect insert and update operations.

- *Optimize queries*: Design efficient queries by considering the specific data requirements and minimizing unnecessary data retrieval. Start by using appropriate join types (for example, inner join, left join) based on the relationship between tables. Utilize query optimization techniques such as query hints, query plan analysis, and query rewriting to improve performance.

- *Cache query results*: Caching query results involves storing the results of frequently executed queries in memory or a cache. It allows subsequent executions of the same query to be served from the cache, eliminating the need for expensive database operations.
- *Use an object-relational mapping (ORM) framework:* Use ORM features such as lazy loading, caching, and batch processing to optimize data retrieval and minimize database round trips. You should use ORM frameworks such as Microsoft's Entity Framework for C# or Hibernate for Java.
- *Optimize stored procedures*: Analyze and optimize the logic and performance of stored procedures. The goal is to avoid unnecessary computations or redundant queries within stored procedures. Optimize the use of temporary tables, variables, and cursors to minimize resource consumption.

**Organize data.** Organizing data for efficient access and retrieval involves structuring and storing data in a way that maximizes performance and minimizes resource consumption. It can improve query response times, reduce data transfer costs, and optimize storage utilization. Here are some techniques for organizing data efficiently:

- *Partition*: Partitioning involves dividing a large dataset into smaller, more manageable subsets called partitions. Each partition can be stored separately, allowing for parallel processing and improved query performance. For example, you can partition data based on a specific range of values or by distributing data across different servers. This technique can enhance scalability, reduce contention, and optimize resource utilization.

- *Shard*: Sharding is a technique where data is horizontally divided across multiple database instances or servers. Each shard contains a subset of the data, and queries can be processed in parallel across these shards. Sharding can improve query performance by distributing the workload and reducing the amount of data accessed by each query.

- *Compress data*: Data compression involves reducing the size of data to minimize storage requirements and improve data transfer efficiency. Compressed data takes up less disk space, allowing for cost savings in storage costs. Additionally, compressed data can be transferred more quickly over networks, resulting in reduced data transfer costs.

By employing these techniques, you can optimize data access, retrieval, and storage, leading to improved performance and cost savings. For example, let's consider a scenario where you have a large dataset of customer information. By partitioning the data based on customer regions or demographics, you can distribute the workload across multiple servers and improve query performance. Additionally, you can compress the data to reduce storage costs and improve data transfer efficiency.

### Optimize architecture

Evaluate your workload architecture to identify opportunities for resource optimization. The goal is to use the right services for the right job. To reach this goal, you might need to redesign parts of the architecture to use fewer resources. Consider serverless or managed services and optimize resource allocation. By optimizing your architecture, you can achieve your functional and nonfunctional requirements while consuming fewer per-instance resources.

**Use design patterns.** Design patterns are reusable solutions to common software design problems. They provide a structured approach to designing code that is efficient, maintainable, and scalable. Design patterns help developers solve recurring design problems and promote code reusability.

Design patterns can help cost optimize code in several ways. They help optimize the use of system resources by providing guidelines for efficient resource allocation and management. For example, the Circuit Breaker pattern helps prevent unnecessary resource consumption by providing a mechanism to handle and recover from failures in a controlled manner.

- *Reduced development time*: Design patterns provide proven solutions to common design problems, which can save development time. By following established patterns, developers can avoid reinventing the wheel and focus on implementing the specific requirements of their application.
- *Improved maintainability*: Design patterns promote modular and structured code, making it easier to understand, modify, and maintain. They can lead to cost savings in terms of reduced debugging and maintenance efforts.
- *Scalability and performance*: Design patterns help in designing scalable and performant systems. Patterns like the Cache-Aside pattern can improve performance by caching frequently accessed data, reducing the need for expensive computations or external calls.

To implement design patterns, developers need to understand the principles and guidelines of each pattern and apply them in their code. Consider identifying the appropriate pattern for a given problem, understanding its structure and components, and integrating it into the overall design. There are various resources available such as documentation, tutorials, and sample code. These resources can help developers learn and implement design patterns effectively.

**Change configurations.** Regularly review and update your workload configuration to ensure it aligns with your current requirements. Consider adjusting resource sizing and configuration settings based on workload demands. By optimizing configuration, you can effectively allocate resources and avoid overprovisioning, leading to cost savings.

**Refactor architecture.** Evaluate your workload architecture and identify opportunities for refactoring or redesigning components to optimize resource consumption. Consider techniques such as adopting microservices architecture, implementing the circuit breaker pattern, and using serverless computing. By optimizing your architecture, you can achieve better resource utilization and cost efficiency.

**Modify resource sizes.** Continuously monitor and analyze the resource utilization of your workload. Based on the observed patterns and trends, make adjustments to resource sizing and configuration settings to optimize resource consumption. Consider rightsizing virtual machine instances, adjusting memory allocation, and optimizing storage capacity. By right-sizing resources, you can avoid unnecessary costs associated with underutilization or overprovisioning.

:::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** Code and architecture rework might not fit with current project schedules and might lead to schedule and cost slippage

## Azure facilitation

**Instrumenting code:** Azure provides various monitoring and logging tools like Azure Monitor, Azure Application Insights, and Azure Log Analytics. These tools allow you to track and analyze the performance and behavior of your code in real-time.

**Identifying hot paths:** Azure Application Insights and profiling tools like Azure Profiler help identify the hot paths in your code by analyzing execution times and resource usage.

**Minimizing allocations:** You can minimize unnecessary memory allocations and optimize memory usage by utilizing tools like Azure Profiler and optimizing your code for efficient memory management.

**Performant SDKs:** Azure offers SDKs (Software Development Kits) in multiple programming languages, optimized for performance and ease of use. These SDKs provide prebuilt functions and libraries that interact with Azure services, reducing the need for custom implementation.

**Network protocols:** Various Azure service support high-speed network protocols like HTTP2 and QUIC for efficient communication between services and applications.

**Connection pooling:** Azure services, such as Azure SQL Database, Azure Cosmos DB, and Azure Redis Cache, automatically handle connection pooling. Connection pooling enables efficient reuse of database connections, reducing the overhead of establishing new connections.

**Batching:** Azure supports batch processing in various services, allowing you to group multiple operations together and execute them in a single request. Batch processing can significantly improve efficiency and reduce network overhead.

**Caching:** Azure provides caching services like Azure Cache for Redis. Caching allows you to store frequently accessed data closer to the application, resulting in faster retrieval and reduced backend load.

**Data serialization:** Azure supports various serialization formats like JSON, XML, and Protocol Buffers. You can choose the appropriate serialization format based on your data size, performance requirements, and interoperability needs.

**Indexing and query optimization:** Azure services like Azure SQL Database and Azure Cosmos DB provide indexing capabilities to optimize query performance. By choosing the right indexing strategy and optimizing your queries, you can improve the overall efficiency of data retrieval.

**Object-relational mapping (ORM):** Azure provides ORM frameworks like Entity Framework and Azure Cosmos DB SDKs. These frameworks simplify data access and mapping between object-oriented code and relational or NoSQL databases.

**Optimizing stored procedures:** Azure services like Azure SQL Database allow you to create and optimize stored procedures. Stored procedures can enhance performance by reducing network round-**trips and pre-compiling SQL statements.**

**Partitioning and sharding:** Azure offers partitioning and sharding capabilities in services like Azure Cosmos DB and Azure SQL Database. Partitioning allows you to distribute data across multiple nodes for scalability and performance optimization.

**Compressing data:** Azure services support data compression techniques like GZIP and DEFLATE.

**Design patterns and architecture:** Azure provides architectural guidance and design patterns for designing scalable, resilient, and performant applications.
