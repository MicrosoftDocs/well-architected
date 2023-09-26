---
title: Code and infrastructure optimization recommendations
description: Learn best practices for optimizing code and infrastructure.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing code and infrastructure

**Applies to: PE 07**

This guide describes the recommendations for optimizing code and infrastructure performance. Optimizing code and infrastructure consists of using components for their core purpose and only using them when necessary. Overusing code and infrastructure creates unnecessary resource consumption, bottlenecks, and slow response. To compensate, you need to add more resources to accomplish the same tasks, which reduces performance efficiency.

**Definitions**

|Term| Definition|
|---|---|
|Heap|An area in memory in which runtime memory allocations occur.|
| Memory leak | When a workload fails to release allocated memory after the memory is no longer needed.|
| Concurrency | When multiple tasks are performed at once but not necessarily at the same time.|
| Parallelism | When multiple tasks are performed at the same time. |
| CPU architecture | The components and principles that affect how the computer works. |
| Data compression | The action of reducing the size of files by minimizing redundant data.|

## Key strategies

Performance and efficiency are key factors to consider when you optimize your code and infrastructure. You need code that's efficient and optimized. Use appropriate data structures and algorithms, so you can minimize resource usage and avoid unnecessary computations. You should also offload responsibilities to the platform. Take advantage of the features and services that the underlying platform provides.

Use code and infrastructure only for their core purpose and only when necessary. Avoid unnecessary complexity and design and utilize each component for its intended function. Keep your design simple and focused, so you can improve performance and minimize resource usage.

### Optimize code performance

To optimize code performance, modify the code to reduce resource usage, minimize execution time, and enhance overall performance. Apply these techniques to improve the efficiency and speed of a software program.

Don't mask performance issues with brute force. Brute force means adding more compute resources to compensate for code performance through extra capacity instead of addressing the source. You need to fix performance issues through optimization. Optimizing code performance helps maximize the utilization of system resources, improves response time, reduces latency, and enhances the user experience.

#### Optimize code logic

To optimize code logic for performance efficiency, analyze and modify the code to minimize unnecessary computations, reduce memory usage, and optimize algorithms and data structures. When you implement these processes, it improves the execution speed and resource utilization of a software program.

**Optimize data structures**. To efficiently store and retrieve data, select the appropriate data structures, such as arrays, linked lists, trees, and hash tables. Choose the best data structure for a specific problem, so you can improve application performance.

**Optimize loops**. Loops are commonly used in programming. Optimize loops for faster execution. To make your code more efficient, reduce unnecessary iterations, minimize function calls within loops, and eliminate redundant calculations. To further enhance performance, consider moving computations outside the loop or utilizing loop unrolling.

**Optimize tools**. Use native SDKs or performance-optimized libraries. Native SDKs are designed to interact with the services and resources on a platform or within a framework. For example, cloud-native SDKs work better with cloud service data planes than with custom API access. SDKs excel at handling network requests and optimizing their interactions. Performance-optimized libraries, such as Math.NET, contain performance-optimized functions. When you apply the functions appropriately, you can improve your workload's performance.

> :::image type="icon" source="../_images/trade-off.svg"::: *Tradeoff*: SDKs provide convenience and abstract away the complexities of interacting with APIs. But SDKs might limit your control and customization options for custom code.

#### Optimize memory management

To optimize code memory, efficiently manage and reduce the memory usage in a codebase. Efficient memory management can improve the performance of the code by reducing the overhead of memory operations.

**Debug memory leaks**. If you don't address memory leaks, they deplete the available memory. Monitor memory usage, and identify sections of code that are responsible for memory leaks. Analyze stack traces to pinpoint the source of the memory leak.

**Reduce memory allocations**. Minimize memory allocations to reduce the overall memory footprint of the code. The workload can utilize the available memory more efficiently. There's less need for the garbage collector to reclaim unused memory, and it reduces the frequency and duration of garbage collection cycles. Memory allocations can be a costly operation, especially when you perform them frequently. Reduce the number of memory allocations, so the code can execute fast and efficiently, which leads to improved performance.

