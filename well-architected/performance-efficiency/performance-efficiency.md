---
title: Performance efficiency checklist
description: Review checklist guidance for Azure performance efficiency, including application design, data management, and implementation.
author: martinekuan
ms.author: martinek
ms.date: 04/27/2023
ms.topic: conceptual
ms.custom:
  - design checklist
---

# Performance efficiency checklist

[Performance efficiency](index.yml), one of the pillars of the [Microsoft Azure Well-Architected Framework](../pillars.md), is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. Use this checklist to review your application architecture from a performance efficiency standpoint.

## Application design

- **Design for scaling**. Scaling allows applications to react to variable load by increasing and decreasing the number of roles, queues, and other services they use. The application must be designed with scaling in mind.

  The application and the services it uses must be stateless, so requests can be routed to any instance and adding or removing specific instances doesn't adversely affect users. Implement configuration, autodetection, or load balancing, so as services are added or removed, the application can do the necessary routing.

  For example, a web application might use a set of queues in a round-robin approach to route requests to background services running in worker roles. The web application must be able to detect changes in the number of queues to successfully route requests and balance the load on the application.
  
  You can scale outbound connectivity to the internet with [Azure NAT Gateway](/azure/virtual-network/nat-gateway/nat-overview). Azure NAT Gateway provides a scalable, reliable, and secure way to connect outbound traffic to the internet, and helps prevent connection failures caused by [SNAT exhaustion](/azure/load-balancer/troubleshoot-outbound-connection). 

- **Partition the workload**. Design parts of the process to be discrete and decomposable. Minimize the size of each part, and follow the usual rules for separation of concerns and the single responsibility principle. These practices allow the component parts to be distributed to maximize use of each compute unit, such as a role or database server.

  Partitioning the workload also makes it easier to scale the application by adding instances of specific resources. For complex domains, consider adopting a [microservices architecture](/azure/architecture/guide/architecture-styles/microservices).

- **Scale as a unit**. Plan for more resources to accommodate growth. For each resource, know the upper scaling limits, and use sharding or decomposition to go beyond these limits. Scaling as a unit also makes operations less prone to negative impact from resource limitations in other parts of the overall system.

  To make scale-out operations easier, determine the scale units for the system in well-defined sets of resources. For example, adding *x* number of web and worker roles might require *y* added queues and *z* storage accounts to handle the added workload. So a scale unit could consist of *x* web and worker roles, *y* queues, and *z* storage accounts.

  Design the application to easily scale by adding one or more scale units. Consider using the [deployment stamps pattern](/azure/architecture/patterns/deployment-stamp) to deploy scale units.

- **Take advantage of platform autoscaling**. If the hosting platform, such as Azure, supports autoscaling, prefer it to custom or third-party mechanisms unless the built-in mechanism can't fulfill your requirements. Schedule scaling rules where possible to ensure resources are available without a startup delay. Add reactive autoscaling to the rules where appropriate to cope with unexpected changes in demand. For more information, see [Autoscaling guidance](/azure/architecture/best-practices/auto-scaling).

  > [!NOTE]
  > You can use autoscaling operations and add custom counters to rules in the older Azure Classic deployment model. For more information, see [Increase a VM-family vCPU quota for the Classic deployment model](/azure/azure-portal/supportability/classic-deployment-model-quota-increase-requests).

- **Avoid client affinity**. Where possible, ensure that the application doesn't require affinity. Requests can then be routed to any instance, and the number of instances is irrelevant. You also avoid the overhead of storing, retrieving, and maintaining state information for each user.

- **Offload CPU-intensive and I/O-intensive tasks as background tasks**. If a request to a service is expected to take a long time to run or consume many resources, offload the processing for this request to a separate task. Depending on hosting platform, use worker roles or background jobs to run these tasks. This strategy enables the service to continue receiving requests and remain responsive. For more information, see [Background jobs guidance](/azure/architecture/best-practices/background-jobs).

- **Distribute the workload for background tasks**. If there are many background tasks, or the tasks require substantial time or resources, spread the work across multiple compute units, such as worker roles or background jobs. For one possible solution, see the [Competing consumers pattern](/azure/architecture/patterns/competing-consumers).

- **Move toward a *shared-nothing* architecture**. A shared-nothing architecture uses independent, self-sufficient nodes that have no single point of contention like shared services or storage. In theory, such a system can scale almost indefinitely.

  While a fully shared-nothing approach isn't practical for most applications, it might provide opportunities to design for better performance. For example, avoiding the use of server-side session state, client affinity, and data partitioning are good examples of moving toward a shared-nothing architecture.

## Data management

