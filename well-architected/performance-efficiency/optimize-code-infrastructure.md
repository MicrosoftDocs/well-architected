---
title: Recommendations for optimizing code and infrastructure
description: Learn how to optimize your code and infrastructure by using components only for their core purpose and only when necessary.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing code and infrastructure

**Applies to: PE 07**

This guide describes the recommendations for optimizing code and infrastructure performance. To optimize your code and infrastructure, you should use your components only for their core purpose and only when necessary. When you overuse code and infrastructure, it creates unnecessary resource consumption, bottlenecks, and slow responses. To compensate for those inefficiencies, you must add more resources to accomplish the same tasks, which reduces performance efficiency.

**Definitions**

|Term| Definition|
|---|---|
| CPU architecture | The components and principles that affect how the computer works. |
| Data compression | The action of reducing the size of files by minimizing redundant data.|
|Heap|An area in memory that's used for runtime memory allocations.|
| Memory leak | When a workload fails to release allocated memory after the memory is no longer needed.|
| Concurrency | When multiple tasks or processes are performed at once but not necessarily at the exact same time.|
| Parallelism | When multiple tasks or processes are performed at the same time. |

## Key strategies

Performance and efficiency are key factors to consider when you optimize your code and infrastructure. You need code that's functional and optimized. To minimize resource usage and avoid unnecessary computations, use appropriate data structures and algorithms. You should also offload responsibilities to the platform. Take advantage of the features and services that the underlying platform provides.

Avoid unnecessary complexity and design. Utilize each component for its intended function. Keep your design simple and undiluted, so you can improve performance and minimize resource usage.

### Optimize code performance

To optimize code performance, modify code to reduce resource usage, minimize runtime, and enhance performance. You can modify code to improve the efficiency and speed of a software program.

Don't mask performance issues with brute force. Brute force means adding compute resources to compensate for code performance, like adding extra capacity instead of addressing the source. You need to fix performance issues with optimization. When you optimize code performance, it helps maximize the utilization of system resources, improves response time, reduces latency, and enhances the user experience.

#### Optimize code logic

To optimize code logic to improve performance efficiency, analyze and modify code to minimize unnecessary computations, reduce memory usage, and optimize algorithms and data structures.

**Optimize data structures**. To efficiently store and retrieve data, select appropriate data structures, such as arrays, linked lists, trees, and hash tables. Choose the best data structure for a specific problem. A suitable data structure improves application performance.

**Optimize loops**. Loops are commonly used in programming. Optimize loops for faster runtime. To make your code more efficient, reduce unnecessary iterations, minimize function calls within loops, and eliminate redundant calculations. To further enhance performance, consider moving computations outside the loop or utilizing loop unrolling.

**Optimize tools**. Use native SDKs or performance-optimized libraries. Native SDKs are designed to interact with the services and resources on a platform or within a framework. For example, cloud-native SDKs work better with cloud service data planes than with custom API access. SDKs excel at handling network requests and optimizing interactions. Performance-optimized libraries, such as Math.NET, contain performance-optimized functions. When you apply the functions appropriately, you can improve your workload's performance.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: SDKs provide convenience and eliminate the complexities of interacting with APIs. But SDKs might limit your control and customization options for custom code.

#### Optimize memory management

To optimize code memory, efficiently manage and reduce the memory usage in a codebase. Proper memory management improves code performance because it reduces the overhead of memory operations.

**Debug memory leaks**. If you don't address memory leaks, they deplete available memory. Monitor memory usage, and identify sections of code that are responsible for memory leaks. Analyze stack traces to pinpoint the source of the memory leak.

**Reduce memory allocations**. Minimize memory allocations to reduce the overall memory footprint of the code. Your workload can utilize the available memory efficiently. There's less need for the garbage collector to reclaim unused memory, and it reduces the frequency and duration of garbage collection cycles. Memory allocations can be costly, especially if you perform them frequently. Minimize memory allocations, so the code can run quickly and efficiently.

Caches store frequently accessed data close to the processor, which improves performance. When you minimize memory allocations, there's less contention for cache space, so you can effectively utilize the cache. A high number of memory allocations can degrade application performance and generate errors. Other ways to minimize memory allocations include:

