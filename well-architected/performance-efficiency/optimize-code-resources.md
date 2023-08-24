---
title: Recommendations for optimizing code and resource use
description: Learn best practices for optimizing code and resource use.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing code and resource use: PE 07

This guide describes the best practices for optimizing code and resource use. Optimizing code and infrastructure focuses on using resources for their core purpose and only using them when necessary. Overuse of code and infrastructure creates unnecessary resource consumption, bottlenecks, and slow responsiveness. To compensate, you need to add more resources to accomplish the same tasks.

## Definitions

|Terms|                           Definition|
 |-|-|
 | Heap|                                An area in memory where runtime memory allocations take place.|
|  Memory leak|                         When a workload fails to release allocated memory after it's not needed.|
|  CPU architecture                    |The components and principles that affect the way the computer works.|
|  Data compression                    |Reducing the size of files by minimizing redundant data.|
|  O(N)|                                Big O notation for execution time that increases as the size of the input increases|
|  O(1)|                                Big O notation for execution time that is constant regardless of the input size.|

## Key design strategy

### Application

**Optimize code.** Optimized code is efficient and easy to manage. You want your code to accomplish operations in the least amount of CPU cycles possible. There are techniques you should use to optimize your code. The techniques include replacing O(N) operations with O(1) equivalents. You should remove unnecessary busy-waiting conditions and use object and connection pooling.

**Use native SDK or performance optimized libraries.** Native SDKs are designed to interact with the services and resources on a platform or within a framework. For example, cloud native SDKs work better with cloud service data planes than custom API access as they are often more performant handling network requests and excel at optimizing their interactions. Performance-optimized libraries, such as Math.NET, contain performance optimized functions that can improve performance when applied appropriately.

**Debug memory leaks.** Memory leaks deplete available memory if unaddressed. You need to monitor memory usage and update your code when you suspect a memory leak. The code review should look for code that allocates memory without explicit deallocation. Analyzing the stack traces can help you pinpoint the section of code responsible for the memory leak.

**Reduce memory allocations.** Too many memory allocations can degrade application performance generate errors. There are several techniques to minimize large memory allocations:

-   **Use local variables instead of global ones.** Global variables hold memory throughout the execution of a program. But local variables only consume memory when the program needs them. A function or code block creates the local variable and destroys the local variable when it exits.

-   **Use lazy initialization.** Lazy initialization is a design pattern that defers the creation of objects or resources until the program needs them. The object or resource only occupies memory when first needed. Implementing this design pattern on large objects can significantly reduce memory usage.

-   **Use buffer management.** Buffer management helps avoid allocating large buffers that occupy memory.

-   **Use object pooling**. Object pooling reuses large objects instead of allocating and deallocating them.

For more information, see [Avoid memory allocations](/dotnet/csharp/advanced-topics/performance/) and [Large object heap (LOH)](/dotnet/standard/garbage-collection/large-object-heap).

**Utilize processor capabilities.** Code should take advantage of the CPU architecture and threading models. 

**Manage data freshness.** Allowing data to be slightly dated can improve the performance of your workload. By allowing eventual consistency, don't need to use CPU cycles or network bandwidth to update data as frequently. With this mindset, you can delegate some sever tasks to clients or intermediaries, such as search indexes and caches. These tasks could be sorting data, filtering data, or rendering views.

**Use** **connection pooling.** Establishing a connection to a database is an expensive operation. It requires creating an authenticated network connection to the remote database server. Database connections are especially expensive for applications that open new connections frequently. Connection pooling reuses existing connections and avoids the expense of opening a new connection for each request. Connection pooling reduces connection latency and enables higher database throughput (transactions per second) on the server. You should choose a pool size that can handle more connections than you currently have. The goal is to have the connection pool quickly handle a new incoming request.

-   **Understand connection pooling limits.** Azure limits the number of network connections for some services. Exceeding this limit causes connections to be slowed down or terminated. With connection pooling, a fixed set of connections is established at startup time and maintained. In many cases, a default pool size might consist only of a small handful of connections that perform quickly in basic test scenarios. Your application might exhaust the default pool size under scale and create a bottleneck. You should establish a pool size that maps to the number of concurrent transactions supported on each application instance.

-   **Test the connection pool.** Each database and application platform has slightly different requirements for the right way to set up and use the pool. In all cases, testing is important to ensure a connection pool is properly established and works as designed under load.

**Use** **asynchronous programming.** Asynchronous programming is an approach that enables a workload to execute multiple operations without waiting for another operations to complete. Traditional synchronous programming executes tasks sequential and can cause performance bottlenecks. The workload cannot process other requests until the previous one finishes. Asynchronous programming is a critical pattern for enabling performance efficiency. It's and is available in most modern programming languages and platforms.

There are many ways to inject asynchronous programming. For HTTP traffic, consider using the [asynchronous request-reply pattern](/azure/architecture/patterns/async-request-reply). For .NET/C# programming, see [Asynchronous programming with async and await](/dotnet/csharp/async), [Task-based asynchronous pattern](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap), and [Event-based asynchronous pattern](/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap).

