---
title: Recommendations for optimizing code and resource use
description: Learn best practices for optimizing code and resource use.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing code and infrastructure use: PE 07

This guide describes the best practices for optimizing code and infrastructure performance. Optimizing code and infrastructure focuses on using components for their core purpose and only using them when necessary. Overuse of code and infrastructure creates unnecessary resource consumption, bottlenecks, and slow responsiveness. To compensate, you need to add more resources to accomplish the same tasks, reducing performance efficiency.

**Definitions**

|Terms| Definition|
|---|---|
|Heap|An area in memory where runtime memory allocations take place.|
|Memory leak|When a workload fails to release allocated memory after it's not needed.|
| Concurrency | Dealing with multiple tasks at once but not necessarily at the same time.|
| Parallelism | Dealing with multiple tasks at the same time. |
|CPU architecture|The components and principles that affect the way the computer works.|
|Data compression|Reducing the size of files by minimizing redundant data.|

## Key strategies

When it comes to code and infrastructure, performance and efficiency are key considerations. You need to code that is efficient and optimized. Use appropriate data structures and algorithms, minimizing resource usage, and avoiding unnecessary computations. You should also offload responsibilities to the platform. Take advantage of the features and services provided by the underlying platform.

Code and infrastructure should only be used for their core purpose and only when necessary. Avoid unnecessary complexity and ensuring that each component is designed and utilized for its intended function. By keeping things simple and focused, you can improve performance and minimize resource usage.

### Optimize code performance

Optimizing code performance refers to the process of improving the efficiency and speed of a software program. It involves making modifications to the code to reduce resource usage, minimize execution time, and enhance overall performance.

Don't mask performance issues with brute force. Brute force means adding more compute resources to compensate for code performance through extra capacity instead of addressing the source. You need to fix performance issues through optimization. Optimizing code performance helps maximize the utilization of system resources, improves responsiveness, reduces latency, and enhances the user experience.

#### Optimize code logic

Optimizing code logic for performance efficiency refers to the process of improving the execution speed and resource utilization of a software program. It involves analyzing and modifying the code to minimize unnecessary computations, reduce memory usage, and optimize algorithms and data structures.

**Optimize data structures.** The selection of appropriate data structures, such as arrays, linked lists, trees, or hash tables, can have a significant effect on the efficiency of storing and retrieving data. By choosing the right data structure for the specific problem, you can improve application performance.

**Optimize loops.** Loops are commonly used in programming, and optimizing them can lead to faster execution. Techniques like reducing unnecessary iterations, minimizing function calls within loops, and eliminating redundant calculations can make your code more efficient. Consider moving computations outside the loop or utilizing loop unrolling to further enhance performance.

**Optimize tools.** Use native SDKs or performance optimized libraries. Native SDKs are designed to interact with the services and resources on a platform or within a framework. For example, cloud native SDKs work better with cloud service data planes than custom API access. The SDKs are often more performant handling network requests and excel at optimizing their interactions. Performance-optimized libraries, such as Math.NET, contain performance optimized functions that can improve performance when applied appropriately.

:::image type="icon" source="../_images/trade-off.svg"::: *Tradeoff:* While SDKs provide convenience and abstract away the complexities of interacting with APIs, they may limit the level of control and customization you have over your custom code.

#### Optimize memory management

Optimizing code memory refers to the process of efficiently managing and reducing the memory usage in a codebase. Efficient memory management can improve the performance of the code by reducing the overhead of memory operations.

**Debug memory leaks.** Memory leaks deplete available memory if unaddressed. Monitor memory usage and identify sections of code responsible for memory leaks.
Analyze stack traces to pinpoint the source of the memory leak.

**Reduce memory allocations.** By minimizing memory allocations, you reduce the overall memory footprint of the code. The workload can utilize the available memory more efficiently. There's less need for the garbage collector to reclaim unused memory, and it reduces the frequency and duration of garbage collection cycles. Memory allocations can be a costly operation, especially when performed frequently. By reducing the number of memory allocations, the code can execute faster and more efficiently, leading to improved performance.