- **Use data partitioning**. Divide the data across multiple databases and database servers. You can design the application to use data storage services that provide partitioning transparently, such as Azure SQL Database elastic pools and Azure Table Storage. This approach can help maximize performance and allow easier scaling. Benefits of partitioning include better query performance and availability, simpler scalability, and easier management.

  Different partitioning techniques include *horizontal*, *vertical*, and *functional*. You can combine these techniques to achieve maximum benefits. Match different data store types to different data types, and choose data store types that are optimized for specific types of data. For example, you can use table storage, a document database, or a column-family data store instead of, or along with, a relational database. For more information, see [Data partitioning guidance](/azure/architecture/best-practices/data-partitioning).

- **Design for eventual consistency**. Eventual consistency improves scalability by reducing or removing the time needed to synchronize related data partitioned across multiple stores. The cost is that data isn't always consistent when it's read, and some write operations might cause conflicts. Eventual consistency is ideal for situations where the same data is read frequently but written infrequently. For more information, see the [Data consistency primer](/previous-versions/msp-n-p/dn589800(v=pandp.10)).

- **Reduce chatty interactions between components and services**. Avoid designing interactions in which an application is required to make multiple calls to a service, each of which returns a small amount of data. Instead use a single call that can return all the data. For example, use stored procedures in databases to encapsulate complex logic, reduce the number of round trips, and help prevent resource locking.

  Where possible, combine several related operations into a single request when the call is to a service or component that has noticeable latency. This practice makes it easier to monitor performance and optimize complex operations. 

- **Use queues to level the load for high velocity data writes**. Surges in demand can overwhelm a service and cause escalating failures. The [queue-based load leveling pattern](/azure/architecture/patterns/queue-based-load-leveling) can help prevent this situation. This pattern uses a queue as a buffer between a task and a service that it invokes. The queue can smooth intermittent heavy loads that might otherwise cause the service to fail or the task to time out.

- **Minimize the load on the data store**. The data store is a costly resource, is often a processing bottleneck, and isn't easy to scale out. It's easier to scale out the application than the data store, so you should attempt to do as much as possible of the compute-intensive processing within the application.

  Where possible, remove logic like processing XML documents or JSON objects from the data store, and do the processing within the application. For example, avoid passing XML to the database, other than as an opaque string for storage. Serialize or deserialize the XML within the application layer and pass it in a form that's native to the data store.


- **Minimize the volume of data retrieved**. Retrieve only the data you require by specifying columns and using criteria to select rows. Use table value parameters and the appropriate isolation level. Use mechanisms such as entity tags to avoid retrieving data unnecessarily.

- **Aggressively use caching**. Use caching wherever possible to reduce the load on resources and services that generate or deliver data. Caching is best suited to data that's relatively static, or that requires considerable processing to get. Where appropriate, caching should occur in each layer of the application, including data access and user interface generation. For more information, see [Caching guidance](/azure/architecture/best-practices/caching).

- **Handle data growth and retention**. The amount of data stored by an application grows over time. This growth increases storage costs and data access latency, affecting application throughput and performance. If possible, periodically archive some of the old data that's no longer accessed. Or, move rarely accessed data into long-term storage that's more cost effective, even if access latency is higher.

- **Optimize Data Transfer Objects (DTOs) by using an efficient binary format**. DTOs are passed between the layers of an application many times. Minimizing their size reduces the load on resources and the network. Balance the savings with the overhead of converting the data to the required format in each location where it's used. To encourage easy reuse of a component, adopt a format that has the maximum interoperability.

- **Set cache control**. To minimize processing load, design and configure the application to use output caching or fragment caching where possible.

- **Enable client side caching**. Web applications should enable cache settings on content that can be cached, which are often disabled by default. Configure the server to deliver the appropriate cache control headers to enable caching of content on proxy servers and clients.

- **Use Azure Blob Storage and the Azure Content Delivery Network to reduce the load on the application**. Consider storing static or relatively static public content, such as images, resources, scripts, and style sheets, in blob storage. This approach relieves the application from the load of dynamically generating this content for each request.

  Also consider using the Content Delivery Network to cache this content and deliver it to clients. Using the Content Delivery Network can improve performance at the client because the content is delivered from the geographically closest datacenter that contains a Content Delivery Network cache. For more information, see [Best practices for using content delivery networks](/azure/architecture/best-practices/cdn).