**Implement** **a queue.** Queues enable asynchronous processing. A queue is a storage buffer located between a requesting component (producer) and the processing component (consumer) of the workload. There can be multiple consumers for a single queue. As the tasks increase, you should scale the consumers to meet the demand. The requesting component places tasks in the queue, the queue stores them until the processing component has capacity. A queue is often the best way to hand off work to a processing service that experiences peaks in demand. To learn more about queue-based load leveling, see [Queue-based load leveling pattern](/azure/architecture/patterns/queue-based-load-leveling). Also see [Storage queues and Service Bus queues compared and contrasted](/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted).

**Optimize** **background jobs.** Many types of applications require background tasks that run independently of the user interface. The application can start the job and continue to process interactive requests from users. Examples include batch jobs, intensive processing tasks, and long-running processes such as workflows. Background tasks shouldn't block the application or cause inconsistencies due to delayed operation when the system is under load. You can improve performance by scaling the compute instances that host the background tasks. For more information, see [Background jobs](/azure/architecture/best-practices/background-jobs) and [Background jobs scaling and performance considerations](/azure/architecture/best-practices/background-jobs#scaling-and-performance-considerations).

**Optimize performance the right way.** Performance efficiency must be quantitative. You should never assume an application change improves performance. You should measure the performance effects of all application updates. Don't mask performance issues with brute force. Brute force means adding more compute resources to compensate for code performance through additional capacity instead of addressing the source. You need to fix performance issues through optimization. 

### Network 

**Optimize network communications.** Reducing the amount of network communication is a good way to improve it. For example, you can batch requests and reduce the chattiness of an application.

**Optimize database queries.** Queries can return large amounts of data. You should ensure database queries only retrieve the information needed.

**Use faster protocols.** You should upgrade to modern protocols. For example, HTTP/2 sends multiple requests over a single connection. [gRPC](/dotnet/architecture/cloud-native/grpc) is a high-performance framework that provides low latency and high throughput.

**Consider data compression.** Compressing and bundling [HTTP content](/iis/configuration/system.webserver/httpcompression/) and [file data](/windows/win32/fileio/file-compression-and-decompression) allows faster transmission between clients and servers. Compression shrinks the data returned from a page or API back to the browser or client app. It optimizes network traffic that can potentially accelerate application communication. However, it's only a potential gain. Compressions adds additional server side and client side processing. The application must compress, send, and decompress data. Multi-cast communication (multiple recipients) can create even more decompression overhead. You need to test and measure the performance variations before and after implementing data compression to determine if it's a good fit for your workload. For more information, see [Response compression in ASP.NET Core](/aspnet/core/performance/response-compression?preserve-view=true&view=aspnetcore-3.1).

### Infrastructure

**Add resource usage** **limits wherever possible.** You can put utilization limits on some workload components. For example, you can define [pod CPU and memory limits](/azure/aks/developer-best-practices-resource-management) in AKS to remove unstable pods. You can define memory limits in [Java virtual machines](/azure/spring-apps/concepts-for-java-memory-management) to optimize performance.

**Eliminate unnecessary infrastructure.** Simplifying your workload reduces the potential for issues related to interactions, dependencies, and compatibility. By removing these elements, you optimize resource utilization of memory, processing power, and storage for essential resources.

**Offload and externalize responsibility.** A performant workload allows resources to focus on their primary tasks. For example, you should avoid running security solutions within your code or on individual compute instances. Web servers should focus on server HTTP requests, not running security checks. You should run web application firewalls on gateways resources.

## Azure facilitation

-   Azure Application Insights has a [smart detection feature](/azure/azure-monitor/alerts/proactive-potential-memory-leak) that automatically analyzes the memory consumption of each process in your application. It can warn you about potential memory leaks.

-   You can define [pod CPU and memory limits](/azure/aks/developer-best-practices-resource-management) in AKS to remove unstable pods.

-   You can define memory limits in [Java virtual machines](/azure/spring-apps/concepts-for-java-memory-management) to optimize performance.

-   Highly scalable queuing services are natively supported in Azure. These services include Azure Queue Storage, a simple queuing service based on Azure Storage, and Azure Service Bus, a message broker service that supports transactions and reduced latency. Many third-party queues are available in the [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace).

-   [Logic Apps](/azure/logic-apps/logic-apps-overview) is a serverless pay-per-use consumption service that enables a set of ready-to-use out-of-the-box connectors and a long-running workflow engine to quickly meet cloud-native integration needs. Logic Apps is flexible enough to support scenarios like running tasks or jobs, advanced scheduling, and triggering.

## Tradeoff

-   Optimizing code requires a team with the skills to review and improve code.

-   Optimizing workload infrastructure might require rearchitecting your workload.

-   Connection pooling could create [pool fragmentation](/dotnet/framework/data/adonet/sql-server-connection-pooling#pool-fragmentation) and degrade performance.