Caches play a crucial role in improving performance by storing frequently accessed data closer to the processor. When memory allocations are minimized, there's less contention for cache space, allowing the cache to be utilized more effectively and improving overall performance. Too many memory allocations can degrade application performance generate errors.

- *Local variables:* Use local variables instead of global variables to minimize memory consumption.
- *Lazy initialization.* Implement lazy initialization to defer the creation of objects or resources until needed.
- *Buffers:* Manage buffers effectively to avoid allocating large memory buffers.
- *Object pooling:* Consider object pooling to reuse large objects instead of allocating and deallocating them.

For more information, see [Avoid memory allocations](/dotnet/csharp/advanced-topics/performance/) and [Large object heap (LOH)](/dotnet/standard/garbage-collection/large-object-heap).

#### Use concurrency and parallelism

Concurrency and parallelism allow multiple tasks to be executed simultaneously, and they increase the overall throughput or the number of a workload can process. By executing tasks concurrently or in parallel, the overall execution time of the application can be reduced, resulting in lower latency or faster response times. Concurrency and parallelism enable better utilization of available computing resources, such as CPU cores or distributed systems, by effectively distributing the workload among them.

**Use parallelism.** Parallelism, on the other hand, involves the simultaneous execution of multiple tasks or processes on multiple computing resources, such as multiple CPU cores or distributed systems. It aims to divide a workload into smaller tasks that can be executed in parallel, reducing the overall execution time. Parallelism can be achieved through techniques like multi-processing, where multiple processes run in parallel, or through distributed computing, where tasks are distributed across multiple machines. Code should take advantage of the CPU architecture and threading models. Distribute tasks across multi-core processors for better workload management. Explore opportunities for parallel execution of code to take advantage of multi-core processors. Parallelizing code can lead to significant performance improvements by distributing the workload across multiple cores.

**Use concurrency.** Concurrency refers to the ability of a system to execute multiple tasks or processes. It enables different parts of a program to make progress independently, potentially improving overall performance. You can implement concurrency through techniques through multi-threading, where multiple threads of execution run concurrently within a single process. You can also use asynchronous programming, where tasks are executed concurrently without blocking the main execution thread.

- *Asynchronous programming:* Asynchronous programming is an approach that allows tasks to be executed without blocking the main thread of execution. It enables a program to continue executing other tasks while waiting for long-running operations to complete.

    Asynchronous programming allows the program to initiate multiple tasks and wait for them to complete asynchronously. Instead of blocking the main thread and waiting for each task to finish before moving on to the next one, the program can continue executing other tasks while the long-running operations are in progress.

    There are various techniques and patterns available depending on the programming language and platform being used. One common approach is to use asynchronous keywords and constructs such as `async` and `await` in languages like C#. These keywords allow you to define asynchronous methods and await the completion of tasks without blocking the execution. For HTTP traffic, consider using the [asynchronous request-reply pattern](/azure/architecture/patterns/async-request-reply).

    Additionally, many frameworks and libraries provide built-in support for asynchronous programming. For example, in the .NET platform, there are patterns like the [Task-based Asynchronous Pattern (TAP)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) and the [Event-based asynchronous pattern](/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap) that can be used to implement asynchronous operations.

    It's important to note that the specific implementation of asynchronous programming may vary depending on the programming language, platform, and the specific requirements of the application.

- *Queues:* A queue is a storage buffer located between a requesting component (producer) and the processing component (consumer) of the workload. There can be multiple consumers for a single queue. As the tasks increase, you should scale the consumers to meet the demand. The requesting component places tasks in the queue, the queue stores them until the processing component has capacity. A queue is often the best way to hand off work to a processing service that experiences peaks in demand. To learn more about queue-based load leveling, see [Queue-based load leveling pattern](/azure/architecture/patterns/queue-based-load-leveling). Also see [Storage queues and Service Bus queues compared and contrasted](/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted).