Caches store frequently accessed data closer to the processor, which improves performance. When you minimize memory allocations, there's less contention for cache space. You can effectively utilize the cache and improve overall performance. A high number of memory allocations can degrade application performance and generate errors. Other ways to minimize memory allocations include:

- *Local variables*: Use local variables instead of global variables to minimize memory consumption.
- *Lazy initialization*. Implement lazy initialization to defer the creation of objects or resources until they're needed.
- *Buffers*: Manage buffers effectively to avoid allocating large memory buffers.
- *Object pooling*: Consider object pooling to reuse large objects instead of allocating and deallocating them.

For more information, see [Avoid memory allocations](/dotnet/csharp/advanced-topics/performance) and [Large object heap (LOH)](/dotnet/standard/garbage-collection/large-object-heap).

#### Use concurrency and parallelism

Use concurrency and parallelism to run multiple tasks, which increases the overall throughput and the number tasks that a workload can process. When you run tasks concurrently or in parallel, it reduces the runtime of the application, which decreases latency and increases response times. Concurrency and parallelism enable efficient utilization of computing resources, such as CPU cores or distributed systems. Concurrency and parallelism effectively distribute the workload among the computing resources.

**Use parallelism**. Parallelism is the ability of a system to simultaneously trigger multiple tasks or processes on multiple computing resources, such as CPU cores or distributed systems. Parallelism divides a workload into smaller tasks that are run in parallel, which reduces the overall runtime. You can achieve parallelism by using techniques like multi-processing, in which multiple processes run in parallel, or by using distributed computing, in which tasks are distributed across multiple machines. Code should take advantage of the CPU architecture and threading models. Distribute tasks across multi-core processors to optimize workload management. Explore opportunities for parallelism of code to take advantage of multi-core processors. When you run code in parallel, performance improves because the workload is distributed across multiple cores.

**Use concurrency**. Concurrency is the ability of a system to run multiple tasks or processes. Concurrency enables different parts of a program to make progress independently, which can improve overall performance. You can implement concurrency by using techniques like multi-threading, in which multiple threads of execution run concurrently within a single process. You can also use asynchronous programming, in which tasks are triggered concurrently without blocking the main execution thread.

- *Asynchronous programming*: Asynchronous programming is an approach that allows tasks to be triggered without blocking the main thread of execution. It enables a program to continue to trigger other tasks while waiting for long-running operations to finish.

    Asynchronous programming allows the program to initiate multiple tasks and wait for them to complete asynchronously. Instead of blocking the main thread and waiting for each task to finish before moving on to the next one, the program can continue running other tasks while the long-running operations are in progress.

   Depending on the programming language and platform, there are many techniques and patterns. One common approach is to use asynchronous keywords and constructs, such as `async` and `await`, in languages like C#. These keywords allow you to define asynchronous methods and wait for tasks to finish without blocking the execution. For HTTP traffic, consider using the [Asynchronous Request-Reply pattern](/azure/architecture/patterns/async-request-reply).

    Many frameworks and libraries provide built-in support for asynchronous programming. For example, in the .NET platform, you can implement asynchronous operations by using patterns like [Task-Based Asynchronous Pattern](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) and [Event-Based Asynchronous pattern](/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap).

    The specific implementation of asynchronous programming might vary depending on the programming language, platform, and the specific requirements of the application.

- *Queues*: A queue is a storage buffer that's located between a requesting component (producer) and the processing component (consumer) of the workload. There can be multiple consumers for a single queue. As the tasks increase, you should scale the consumers to meet the demand. The requesting component places tasks in the queue. The queue stores the tasks until the processing component has capacity. A queue is often the best way to hand off work to a processing service that experiences peaks in demand. For more information, see [Queue-Based Load Leveling pattern](/azure/architecture/patterns/queue-based-load-leveling) and [Storage queues and Service Bus queues compared and contrasted](/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted).