- **Optimize and tune SQL queries and indexes**. Some T-SQL statements or constructs might have an adverse effect on performance that can be reduced by optimizing the code in a stored procedure. For example, avoid converting **datetime** types to **varchar** before comparing with a **datetime** literal value. Use date/time comparison functions instead.

  Lack of appropriate indexes can also slow query execution. If you use an object/relational mapping framework, understand how it works and how it might affect performance of the data access layer. For more information, see [Query tuning](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

- **Denormalize data**. Data normalization helps to avoid duplication and inconsistency. However, data normalization imposes an overhead that can affect performance. Overhead includes maintaining multiple indexes, checking for referential integrity, performing multiple accesses of small chunks of data, and joining tables to reassemble the data. Consider whether some added storage volume and duplication is acceptable to reduce the load on the data store.

  Also consider if the application itself, which is typically easier to scale, can be relied on to take over tasks such as managing referential integrity to reduce the load on the data store. For more information, see [Data partitioning guidance](/azure/architecture/best-practices/data-partitioning).

## Implementation

- **Avoid performance antipatterns**. Review the [performance antipatterns for cloud applications](/azure/architecture/antipatterns/) for common practices that often cause performance problems when applications are under pressure.

- **Use asynchronous calls**. If possible, use asynchronous code to access resources or services that might be limited by I/O or network bandwidth or have a noticeable latency. Asynchronous calls avoid locking the calling thread.

- **Avoid locking resources, and use an optimistic approach instead**. Locking access to resources such as storage or other services that have noticeable latency is a primary cause of poor performance. Always use optimistic approaches to managing concurrent operations like writing to storage. Use storage layer features to manage conflicts. In distributed applications, data might be only eventually consistent.

- **Compress highly compressible data over high latency, low bandwidth networks**. In most web applications, HTTP responses to client requests are the largest volume of data generated by the application and passed over the network. HTTP compression can reduce this volume, especially for static content.

  Compressing dynamic content can reduce cost and load on the network, but applies a slightly higher load on the server. In more generalized environments, data compression can reduce the volume of data transmitted and minimize transfer time and costs, but the compression and decompression processes incur overhead.

  > [!NOTE]
  > Compression should be used only when there's a demonstrable gain in performance. Other serialization methods like JSON or binary encodings might reduce payload size and have less impact on performance, whereas XML is likely to increase it.

- **Minimize the time that connections and resources are in use**. Maintain connections and resources only for as long as you need to use them. Open connections as late as possible, and allow them to be returned to the connection pool as soon as possible. Acquire resources as late as possible, and dispose of them as soon as possible.

- **Minimize the number of connections required**. Service connections absorb resources. Limit the number of required service connections, and ensure that existing connections are reused whenever possible. For example, after authentication, use impersonation where appropriate to run code as a specific identity. Impersonation helps make the best use of the connection pool by reusing connections.

   > [!NOTE]
   > APIs for some services automatically reuse connections, if service-specific guidelines are followed. Understand the conditions that enable connection reuse for each service that your application uses.

- **Send requests in batches to optimize network use**. For example, send and read messages in batches when you access a queue, and do multiple reads or writes as a batch when you access storage or a cache. Batching can help to maximize efficiency of the services and data stores by reducing the number of calls across the network.

- **Avoid requirements to store server-side session state**. Server-side session state management typically requires client affinity, or routing each request to the same server instance, which affects the system's ability to scale. Where possible, design clients to be stateless with respect to the servers that they use. However, if the application must maintain session state, store sensitive data or large volumes of per-client data in a distributed server-side cache that all instances of the application can access.

- **Optimize table storage schemas**. When using table stores that require the table and column names to be passed and processed with every query, such as Table Storage, consider using shorter names to reduce overhead. However, don't sacrifice readability or manageability by using overly compact names.

- **Create resource dependencies during deployment or at application startup**. Avoid repeated calls to methods that test the existence of a resource and then create the resource if it doesn't exist. Methods such as `CloudTable.CreateIfNotExists` and `CloudQueue.CreateIfNotExists` in the Azure Storage client library follow this pattern. These methods can impose considerable overhead if they're invoked before each access to a storage table or storage queue.

  Instead, create the required resources when the application is deployed, or when it first starts. A single call to `CreateIfNotExists` for each resource in the startup code for a web or worker role is acceptable. However, be sure to handle exceptions that might arise if your code attempts to access a resource that doesn't exist. In these situations, you should log the exception, and possibly alert an operator that a resource is missing.

  Under some circumstances, it might be appropriate to create the missing resource as part of the exception handling code. Use this approach with caution, because the nonexistence of the resource might indicate a programming error, a misspelled resource name, or some other infrastructure-level issue.

- **Use lightweight frameworks**. Carefully choose the APIs and frameworks you use to minimize resource usage, execution time, and overall load on the application. For example, using a web API to handle service requests can reduce the application footprint and increase execution speed. But a web API might not be suitable for advanced scenarios where the added capabilities of Windows Communication Foundation are required.

- **Minimize the number of service accounts**. For example, use a specific account to access resources or services that impose a limit on connections or perform better when fewer connections are maintained. This approach is common for services such as databases, but can affect accurate auditing due to the impersonation of the original user.

- **Carry out performance profiling and load testing** during development, as part of test routines, and before final release to ensure the application performs and scales as required. This testing should occur on the same type of hardware as the production platform. Use the same types and quantities of data and user load as in production. For more information, see [Test the performance of a cloud service](/azure/vs-azure-tools-performance-profiling-cloud-services).

## Next steps
> [!div class="nextstepaction"]
> [Tradeoffs for performance efficiency](tradeoffs.md)