- *Local variables*. Use local variables instead of global variables to minimize memory consumption.
- *Lazy initialization*. Implement lazy initialization to defer the creation of objects or resources until they're needed.
- *Buffers*. Manage buffers effectively to avoid allocating large memory buffers.
- *Object pooling*. Consider object pooling to reuse large objects instead of allocating and deallocating them.

For more information, see [Reduce memory allocations](/dotnet/csharp/advanced-topics/performance) and [The large object heap on Windows systems](/dotnet/standard/garbage-collection/large-object-heap).

#### Use concurrency and parallelism

Use concurrency and parallelism to run multiple tasks. These techniques increase the overall throughput and the number of tasks that a workload can process. When you run tasks concurrently or in parallel, it reduces the runtime of the application, which decreases latency and increases response times. Concurrency and parallelism enable efficient utilization of computing resources, such as CPU cores or distributed systems. Concurrency and parallelism effectively distribute the workload among the computing resources.

**Use parallelism**. Parallelism is the ability of a system to simultaneously trigger multiple tasks or processes on multiple computing resources. Parallelism divides a workload into smaller tasks that are run in parallel. You can achieve parallelism by using techniques like multiprocessing, in which multiple processes run in parallel, or by using distributed computing, in which tasks are distributed across multiple machines. Distribute tasks across multicore processors to optimize workload management. Optimize code to take advantage of the CPU architecture, threading models, and multicore processors. When you run code in parallel, performance improves because the workload is distributed across multiple cores.

**Use concurrency**. Concurrency is the ability of a system to run multiple tasks or processes. Concurrency enables different parts of a program to make progress independently, which can improve overall performance. You can implement concurrency by using techniques like multithreading, in which multiple threads run concurrently within a single process. You can also use asynchronous programming, in which tasks are triggered concurrently.

- *Asynchronous programming*: Asynchronous programming is an approach that's used to trigger tasks without blocking the main thread. Asynchronous programming enables a program to trigger tasks while waiting for long-running operations to finish.

    With asynchronous programming, the program can initiate multiple tasks and wait for them to complete asynchronously. The program doesn't have to wait for each task to finish before moving on to the next one.

  There are many asynchronous programming techniques and patterns, depending on the programming language and platform. One common approach is to use asynchronous keywords and constructs, such as `async` and `await`, in languages like C#. With these keywords, you can define asynchronous methods. For HTTP traffic, consider using the [Asynchronous Request-Reply pattern](/azure/architecture/patterns/async-request-reply).

    Many frameworks and libraries provide built-in support for asynchronous programming. For example, in the .NET platform, you can implement asynchronous operations by using patterns like [Task-Based Asynchronous pattern](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) and [Event-Based Asynchronous pattern](/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap).

    The specific implementation of asynchronous programming varies depending on the programming language, platform, and requirements of the application.

- *Queues*: A queue is a storage buffer that's located between a requesting component, or the producer, and the processing component, or the consumer, of the workload. There can be multiple consumers for a single queue. As the tasks increase, you should scale the consumers to meet the demand. The producer places tasks in a queue. The queue stores the tasks until a consumer has capacity. A queue is often the best way to hand off work to a processing service that experiences peaks in demand. For more information, see [Queue-Based Load Leveling pattern](/azure/architecture/patterns/queue-based-load-leveling) and [Storage queues and Service Bus queues](/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted).

#### Use connection pooling

It can be expensive to establish a connection to a database. You have to create an authenticated network connection to the remote database server. Database connections are especially expensive for applications that frequently open new connections. Connection pooling reuses existing connections and eliminates the expense of opening a new connection for each request. Connection pooling reduces connection latency and enables high database throughput (transactions per second) on the server. You should choose a pool size that can handle more connections than you currently have. The goal is to have the connection pool quickly handle new incoming requests.