#### Use retry mechanisms

Retry mechanisms are operations that are used in code to handle and recover from transient errors or failures. Retry mechanisms provide a way to automatically retry an operation that failed due to temporary issues, such as network errors, service unavailability, or resource congestion.

Retry mechanisms often incorporate techniques such as exponential backoff. This technique gradually increases the delay between retries. This approach reduces the number of simultaneous retry attempts, which helps alleviate resource congestion. When you introduce a delay between retries, you can minimize the strain on resources, which results in efficient resource utilization.

#### Use connection pooling

When you establish a connection to a database, it can be expensive. It requires creating an authenticated network connection to the remote database server. Database connections are especially expensive for applications that frequently open new connections. Connection pooling reuses existing connections and avoids the expense of opening a new connection for each request. Connection pooling reduces connection latency and enables higher database throughput (transactions per second) on the server. You should choose a pool size that can handle more connections than you currently have. The goal is to have the connection pool quickly handle new incoming requests.

- *Understand connection pooling limits*. Some services limit the number of network connections. Exceeding this limit causes connections to be slowed down or terminated. With connection pooling, a fixed set of connections is established at startup time and maintained. In many cases, a default pool size might consist only of a small handful of connections that perform quickly in basic test scenarios. Your application might exhaust the default pool size under scale and create a bottleneck. You should establish a pool size that maps to the number of concurrent transactions supported on each application instance.
- *Test the connection pool*. Each database and application platform has slightly different requirements for the right way to set up and use the pool. In all cases, testing is important to ensure a connection pool is properly established and works as designed under load.

  > :::image type="icon" source="../_images/risk.svg"::: *Risk*: Connection pooling could create [pool fragmentation](/dotnet/framework/data/adonet/sql-server-connection-pooling#pool-fragmentation) and degrade performance.

#### Optimize background jobs

Many types of applications require background tasks that run independently of the user interface. The application can start the job and continue to process interactive requests from users. Examples include batch jobs, intensive processing tasks, and long-running processes such as workflows. Background tasks shouldn't block the application or cause inconsistencies due to delayed operation when the system is under load. You can improve performance by scaling the compute instances that host the background tasks. For more information, see [Background jobs](/azure/architecture/best-practices/background-jobs) and[Background jobs scaling and performance considerations](/azure/architecture/best-practices/background-jobs#scaling-and-performance-considerations).

### Optimize infrastructure performance

Optimizing infrastructure performance involves tuning the performance of hardware and network components to ensure optimal operation and resource utilization.

**Add usage limits**. You can put utilization limits on some workload components. For example, you can define [pod CPU and memory limits](/azure/aks/developer-best-practices-resource-management) in AKS to remove unstable pods. You can define memory limits in [Java virtual machines](/azure/spring-apps/concepts-for-java-memory-management) to optimize performance.

**Eliminate infrastructure**. Simplifying your workload reduces the potential for issues related to interactions, dependencies, and compatibility. By removing these elements, you optimize resource utilization of memory, processing power, and storage for essential resources.

**Reduce load**. Reducing the load on a workload involves minimizing the work or demand placed on an application and allowing resources to focus on their primary tasks. For example, it's recommended to avoid running security solutions within your code or on individual compute instances. Instead, web servers should focus on serving HTTP requests, while web application firewalls and gateway resources can handle security checks. Here are two strategies to help reduce the load on your workload:

- *Eventual consistency*: Adopting an eventual consistency model can enhance performance by allowing data to be slightly dated. Eventual consistency reduces the immediate demand on CPU cycles and network bandwidth for constant data updates.
- *Delegate tasks*: Delegate certain server tasks to clients or intermediaries, such as search indexes and caches. This delegation can include tasks like sorting data, filtering data, or rendering views. By offloading these tasks, you can reduce the workload on your servers and improve performance.

**Optimize the network**. Optimizing a workload network for performance refers to the process of configuring and fine-tuning the network infrastructure. The goal is to ensure that the workload can operate at its highest level of efficiency and deliver optimal performance.

- *Network protocols*. Upgrade to modern protocols like HTTP/2, which enables multiple requests to be sent over a single connection, reducing the overhead of establishing new connections.

    > :::image type="icon" source="../_images/trade-off.svg":::*Tradeoff*: Newer protocol can exclude older clients.

- *Network chattiness*. Reduce the number of network requests by batching them together. Instead of making multiple small requests, combine them into larger requests to reduce network overhead.
- *Database queries*. Ensure that database queries retrieve only the necessary information. Avoid retrieving large amounts of unnecessary data, which can lead to increased network traffic and slower performance.
- *Static data*. Utilize a content delivery network (CDN) to cache frequently accessed static content closer to the users. Caching reduces the need for data to travel over long distances, improving response times and reducing network traffic.
- *Log collection*. Collect and retain only the necessary log data to support your requirements. Configure data collection rules and consider design considerations for optimizing your Log Analytics costs.
- *Data compression*. Compressing and bundling [HTTP content](/iis/configuration/system.webserver/httpcompression) and [file data](/windows/win32/fileio/file-compression-and-decompression) allows faster transmission between clients and servers. Compression shrinks the data returned from a page or API back to the browser or client app. It optimizes network traffic that can potentially accelerate application communication.

    > :::image type="icon" source="../_images/trade-off.svg":::
*Tradeoff*: Compressions adds extra server side and client side processing. The application must compress, send, and decompress data. Multi-cast communication (multiple recipients) can create even more decompression overhead. You need to test and measure the performance variations before and after implementing data compression to determine if it's a good fit for your workload. For more information, see [Response compression in ASP.NET Core](/aspnet/core/performance/response-compression).

## Azure facilitation

**Optimization tools**: Azure Perf Insights provides insights into network performance and actionable recommendations for optimizing Azure services and resources.

**Native SDKs & performance-optimized libraries**: Azure provides [SDKs](https://azure.microsoft.com/downloads) and libraries for various programming languages to interact with Azure services. The SDKs are designed to simplify interactions between applications and Azure resources. These SDKs provide optimal interaction with Azure services, reducing latency and enhancing efficiency

**Memory management**: [Azure Application Insights Smart Detection](/azure/azure-monitor/alerts/proactive-diagnostics) analyzes memory consumption and helps identify and address potential memory leaks. Azure offers best practices and guidelines for efficient memory management.

**Concurrency and parallelism**: Azure provides services like [Azure Kubernetes Service (AKS)](https://learn.microsoft.com/azure/aks) that support deploying containerized applications for enhanced parallel processing. [Azure Batch](https://learn.microsoft.com/azure/batch/batch-technical-overview) is a cloud-based job scheduling service that enables parallel and high-performance computing without the need for infrastructure setup.

**Infrastructure consistency**: Azure offers tools like [Azure Resource Manager (ARM) templates](/azure/templates), which allow users to define and deploy infrastructure using code for efficient, repeatable, and consistent resource deployments. [Azure Policy](/azure/governance/policy/overview) provides governance capabilities to ensure that resource deployments adhere to organizational best practices and standards.

**Monitoring and analysis**: Azure Log Analytics provides detailed insights into code and infrastructure performance, helping identify performance bottlenecks.
Azure Monitor: It offers full-stack monitoring, advanced analytics, and intelligent alerting to facilitate the identification and resolution of performance issues.

**Asynchronous programming**: Azure provides scalable queuing services like Azure Queue Storage and Azure Service Bus, which facilitate asynchronous programming by allowing tasks to be queued and processed independently.

Azure Marketplace offers various third-party queues and tools that can be easily integrated with Azure services to support asynchronous operations.

## Related links

## Community links

## Next steps