#### Use retry mechanisms

Retry mechanisms are techniques used in code to handle and recover from transient errors or failures. They provide a way to automatically retry an operation that failed due to temporary issues such as network errors, service unavailability, or resource congestion.

Retry mechanisms often incorporate techniques such as exponential backoff, where the delay between retries gradually increases. This approach helps to alleviate resource congestion by reducing the number of simultaneous retry attempts. Introducing a delay between retries you can minimize the strain on resources, resulting in more efficient resource utilization.

#### Use connection pooling

Establishing a connection to a database is an expensive operation. It requires creating an authenticated network connection to the remote database server. Database connections are especially expensive for applications that open new connections frequently. Connection pooling reuses existing connections and avoids the expense of opening a new connection for each request. Connection pooling reduces connection latency and enables higher database throughput (transactions per second) on the server. You should choose a pool size that can handle more connections than you currently have. The goal is to have the connection pool quickly handle a new incoming request.

- *Understand connection pooling limits.* Some services limit the number of network connections. Exceeding this limit causes connections to be slowed down or terminated. With connection pooling, a fixed set of connections is established at startup time and maintained. In many cases, a default pool size might consist only of a small handful of connections that perform quickly in basic test scenarios. Your application might exhaust the default pool size under scale and create a bottleneck. You should establish a pool size that maps to the number of concurrent transactions supported on each application instance.
- *Test the connection pool.* Each database and application platform has slightly different requirements for the right way to set up and use the pool. In all cases, testing is important to ensure a connection pool is properly established and works as designed under load.

     :::image type="icon" source="../_images/risk.svg"::: *Risk:* Connection pooling could create [pool fragmentation](/dotnet/framework/data/adonet/sql-server-connection-pooling#pool-fragmentation) and degrade performance.

#### Optimize background jobs

Many types of applications require background tasks that run independently of the user interface. The application can start the job and continue to process interactive requests from users. Examples include batch jobs, intensive processing tasks, and long-running processes such as workflows. Background tasks shouldn't block the application or cause inconsistencies due to delayed operation when the system is under load. You can improve performance by scaling the compute instances that host the background tasks. For more information, see [Background jobs](/azure/architecture/best-practices/background-jobs) and[Background jobs scaling and performance considerations](/azure/architecture/best-practices/background-jobs#scaling-and-performance-considerations).

### Optimize infrastructure performance

Optimizing infrastructure performance involves tuning the performance of hardware and network components to ensure optimal operation and resource utilization.

**Add usage limits.** You can put utilization limits on some workload components. For example, you can define [pod CPU and memory limits](/azure/aks/developer-best-practices-resource-management) in AKS to remove unstable pods. You can define memory limits in [Java virtual machines](/azure/spring-apps/concepts-for-java-memory-management) to optimize performance.

**Eliminate infrastructure.** Simplifying your workload reduces the potential for issues related to interactions, dependencies, and compatibility. By removing these elements, you optimize resource utilization of memory, processing power, and storage for essential resources.

**Reduce load.** Reducing the load on a workload involves minimizing the work or demand placed on an application and allowing resources to focus on their primary tasks. For example, it's recommended to avoid running security solutions within your code or on individual compute instances. Instead, web servers should focus on serving HTTP requests, while web application firewalls and gateway resources can handle security checks. Here are two strategies to help reduce the load on your workload:

- *Eventual consistency:* Adopting an eventual consistency model can enhance performance by allowing data to be slightly dated. Eventual consistency reduces the immediate demand on CPU cycles and network bandwidth for constant data updates.
- *Delegate tasks:* Delegate certain server tasks to clients or intermediaries, such as search indexes and caches. This delegation can include tasks like sorting data, filtering data, or rendering views. By offloading these tasks, you can reduce the workload on your servers and improve performance.

**Optimize the network.** Optimizing a workload network for performance refers to the process of configuring and fine-tuning the network infrastructure. The goal is to ensure that the workload can operate at its highest level of efficiency and deliver optimal performance.

- *Network protocols.* Upgrade to modern protocols like HTTP/2, which enables multiple requests to be sent over a single connection, reducing the overhead of establishing new connections.

    :::image type="icon" source="../_images/trade-off.svg":::*Tradeoff:* Newer protocol can exclude older clients.
- *Network chattiness.* Reduce the number of network requests by batching them together. Instead of making multiple small requests, combine them into larger requests to reduce network overhead.
- *Database queries.* Ensure that database queries retrieve only the necessary information. Avoid retrieving large amounts of unnecessary data, which can lead to increased network traffic and slower performance.
- *Static data.* Utilize a content delivery network (CDN) to cache frequently accessed static content closer to the users. Caching reduces the need for data to travel over long distances, improving response times and reducing network traffic.
- *Log collection.* Collect and retain only the necessary log data to support your requirements. Configure data collection rules and consider design considerations for optimizing your Log Analytics costs.
- *Data compression.* Compressing and bundling [HTTP content](/iis/configuration/system.webserver/httpcompression/) and [file data](/windows/win32/fileio/file-compression-and-decompression) allows faster transmission between clients and servers. Compression shrinks the data returned from a page or API back to the browser or client app. It optimizes network traffic that can potentially accelerate application communication.

    :::image type="icon" source="../_images/trade-off.svg":::
*Tradeoff:* Compressions adds extra server side and client side processing. The application must compress, send, and decompress data. Multi-cast communication (multiple recipients) can create even more decompression overhead. You need to test and measure the performance variations before and after implementing data compression to determine if it's a good fit for your workload. For more information, see [Response compression in ASP.NET Core](/aspnet/core/performance/response-compression?preserve-view=true&view=aspnetcore-3.1).

## Azure facilitation

**Optimization tools:** Azure Perf Insights provides insights into network performance and actionable recommendations for optimizing Azure services and resources.

**Native SDKs & performance-optimized libraries:** Azure provides [SDKs](https://azure.microsoft.com/downloads/) and libraries for various programming languages to interact with Azure services. The SDKs are designed to simplify interactions between applications and Azure resources. These SDKs provide optimal interaction with Azure services, reducing latency and enhancing efficiency

**Memory management:** [Azure Application Insights Smart Detection](/azure/azure-monitor/alerts/proactive-diagnostics) analyzes memory consumption and helps identify and address potential memory leaks. Azure offers best practices and guidelines for efficient memory management.

**Concurrency and parallelism:** Azure provides services like [Azure Kubernetes Service (AKS)](https://learn.microsoft.com/azure/aks/) that support deploying containerized applications for enhanced parallel processing. [Azure Batch](https://learn.microsoft.com/azure/batch/batch-technical-overview) is a cloud-based job scheduling service that enables parallel and high-performance computing without the need for infrastructure setup.

**Infrastructure consistency:** Azure offers tools like [Azure Resource Manager (ARM) templates](/azure/templates/), which allow users to define and deploy infrastructure using code for efficient, repeatable, and consistent resource deployments. [Azure Policy](/azure/governance/policy/overview) provides governance capabilities to ensure that resource deployments adhere to organizational best practices and standards.

**Monitoring and analysis:** Azure Log Analytics provides detailed insights into code and infrastructure performance, helping identify performance bottlenecks.
Azure Monitor: It offers full-stack monitoring, advanced analytics, and intelligent alerting to facilitate the identification and resolution of performance issues.

**Asynchronous programming:** Azure provides scalable queuing services like Azure Queue Storage and Azure Service Bus, which facilitate asynchronous programming by allowing tasks to be queued and processed independently.

Azure Marketplace offers various third-party queues and tools that can be easily integrated with Azure services to support asynchronous operations.
