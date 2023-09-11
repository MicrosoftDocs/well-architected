---
title: Recommendations for scaling and partitioning
description: Learn best practices for scaling and partitioning.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for scaling and partitioning: PE 05

This guide describes the recommendations for scaling and partitioning a workload. Scaling and partitioning are important strategies for achieving performance efficiency in workloads. Scaling refers to the ability to increase or decrease the resources allocated to a workload based on demand. Partitioning involves dividing the workload into smaller, manageable units to distribute the data and processing across multiple resources. A workload that doesn't scale or partition experiences poor performance in high demand periods and underutilizes capacity in low demand periods.

**Definitions**

|Terms| Definition|
|---|---|
|Scalability| The ability of a workload to change capacity limits dynamically.|
| Autoscale|A feature of some services to automate changes in capacity limits based on configurations.|
| SKUs|Specification tiers for services.|
| Capacity|The upper limit of a given service or service feature.|
| Partitioning|Dividing a workload or data to manage it more efficiently.|
| Vertical scaling|Increasing the capacity of a resource to handle more demand.|
| Horizontal scaling|Adding instances of the resource with the same capacity to handle more demand.|
| Scale unit|Resources that need to scale proportionately together.|
| Client affinity|When requests from a single client are intentionally routed to a single server instance. It's also known as session affinity and instance pinning.|
| Data locking|A way to prevent simultaneous updates to the same piece of data.|
| State affinity|When a single server stores client session data.|
| Optimistic concurrency|An approach for updating databases that uses snapshots to make updates instead of database locks.|
| Consistency (distributed database)|The uniformity of data in a distributed database.|
| Consistency (relational database)|The idea that a transaction should bring a database from one state to another.|
| Consistency level| A configuration that describes how and when data is replicated.|

## Key strategies

### Scale

Scaling a workload refers to the process of adjusting the resources allocated to a workload. The adjustments include increasing or decreasing the number of servers, instances, or resources, to meet the changing demands of the workload. Scaling allows the workload to handle increased traffic or workload demands without experiencing performance degradation or downtime.

#### Understand scaling

Picking the right scaling strategy refers to selecting the appropriate approach, either vertical or horizontal scaling. You base the choice on the specific characteristics and requirements of a workload.

**Understand vertical scaling (scaling up).** Vertical scaling involves increasing the capacity of a single resource, such as upgrading to a larger server or instance size. It's suitable when the workload can benefit from increased processing power, memory, or other resources within a single instance. Vertical scaling is advantageous when the workload isn't easily divided into smaller parts or when the application architecture doesn't support horizontal scaling.

**Understand horizontal scaling (scaling out).** Horizontal scaling involves adding more instances or resources to distribute the workload across multiple servers. It's suitable when the workload can be divided into smaller parts that can run independently. Horizontal scaling offers benefits such as improved resiliency, increased capacity, and the ability to handle increased traffic or workload demands. It's effective for cloud-native applications designed to run on multiple nodes.

**Understand the workload.** It's important to note that the suitability of vertical or horizontal scaling depends on the specific characteristics and requirements of the workload. Regular performance monitoring and testing can help optimize the scaling strategy over time.

- *Requirements:* Understand the specific requirements of the workload. Consider factors such as resource demands, scalability needs, and the limitations of the workload.
- *Scale units.* A scale unit typically consists of multiple resources that support a flow or entire workload. You should document a scale unit design for components expected to be scaled together. For example, 100 virtual machines might require two queues and three storage accounts to handle the extra workload. The scale unit would be 100 virtual machines, two queues, and three storage accounts. You should independently scale all the components that experience capacity-use fluctuation
- *Architecture:* Assess the design of the application architecture. Some applications may be inherently designed to scale horizontally, with stateless components that can be easily distributed across multiple instances. Other applications may have stateful components or dependencies that make vertical scaling more suitable. Evaluate the scalability and elasticity requirements of the workload.

#### Design for infrastructure scalability

Designing for infrastructure scalability refers to the process of creating an architecture that can handle increasing demands and workload by adding or adjusting resources as needed. It involves planning and implementing solutions that can scale horizontally (adding more instances) or vertically (increasing the capacity of existing instances) to accommodate growth. When you design a workload to be scalable, it can effectively distribute the workload across multiple resources, preventing bottlenecks and maximizing resource utilization.