**Understand connection pooling limits**. Some services limit the number of network connections. When you exceed this limit, connections might slow down or terminate. You can use connection pooling to establish a fixed set of connections at startup time and then maintain those connections. In many cases, a default pool size might consist of only a few connections that perform quickly in basic test scenarios. Your application might exhaust the default pool size under scale and create a bottleneck. You should establish a pool size that maps to the number of concurrent transactions that are supported on each application instance.

**Test the connection pool**. Each database and application platform has slightly different requirements for setting up and using a pool. Test your connection pool to ensure that it's properly established and works efficiently under load.

  > :::image type="icon" source="../_images/risk.svg"::: **Risk**: Connection pooling can create [pool fragmentation](/dotnet/framework/data/adonet/sql-server-connection-pooling#pool-fragmentation) and degrade performance.

#### Optimize background jobs

Many applications require background tasks that run independently of the UI. The application can start the job and continue to process interactive requests from users. Examples of background jobs include batch jobs, processor-intensive tasks, and long-running processes, such as workflows. Background tasks shouldn't block the application or cause inconsistencies due to delayed operation when the system is under load. To improve performance, you can scale compute instances that host background tasks. For more information, see [Background jobs](/azure/architecture/best-practices/background-jobs) and [Scaling and performance considerations](/azure/architecture/best-practices/background-jobs#scaling-and-performance-considerations).

### Optimize infrastructure performance

To optimize infrastructure performance, tune the performance of hardware and network components to ensure optimal operation and resource utilization.

**Add usage limits**. You can implement usage limits on some workload components. For example, to remove unstable pods, you can [define pod CPU and memory limits](/azure/aks/developer-best-practices-resource-management#define-pod-resource-requests-and-limits) in Azure Kubernetes Service (AKS). To optimize performance, you can [define memory limits in Java virtual machines (VMs)](/azure/spring-apps/concepts-for-java-memory-management).

**Streamline infrastructure**. Simplify your workload to reduce the potential for interaction, dependency, and compatibility issues. When you simplify your workload, you optimize resource utilization of memory, processing power, and storage.

**Reduce load**. To reduce load on a workload, minimize the work or demand that's placed on an application, and enable resources to perform their primary tasks. For example, it's common practice to avoid running security solutions within your code or on individual compute instances. Instead, web servers should serve HTTP requests. Web application firewalls and gateway resources can handle security checks. The following strategies help reduce the load on your workload:

- *Eventual consistency*: Adopt an eventual consistency model to enhance performance by allowing data to be slightly dated. Eventual consistency reduces the immediate demand on CPU cycles and network bandwidth for constant data updates.
- *Delegate tasks*: Delegate server tasks to clients or intermediaries, such as search indexes and caches. Delegate tasks like sorting data, filtering data, or rendering views. When you offload these tasks, you reduce the workload on your servers and improve performance.

**Optimize the network**. To optimize a workload network for performance, configure and fine-tune the network infrastructure. Ensure that the workload can operate at its highest level of efficiency.

- *Network protocols*: Upgrade to modern protocols like HTTP/2, which enable multiple requests to be sent over a single connection. Modern protocols reduce the overhead of establishing new connections.

    > :::image type="icon" source="../_images/trade-off.svg":::**Tradeoff**: Modern protocols might exclude older clients.

- *Network chattiness*: Batch network requests together to reduce the number of requests. Instead of making multiple small requests, combine them into larger requests to reduce network overhead.
- *Database queries*: Ensure that database queries retrieve only the necessary information. Avoid retrieving large amounts of unnecessary data, which can lead to increased network traffic and slow performance.
- *Static data*: Utilize a content delivery network to cache frequently accessed static content that's close to the users. When you cache data, it doesn't have to travel over long distances. Caching improves response times and reduces network traffic.
- *Log collection*: Collect and retain only the log data that's necessary to support your requirements. Configure data collection rules and implement design considerations to optimize your Log Analytics costs.
- *Data compression*: Compress and bundle [HTTP content](/iis/configuration/system.webserver/httpcompression) and [file data](/windows/win32/fileio/file-compression-and-decompression) to allow fast transmission between clients and servers. Compression shrinks the data that a page or API returns and sends back to the browser or client app. Compression optimizes network traffic, which can accelerate application communication.

    > :::image type="icon" source="../_images/trade-off.svg":::
**Tradeoff**: Compression adds server-side and client-side processing. The application must compress, send, and decompress data. Multicast communication, or communication to multiple recipients, can create decompression overhead. You need to test and measure the performance variations before and after implementing data compression to determine if it's a good fit for your workload. For more information, see [Response compression in ASP.NET Core](/aspnet/core/performance/response-compression).

## Azure facilitation

**Optimization tools**. PerfInsights is a tool that provides insights into network performance and provides recommendations for optimizing Azure services and resources.

**Native SDKs and performance-optimized libraries**. Azure offers [SDKs](https://azure.microsoft.com/downloads) and libraries for various programming languages to interact with Azure services. Use SDKs to simplify interactions between applications and Azure resources. SDKs provide optimal interaction with Azure services, which reduces latency and enhances efficiency.

**Memory management**. Use [the smart detection feature of Application Insights](/azure/azure-monitor/alerts/proactive-diagnostics) to analyze memory consumption and help to identify and address memory leaks.

**Concurrency and parallelism**. [AKS](/azure/aks) supports deploying containerized applications, which improves parallel processing. [Azure Batch](/azure/batch/batch-technical-overview) is a cloud-based job scheduling service that you can use to enable parallel and high-performance computing without the need for infrastructure setup.

**Infrastructure consistency**. Implement [Azure Resource Manager templates](/azure/templates) to define and deploy infrastructure by using code. Use these templates to implement efficient, repeatable, and consistent resource deployments. [Azure Policy](/azure/governance/policy/overview) provides governance capabilities to ensure that resource deployments adhere to organizational best practices and standards.

**Monitor and analyze**. Log Analytics provides insights into code and infrastructure performance, which helps identify performance bottlenecks.
Azure Monitor offers full-stack monitoring, advanced analytics, and intelligent alerting to facilitate the identification and resolution of performance issues.

**Asynchronous programming**. Use scalable queuing services, like Azure Queue Storage and Azure Service Bus, to facilitate asynchronous programming. You can queue tasks and independently process them.

To support asynchronous operations, Azure Marketplace offers third-party queues and tools that you can integrate with Azure services.

## Related links

- [AKS](/azure/aks)
- [Application Insights smart detection feature](/azure/azure-monitor/alerts/proactive-diagnostics)
- [Asynchronous Request-Reply pattern](/azure/architecture/patterns/async-request-reply)
- [Avoid memory allocations](/dotnet/csharp/advanced-topics/performance)
- [Azure Batch](/azure/batch/batch-technical-overview)
- [Azure Policy](/azure/governance/policy/overview)
- [Azure Resource Manager templates](/azure/templates)
- [Azure SDKs](https://azure.microsoft.com/downloads)
- [Background jobs](/azure/architecture/best-practices/background-jobs)
- [Background jobs scaling and performance considerations](/azure/architecture/best-practices/background-jobs#scaling-and-performance-considerations)
- [Compress file data](/windows/win32/fileio/file-compression-and-decompression)
- [Compress HTTP content](/iis/configuration/system.webserver/httpcompression)
- [Define pod CPU and memory limits](/azure/aks/developer-best-practices-resource-management)
- [Event-Based Asynchronous pattern](/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap)
- [Java virtual machines (VMs)](/azure/spring-apps/concepts-for-java-memory-management)
- [Large object heap](/dotnet/standard/garbage-collection/large-object-heap)
- [Pool fragmentation](/dotnet/framework/data/adonet/sql-server-connection-pooling#pool-fragmentation)
- [Queue-Based Load Leveling pattern](/azure/architecture/patterns/queue-based-load-leveling)
- [Response compression in ASP.NET Core](/aspnet/core/performance/response-compression)
- [Storage queues and Service Bus queues](/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)
- [Task-Based Asynchronous pattern](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)

## Next steps

We recommend that you review the Performance Efficiency checklist to explore other concepts.

> [!div class="nextstepaction"]
> [Performance Efficiency checklist](checklist.md)