**Avoid singletons.** Avoid the use of a single, centralized resource for the entire workload. Instead, distribute the workload across multiple resources to achieve better scalability, fault tolerance, and performance. Avoiding singletons in workload resources is crucial for improving performance and scalability. Let's explore some specific examples and design considerations to avoid singletons in different aspects of workload resources:

- *[Queue-based load leveling](/azure/architecture/patterns/queue-based-load-leveling):* When working with queues, it's important to avoid singletons by designing queue processing with partitions and being agnostic about message ordering. Instead of relying on a single queue to process messages, consider partitioning the workload across multiple queues to distribute the processing load. It provides better scalability and parallel processing.
- *Data processing:* Singleton patterns often appear in data processing scenarios where the processing doesn't fan out. To avoid singletons in data processing, it's recommended to break long-running tasks into smaller tasks that can scale better. By decomposing the processing into smaller chunks, you can distribute the workload across multiple resources and take advantage of parallelism.
- *Design patterns:* Design patterns such as [*Fan-out/fan-in*](/azure/azure-functions/durable/durable-functions-overview#fan-in-out) or [*Pipes and filters*](/azure/architecture/patterns/pipes-and-filters) can help avoid singletons in workflows. These patterns enable the distribution of processing tasks across multiple resources and promote scalability and flexibility.

**Decouple components.** Decoupling application components is an important aspect of designing for scalability. It involves breaking down the application into smaller, independent components that can operate and scale independently. This decoupling allows each component to be scaled individually based on its specific workload and requirements.

Decoupling components allows you to scale each component independently based on its specific needs. For example, if one component requires more resources due to increased demand, you can scale that component without affecting the others. The flexibility ensures efficient resource allocation and prevents bottlenecks. By decoupling components, you can isolate failures and minimize the effect on the overall application. If one component fails, the other components can continue to function independently. Decoupled components are easier to maintain and update. Since they're independent, changes or updates to one component can be made without affecting the others. To decouple application components for scalability, you can follow these guidelines:

- *Separation of concerns:* Identify the different responsibilities and functionalities of your application. Divide these responsibilities into separate components based on their specific tasks. For example, you might have separate components for user authentication, data processing, and user interface.
- *Loose coupling:* Design the components to communicate with each other through well-defined interfaces and protocols. The design reduces dependencies between components and allows for easier replacement or scaling of individual components
- *Asynchronous communication:* Use asynchronous communication patterns such as message queues or event-driven architectures to decouple components further. These patterns allow components to process tasks independently and at their own pace, improving overall scalability.
- *Microservices:* Consider implementing microservices, which are small, independent services that focus on specific business functionalities. Each microservice can be developed, deployed, and scaled independently, providing greater flexibility and scalability.

### Design for application scalability

As you scale a workload, you must design the application to distribute load. Just because you can add more replicas at the infrastructure level doesn't mean your application can use the replicas. Where possible, avoid solutions that require client affinity, data locking, or state affinity for a single instance. You want to route a client or process to a resource that has available capacity.

**Eliminate server-side session state.** You should design applications to be stateless where possible. For stateful applications, you should use an external state store external to your server. Externalizing session state refers to the practice of storing session data outside of the application server or container. By externalizing session state, you can distribute session data across multiple servers or services, enabling seamless session management in a distributed environment. To externalize session state, you can follow these steps:

- *Evaluate your application's session requirements:* Understand the session data that needs to be stored and managed. You need to consider session attributes, session timeouts, and any specific requirements for session replication or persistence. Determine the size of the session state and the frequency of read and write operations.
- *Choose a session state storage solution:* Select a storage solution that aligns with your performance and scalability needs. Options include using a distributed cache, a database, or a session state service. Consider factors such as data consistency, latency, and scalability when making your choice.
- *Configure application:* Update your application's configuration to use the chosen session state storage solution. You might need to modify your application's configuration files or code to connect to the external storage service.
- *Update session management logic:* Modify your application's session management logic to store and retrieve session data from the external storage solution. You might need to use APIs or libraries provided by the storage solution to manage session state.

**Eliminate client affinity.** Client affinity is also known as session affinity or sticky sessions. When you eliminate client affinity, you distribute client requests evenly across multiple replicas or servers, without routing all requests from a client to the same replica. This configuration can improve the scalability and performance of applications by allowing any available replica to process the requests.

**Review your load balancing algorithm.** A load balancing algorithm can cause unintentional and artificial client pinning to a backend instance. Potential causes include enable session affinity or tuple-based hashes without enough request variation.

**Eliminate data locking.** Data locking ensures high consistency but has performance disadvantages. It can cause lock escalations and negatively affect concurrency, latency, and availability. To eliminate data locking, you should implement [optimistic concurrency](/sql/connect/ado-net/optimistic-concurrency). Nonrelational databases should use [optimistic concurrency control](/azure/cosmos-db/nosql/database-transactions-optimistic-concurrency#optimistic-concurrency-control) and have the right [consistency level](/azure/cosmos-db/consistency-levels). Your data partitioning strategy should also support your concurrency needs.

**Use dynamic service discovery.** Dynamic service discovery refers to the process of automatically detecting and registering services in a distributed system. It allows clients to discover available services without being tightly coupled to specific instances. Clients shouldn't be able to take a direct dependency on a specific instance in the workload. This approach provides flexibility and scalability to the system. To avoid these dependencies, you should use a proxy to distribute and redistribute client connections. The proxy acts as an intermediary between clients and services, distributing client requests and managing connections. It provides a layer of abstraction that allows services to be added or removed without affecting clients.

**Add retry logic.** Adding retry logic to an application when scaling is important to handle temporary failures and maintain application availability and reliability. When you scale an application, especially in a distributed system, there's a possibility of increased load and potential failures due to various factors such as network issues, resource limitations, or service disruptions. The retry logic allows the application to automatically retry failed operations, such as connecting to a service or making a network request, after a certain period of time. Retry logic helps to mitigate transient failures and gives the system a chance to recover without causing disruptions or impacting user experience.

- *Identify the operations or services that require retry logic.* These operations or services can include database connections, API calls, or any other external dependencies.
- *Determine the appropriate retry strategy based on the specific requirements of your application.* The configuration may involve setting the number of retries, the time interval between retries, and any extra conditions for retrying.
- *Implement the retry logic in your application code.* Wrap the relevant operations or service calls in a retry loop or using libraries or frameworks that provide built-in retry functionality.

**Use background tasks.** When an application scales, it means that it can handle an increasing workload or a higher number of concurrent requests. Offloading intensive tasks as background tasks allows the main application to focus on handling user requests without resource-intensive operations overwhelming it. To offload tasks as background tasks, you can follow these steps:

1. *Identify the tasks:* Find the CPU-intensive and I/O-intensive tasks in your application that you can offload. These tasks typically involve heavy computations or interactions with external resources such as databases or network operations.
1. *Support background tasks:* Design your application to support background tasks. The design involves decoupling the intensive tasks from the main application logic and providing a mechanism to start and manage background tasks.
1. *Implement the background tasks:* Use appropriate technologies or frameworks to implement background task processing. Include features provided by your programming language or platform, such as asynchronous programming, threading, or task queues. Encapsulate the intensive operations in separate tasks or threads. These tasks can then be executed concurrently or scheduled to run at specific intervals.
1. *Distribute background tasks.* If there are many background tasks, or the tasks require substantial time or resources, spread the work across multiple compute units, such as worker roles or background jobs. For one possible solution, see the [Competing consumers pattern](/azure/architecture/patterns/competing-consumers).

**Enable distributed tracing.** Enabling distributed tracing is a technique used to track and monitor requests as they flow through a distributed system. It allows you to trace the path of a request as it travels across multiple services and components, providing valuable insights into the performance and efficiency of your system. Distributed tracing is important for performance efficiency because it helps identify bottlenecks, latency issues, and areas for optimization within a distributed system. By visualizing the complete flow of a request, you can pinpoint where delays or inefficiencies occur and take appropriate actions to improve performance. To enable distributed tracing, you can follow these general steps:

1. *Instrumentation:* Start by instrumenting your applications and services to generate trace data. Use libraries or frameworks that support distributed tracing, such as OpenTelemetry.
1. *Trace propagation:* Ensure that trace information is propagated across service boundaries. You should typically pass a unique trace ID and other contextual information with each request.
1. *Trace collection:* Set up a centralized trace collection system. This system collects and stores the trace data generated by your applications and services.
1. *Visualization and analysis:* Use the trace data collected to visualize the end-to-end flow of requests and analyze the performance characteristics of your distributed system. Visualizations can help you identify bottlenecks, latency issues, and areas for optimization.

#### Implement scaling

Implementing scaling in a workload refers to the ability to adjust the available resources, such as computing power and storage, based on the changing demands of the workload. It involves adding or removing resources dynamically to accommodate variations in workload requirements.

**Understand scaling boundaries.** When you understand the service scaling limits, increments, and restrictions, you can make informed decisions when selecting a service. It ensures that the chosen service can handle the expected workload, scale efficiently, and meet the performance requirements of your application.

- *Scaling limits.* Scaling limits refer to the maximum capacity that a location or service can handle. It's important to know these limits to ensure that the service can accommodate the expected workload and handle peak usage without performance degradation. Every resource has an upper scale limit. If you need to go beyond scale limits, you should partition your workload. You must verify the capacity can meet your workload requirements.
- *Scaling increments:* Services scale at defined increments. Compute services might scale by instances and pods. Databases might scale by transaction units, virtual cores, or instances. It's important to understand these increments to optimize resource allocation and prevent resource flapping.
- *Scaling restrictions:* Some services allow you to scale (up or out) but limit your ability to undo that scaling automatically. You need to scale in manually, sometimes redeploying a new resource. The limitations are often to protect the workload. Scaling down or scaling in can have implications on the availability and performance of the workload. A service may enforce certain limitations or constraints to ensure that the workload has sufficient resources to operate effectively. It can affect data consistency and synchronization, especially in distributed systems. The service may have mechanisms in place to handle data replication and consistency during scaling up or out but may not provide the same level of support for scaling down or in.

**Use services with autoscaling.** Autoscaling automatically scales infrastructure to meet demand. Use platform-as-a-service (PaaS) offerings with built-in autoscaling. The ease of scaling on PaaS is a major advantage. For example, scaling out virtual machines (IaaS) requires a separate load balancer, client-request handling, and storing state externally. PaaS offerings handle most of these tasks.

**Use meaningful load metrics.** Scaling should use meaningful load metrics as scaling triggers. Meaningful load metrics include simple metrics (CPU, memory) or more advanced metrics (queue depth, SQL queries, custom metrics queries, HTTP queue length). You should consider using a combination of simple and advanced load metrics as your scaling trigger.

**Constrain autoscaling.** Set maximum scaling limits prevent autoscaling too much capacity and incur unnecessary costs. Sometimes you can't set scaling limits. In these cases, you should set alerts to notify you when the component has reached the maximum scale limit and when it's has over scaled.

**Use a buffer.** A buffer is unused capacity to handle spikes in demand. A well-designed workload plans for unexpected spikes in workload. You should add a buffer to handle spikes for horizontal and vertical scaling.

**Prevent flapping.** Flapping refers to a looping condition that causes a series of opposing scale events. It occurs when one scale event triggers an opposite scale event, creating a continuous back-and-forth scaling action. For example, if scaling-in reduces the number of instances, it may cause the CPU usage to rise in the remaining instances, triggering a scale-out event. The scale-out event, in turn, causes the CPU usage to drop, repeating the process.

To avoid flapping, it's important to choose an adequate margin between the scale-out and scale-in thresholds. By setting thresholds that provide a significant difference in CPU usage, you can prevent frequent and unnecessary scale-in and scale-out actions. By properly configuring your autoscaling rules and considering the margin between thresholds, you can control flapping and ensure that your scaling operations provide tangible capacity changes.

**Use deployment stamps.** There are techniques that make it easier to scale a workload. You can use the [deployment stamps](/azure/architecture/patterns/deployment-stamp) pattern, so that it's easily scaled by adding one or more scale units.

:::image type="icon" source="../_images/risk.svg"::: *Risk:* While scaling helps optimize costs by adjusting capacity to meet demand, it can also increase cost during long periods of increased demand.

#### Test scaling

Conduct load and stress tests to evaluate the performance of the workload under different scaling scenarios. Use nonproduction environments to test vertical and horizontal scaling on performance. This testing can help determine which scaling approach is more effective in improving performance efficiency. To test scaling in a workload, you can follow these steps:

1. *Define the test scenarios:* Identify the key workload scenarios that you need to test, such as increasing user traffic, concurrent requests, data volume, or resource utilization.
1. *Set up a test environment:* Create a separate testing environment that closely resembles the production environment in terms of infrastructure, configuration, and data.
1. *Determine performance metrics:* Define the performance metrics to measure, such as response time, throughput, CPU and memory utilization, and error rates.
1. *Design test cases:* Develop test cases that simulate different workload scenarios, gradually increasing the load to assess the performance at various levels.
1. *Execute the tests:* Run the tests using the defined test cases and collect performance data at each load level. Monitor system behavior, resource consumption, and any performance degradation.
1. *Analyze and optimize:* Analyze the test results to identify any performance bottlenecks, scalability limitations, or areas for improvement. Optimize the system configuration, infrastructure, or code to enhance scalability and performance. It takes time for scaling to complete, so test the affects scaling delays.
1. *Find dependency issues.* Scaling or partitioning in one area of a workload might cause performance issues on a dependency. The stateful parts of a system, such as databases, are the most common cause of dependency performance issues. Databases require careful design to scale horizontally. You need to consider measures, such as [optimistic concurrency](/dotnet/framework/data/adonet/sql/linq/optimistic-concurrency-overview) or data partitioning, to enable more throughput to the database.
1. *Repeat and validate:* Repeat the scalability tests after implementing optimizations to validate the improvements and ensure the system can handle the expected workloads efficiently.

:::image type="icon" source="../_images/trade-off.svg":::*Tradeoff:* Evaluate the cost implications of vertical and horizontal scaling. Vertical scaling may involve higher costs due to the need for larger and more powerful resources. Horizontal scaling can offer cost savings by utilizing smaller instances that can be added or removed based on demand. Consider the budget constraints and cost-efficiency goals of the workload.

### Partition

Partitioning refers to the process of dividing a large dataset or workload into smaller, more manageable parts called partitions. Each partition contains a subset of the data or workload and is typically stored or processed separately. Partitioning enables parallel processing and reduces contention. Dividing the workload into smaller units allows the application to process each unit independently. The result is better utilization of resources and faster processing times. Partitioning also helps distribute the data across multiple storage devices, reducing the load on individual devices and improving overall performance.

**Understand partitioning.** The specific partitioning approach you use depends on the type of data or workload and the technology being used. Some common strategies for partitioning include:

- *Horizontal partitioning:* This approach involves dividing the dataset or workload based on specific criteria, such as ranges of values or specific attributes. Each partition contains a subset of the data that meets the defined criteria.
- *Vertical partitioning:* In this approach, the dataset or workload is divided based on specific attributes or columns. Each partition contains a subset of the columns or attributes, allowing for more efficient access to the required data.
*Functional partitioning:* This approach involves dividing the data or workload based on the specific functions or operations that need to be performed. Each partition contains the data or components necessary for a specific function, enabling optimized processing and performance.

**Plan partitioning.** When implementing partitioning, it's crucial to consider factors such as data distribution, query patterns, data growth, and system requirements. Proper planning and design are essential to ensure the effectiveness of partitioning and maximize performance efficiency. If you address partitioning as an afterthought, it\'s more challenging because you already have a live system to maintain. You might need to change data access logic, distribute large quantities of data across partitions, and support continued usage during data distribution.

**Implement partitioning.** When deciding which type of partitioning to use, it's important to analyze the characteristics of your data, the access patterns, the concurrency requirements, and the scalability goals. Each type of partitioning has its own advantages and considerations, and the choice depends on the specific needs of your application. Here are some factors to consider for each type of partitioning:

- *Horizontal partitioning:* Use horizontal partitioning when you have a large dataset that can be divided into subsets based on specific criteria, such as ranges of values or specific attributes. Horizontal partitioning is suitable when you want to distribute the data across multiple resources or servers to achieve better scalability and performance. It's effective when the workload can be parallelized and processed independently on each partition. Consider horizontal partitioning when multiple users or processes needs to be access or update the dataset concurrently.
- *Vertical partitioning:* Vertical partitioning involves dividing the dataset based on specific attributes or columns. Use vertical partitioning when different parts of the dataset have distinct access patterns or usage frequencies. It's useful when certain attributes or columns are frequently accessed, while others are accessed less frequently. Vertical partitioning allows for efficient access to the required data by minimizing the amount of unnecessary data retrieval.
- *Functional partitioning:* Functional partitioning involves dividing the data based on specific functions or operations. Use functional partitioning when different parts of the dataset are associated with different functions or bounded contexts in the system. It's beneficial when different functions require different subsets of the data and can be processed independently. Functional partitioning can optimize performance by allowing each partition to focus on the specific operations it's designed for.

:::image type="icon" source="../_images/trade-off.svg":::*Tradeoff:* It's important to note that partitioning adds complexity to the design and development of a system. It requires conversations and planning between developers and database administrators.

**Test partitioning.**

Testing and optimizing the partitioning scheme involves verifying the effectiveness and efficiency of the partitioning strategy and making adjustments to improve performance. Execute test scenarios to evaluate the performance of the partitioning scheme. Measure factors such as response time, throughput, and scalability. Compare the results against performance goals and identify any bottlenecks or issues. Based on the analysis, identify potential optimization opportunities. You might need to redistribute data across partitions, adjust partition sizes, or change the partitioning criteria.

:::image type="icon" source="../_images/risk.svg"::: *Risk:* Partitioning introduces some potential problems that need to be considered and addressed:

- *Data skew:* Partitioning can lead to data skew, where certain partitions receive a disproportionate amount of data or workload compared to others. Data skew can result in performance imbalances and increased contention on specific partitions.
- *Query performance:* Poorly designed partitioning schemes can negatively affect query performance. If queries need to access data across multiple partitions, it may require extra coordination and communication between partitions, leading to increased latency.
- *Data distribution:* Incorrectly partitioning data can result in uneven data distribution. Some partitions may become overloaded with data, while others remain underutilized.

## Azure facilitation

**Implementing scaling:** Azure has the infrastructure capacity to support vertical and horizontal scaling. Azure services have different performance tiers known as SKUs. SKUs allow you to scale vertically. Many of Azure\'s resources support autoscaling or other in-place scale options. Some support advanced metrics or custom input to support fine-tuning scaling behavior. Most scaling implementations in Azure can set limits and support the necessary observability to be alerted to change.

Azure Monitor allows you to monitor various metrics and conditions in your applications and infrastructure, and trigger automated scaling actions based on predefined rules. For example, in Azure Kubernetes Service (AKS), you can use Azure Monitor to enable horizontal pod autoscaling (HPA) and cluster autoscaling. By using Azure Monitor's monitoring and alerting capabilities, you can effectively facilitate scaling in Azure and ensure that your applications and infrastructure can dynamically adjust to meet demand.

**Building custom autoscaling:** For resources that don't have autoscale, can utilize Alerts in Azure Monitor. You can configure these alerts to be query-based or metric-based and can perform actions using Azure Automation. Azure Automation provides a platform for hosting and running PowerShell and Python code across Azure, non-Azure cloud, and on-premises environments. It offers features such as executing runbooks on demand or on a schedule, execution history and logging, integrated secrets store, and source control integration.

**Eliminating data locking:** In Azure SQL Database, you can enable [optimized locking](/sql/relational-databases/performance/optimized-locking) to improve performance on databases that require strong consistency.

**Using background tasks:** Azure offer services and guidance for implementing background jobs. For more information, see [Background jobs](/azure/architecture/best-practices/background-jobs).

**Load balancing.** Azure provides load balancers that don't require client affinity. These load balancers include [Azure Front Door](/azure/frontdoor/routing-methods#session-affinity) (global), [Azure Application Gateway](/azure/application-gateway/features#session-affinity) (regional), and [Azure Load Balancer](/azure/load-balancer/load-balancer-distribution-mode?tabs=azure-portal) (global and regional).

**Implement partitioning.** Azure offers various partitioning strategies for different data stores. These strategies help improve performance and scalability by distributing the data across multiple partitions. For more information, see the following links:

- [Partitioning Azure SQL Databases](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-sql-database)
- [Partitioning Azure table storage](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-table-storage)
- [Partitioning Azure blob storage](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-blob-storage)
- [Partitioning Azure storage queues](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-storage-queues)
- [Partitioning Azure Service Bus](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-service-bus)
- [Partitioning Azure Cosmos DB](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-cosmos-db)
- [Partitioning Azure Search](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-search)
- [Partitioning Azure Cache for Redis](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-cache-for-redis)
- [Partitioning Azure Service Fabric](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-service-fabric)
- [Partitioning Azure Event Hubs](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-event-hubs)

## Related links

- [Why partition data?](/azure/architecture/best-practices/data-partitioning#why-partition-data)
- [Best practices for autoscale](/azure/azure-monitor/platform/autoscale-best-practices#choose-the-thresholds-carefully-for-all-metric-types)
- [Overview of autoscale in Azure](/azure/azure-monitor/platform/autoscale-overview)
- [Horizontal, vertical, and functional data partitioning](/azure/architecture/best-practices/data-partitioning)
- [Application design considerations](/azure/architecture/best-practices/data-partitioning#application-design-considerations)
