---
title: Recommendations for scaling and partitioning
description: Learn best practices for scaling and partitioning to achieve performance efficiency in workloads.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for scaling and partitioning

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:**

|[PE:05](checklist.md)| **Optimize scaling and partitioning**. Incorporate reliable and controlled scaling and partitioning. The scale unit design of the workload is the basis of the scaling and partitioning strategy.|
|---|---|

This guide describes the recommendations for scaling and partitioning a workload. Scaling and partitioning are important strategies for achieving performance efficiency in workloads. Scaling is the ability to increase or decrease the resources allocated to a workload based on demand. Partitioning involves dividing the workload into smaller, manageable units to distribute data and processing across multiple resources. A workload that doesn't scale or partition might experience poor performance in high-demand periods and underutilized capacity in low-demand periods.

**Definitions**

|Term| Definition|
|---|---|
| Autoscale | A feature that automatically adjusts the capacity limits of a service based on predefined configurations, allowing it to scale up or down as needed.|
| Capacity | The upper limit or maximum capacity of a given service or feature.|
| Client affinity (session affinity) | The intentional routing of requests from a single client to a single server instance to help ensure consistent session management.|
| Consistency (distributed database)| The uniformity of data across multiple nodes in a distributed database, ensuring that all replicas have the same data at a given point in time. |
| Consistency (relational database)| The property of a transaction bringing a database from one valid state to another, maintaining data integrity. |
| Consistency level| A configuration that defines how and when data is replicated in a distributed database system, determining the tradeoff between consistency and performance. |
| Data locking| A mechanism used to prevent simultaneous updates to the same data.|
| Horizontal scaling| A scaling approach that adds instances of a given type of resource.|
| Optimistic concurrency| An approach for updating databases that uses snapshots to make updates instead of traditional locking mechanisms. |
| Partitioning | The process of physically dividing data into separate data stores.|
| Scalability | The ability of a workload to dynamically change its capacity limits to accommodate varying levels of demand.|
| Scale unit| A group of resources that must scale proportionately together to maintain balanced performance. |
| State affinity | The storage of client session data on a single server so that subsequent requests from the same client are handled by the same server. |
| Vertical scaling| A scaling approach that adds compute capacity to existing resources.|

## Key design strategies

Both scaling and partitioning contribute to performance efficiency by ensuring that resources are used effectively and the system can handle varying workloads. These practices are especially important in cloud environments where applications need to be flexible and adaptable to changing demands. Scaling ensures you can expand workload capacity to meet increasing demands. Partitioning allows you to divide tasks or data efficiently to handle these growing needs. The foundation of both these processes is the scale unit design of the workload. It dictates how your workload should grow and distribute tasks. By incorporating a reliable and controlled approach to scaling and partitioning, you can sidestep potential system inefficiencies.

### Optimize scaling

Optimize scaling is the process of adjusting the number of servers, instances, or resources to meet the fluctuating demands of a workload. It ensures that the workload can handle increased traffic or demands without experiencing performance degradation or downtime.

#### Choose a scaling strategy

Choosing a scaling strategy involves deciding between vertical or horizontal methods to enhance the capacity of a workload based on its specific requirements. Selecting the right strategy ensures that resources are adjusted efficiently to meet workload demands without overuse or waste. To choose the right scaling strategy, you need to understand the uses cases for vertical and horizontal scaling and how they meet the needs of your workload.

*Understand vertical scaling.* Using vertical scaling, you can increase the capacity of a single resource, such as upgrading to a larger server or instance size. Vertical scaling is useful when the workload can benefit from increased processing power, memory, or other resources within a single instance. Vertical scaling is appropriate for workloads that aren't easily divided into smaller parts or when the application architecture doesn't support horizontal scaling.

*Understand horizontal scaling.* Using horizontal scaling, you can add more instances or resources to distribute the workload across multiple servers. Horizontal scaling offers benefits such as improved resiliency, increased capacity, and the ability to handle increased traffic or workload demands. It's effective for cloud-native applications designed to run on multiple nodes. Horizontal scaling is appropriate for workloads that can be divided into smaller parts that run independently.

*Understand the workload.* The suitability of vertical or horizontal scaling depends on the specific characteristics and requirements of the workload. Regular performance monitoring and testing in the following areas can help optimize the scaling strategy over time:

- *Requirements*: Understand the specific requirements of the workload by considering factors such as resource demands, scalability needs, and the limitations of the workload.

- *Scale units*: Create a scale unit design for components expected to be scaled together. For example, 100 virtual machines might require two queues and three storage accounts to handle the extra workload. The scale unit would be 100 virtual machines, two queues, and three storage accounts. You should independently scale all the components that experience capacity-use fluctuation.

- *Architecture*: Assess the design of the application architecture. Some applications might be inherently designed to scale horizontally, with stateless components that can be easily distributed across multiple instances. Other applications might have stateful components or dependencies that make vertical scaling more appropriate. Evaluate the scalability and elasticity requirements of the workload.

#### Design for infrastructure scalability

Designing for infrastructure scalability is the process of creating an architecture that can handle increasing demands and workload by adding or adjusting resources as needed. It involves planning and implementing solutions that can scale horizontally or vertically to accommodate growth. Strategies include avoiding singletons that can become bottlenecks and decoupling application components to ensure independent scalability. When you design a workload to be scalable, it can effectively distribute the workload across multiple resources, which prevents bottlenecks and maximizes resource utilization.

##### Avoid singletons

You should avoid the use of a single, centralized resource for the entire workload. Instead, distribute your workload across multiple resources for better scalability, fault tolerance, and performance. Explore some specific examples and design considerations to avoid singletons in workload resources:

- *[Queue-based load leveling](/azure/architecture/patterns/queue-based-load-leveling)*: Instead of relying on a single queue to process messages, consider partitioning the workload across multiple queues to distribute the processing load. It provides better scalability and parallel processing.

- *Data processing*: Singleton patterns often appear in data processing scenarios where the processing doesn't fan out. Break long-running tasks into smaller tasks that can scale better to distribute the workload across multiple resources and take advantage of parallelism.

- *Design patterns*: Design patterns such as [Fan-out/Fan-in](/azure/azure-functions/durable/durable-functions-overview#fan-in-out) or [Pipes and Filters](/azure/architecture/patterns/pipes-and-filters) can help avoid singletons in workflows. These patterns enable the distribution of processing tasks across multiple resources and promote scalability and flexibility.

##### Decouple components

Decoupling application components is an important aspect of designing for scalability. It involves breaking down the application into smaller, independent components that can operate and scale independently based on specific workload requirements. For example, if one component requires more resources due to increased demand, you can scale that component without affecting the others. This flexibility ensures efficient resource allocation and prevents bottlenecks. By decoupling components, you can isolate failures and minimize the effect on the overall application. If one component fails, the other components can continue to function independently.

Decoupled components are easier to maintain and update. Changes or updates to one component can be made without affecting the others because they're independent. Follow these guidelines to decouple application components for scalability:

- *Separation of concerns*: Identify the responsibilities and functionalities of your application. Divide the responsibilities into separate components based on their specific tasks. For example, you might have separate components for user authentication, data processing, and UI.

- *Loose coupling*: Design the components to communicate with each other through well-defined interfaces and protocols. This design reduces dependencies between components and allows for easier replacement or scaling of individual components.

- *Asynchronous communication*: Use asynchronous communication patterns such as message queues or event-driven architectures to decouple components further. These patterns allow components to process tasks independently at their own pace, improving overall scalability.

- *Microservices*: Consider implementing microservices, which are small, independent services that focus on specific business functionalities. Each microservice can be developed, deployed, and scaled independently, providing greater flexibility and scalability.

#### Design for application scalability

As you scale a workload, you should design the application to distribute the load. Just because you can add more replicas at the infrastructure level doesn't mean your application can use the replicas. Avoid solutions that require client affinity, data locking, or state affinity for a single instance if possible. You want to route a client or process to a resource that has available capacity.

- *Eliminate server-side session state.* You should design applications to be stateless where possible. For stateful applications, you should use a state store that's external to your server. Externalizing session state is the practice of storing session data outside of the application server or container. You can externalize session state to distribute session data across multiple servers or services, enabling seamless session management in a distributed environment. Consider the following when externalizing session state:

    - *Evaluate your session requirements.* Understand the session data that needs to be stored and managed. Consider session attributes, session timeouts, and any specific requirements for session replication or persistence. Determine the size of your session state and the frequency of read and write operations.

    - *Choose a solution.* Select a storage solution that aligns with your performance and scalability needs. Options include using a distributed cache, a database, or a session state service. Consider factors such as data consistency, latency, and scalability when making your choice.

    - *Set up your application.* Update your application to use the chosen session state storage solution. You might need to change your application's configuration files or code to connect to the external storage service.

    - *Update your logic.* Change your application's session management logic to store and retrieve session data from the external storage solution. You might need to use APIs or libraries provided by the storage solution to manage session state.

- *Eliminate client affinity.* Client affinity is also known as session affinity or sticky sessions. When you eliminate client affinity, you distribute client requests evenly across multiple replicas or servers, without routing all requests from a client to the same replica. This configuration can improve the scalability and performance of applications by allowing any available replica to process the requests.

- *Review your load balancing algorithm.* A load balancing algorithm can cause unintentional and artificial client pinning where too many requests are sent to one back-end instance. This can happen if the algorithm is set up to always send requests from the same user to the same instance, or if the requests are too similar to each other.

- *Eliminate data locking.* Data locking ensures consistency but has performance disadvantages. It can cause lock escalations and negatively affect concurrency, latency, and availability. To eliminate data locking, you should implement [optimistic concurrency](/sql/connect/ado-net/optimistic-concurrency). Nonrelational databases should use [optimistic concurrency control](/azure/cosmos-db/nosql/database-transactions-optimistic-concurrency#optimistic-concurrency-control) and have the right [consistency level](/azure/cosmos-db/consistency-levels). Your data partitioning strategy should also support your concurrency needs.

- *Use dynamic service discovery.* Dynamic service discovery is the process of automatically detecting and registering services in a distributed system. It allows clients to discover available services without being tightly coupled to specific instances. Clients shouldn't be able to take a direct dependency on a specific instance in the workload. To avoid these dependencies, you should use a proxy to distribute and redistribute client connections. The proxy acts as an intermediary between clients and services, providing a layer of abstraction that allows services to be added or removed without affecting clients.

- *Use background tasks.* When an application is scaled, it can handle an increasing workload or a higher number of concurrent requests. Offloading intensive tasks as background tasks allows the main application to handle user requests without resource-intensive operations overwhelming it. Follow these steps to offload tasks as background tasks:

1. Find the CPU-intensive and I/O-intensive tasks in your application that you can offload. These tasks typically involve heavy computations or interactions with external resources such as databases or network operations.

1. Design your application to support background tasks. Decouple the intensive tasks from the main application logic and provide a mechanism to start and manage background tasks.

1. Implement background task processing with appropriate technologies or frameworks. Include features provided by your programming language or platform, such as asynchronous programming, threading, or task queues. Contain intensive operations in separate tasks or threads, these tasks can be run concurrently or scheduled to run at specific intervals.

1. Distribute background tasks if there are many of them, or if the tasks require substantial time or resources. For one possible solution, see the [Competing Consumers pattern](/azure/architecture/patterns/competing-consumers).

#### Implement scaling

Implement scaling in a workload by adjusting the available resources, such as computing power and storage, based on the changing demands of the workload. You can add or remove resources dynamically to accommodate variations in workload requirements.

- *Use services with autoscaling.* The autoscale feature automatically scales infrastructure to meet demand. Use platform as a service (PaaS) offerings with built-in autoscale features. The ease of scaling on PaaS is a major advantage. For example, scaling out virtual machines requires a separate load balancer, client-request handling, and externally stored state. PaaS offerings handle most of these tasks.

- *Understand service scaling boundaries.* When you understand service scaling limits, increments, and restrictions, you can make informed decisions when selecting a service. Scaling boundaries determine whether or not your chosen service can handle the expected workload, scale efficiently, and meet the performance requirements of your application. Scaling boundaries to consider include:

  - *Scaling limits*: Scaling limits are the maximum capacity that a location or service can handle. It's important to know these limits to help ensure that the service can accommodate the expected workload and handle peak usage without performance degradation. Every resource has an upper scale limit. If you need to go beyond scale limits, you should partition your workload.
  
  - *Scaling increments*: Services scale at defined increments. For example, compute services might scale by instances and pods while databases might scale by instances, transaction units, and virtual cores. It's important to understand these increments to optimize resource allocation and prevent resource flapping.
  
  - *Scaling restrictions*: Some services allow you to scale up or out but limit your ability to automatically reverse scaling. You're forced to scale in manually, or you might have to redeploy a new resource. These limitations are often to protect the workload. Scaling down or scaling in can have implications on the availability and performance of the workload. A service might enforce certain limitations or constraints to help ensure that the workload has sufficient resources to operate effectively. This can affect data consistency and synchronization, especially in distributed systems. The service might have mechanisms in place to handle data replication and consistency during scaling up or out but might not provide the same level of support for scaling down or in.

- *Use meaningful load metrics.* Scaling should use meaningful load metrics as scaling triggers. Meaningful load metrics include simple metrics, like CPU or memory, or more advanced metrics, such as queue depth, SQL queries, custom metrics queries, and HTTP queue length. Consider using a combination of simple and advanced load metrics as your scaling trigger.

- *Constrain autoscaling.* Set automatic scaling limits to minimize over-scaling that could result in unnecessary costs. Sometimes you can't set scaling limits. In these cases, you should set alerts to notify you when the component has reached the maximum scale limit and when it's over-scaled.

- *Use a buffer.* A buffer is unused capacity that can be used to handle spikes in demand. A well-designed workload plans for unexpected spikes in workload. You should add a buffer to handle spikes for horizontal and vertical scaling.

- *Prevent flapping.* Flapping is a looping condition that occurs when one scale event triggers an opposite scale event, creating a continuous back-and-forth scaling action. For example, if scaling in reduces the number of instances, it might cause the CPU usage to rise in the remaining instances, triggering a scale-out event. The scale-out event, in turn, causes the CPU usage to drop, repeating the process.

    It's important to choose an adequate margin between the scale-out and scale-in thresholds to avoid flapping. You can prevent frequent and unnecessary scale-in and scale-out actions by setting thresholds that provide a significant difference in CPU usage.

- *Use Deployment Stamps.* There are techniques that make it easier to scale a workload. You can use the [Deployment Stamps](/azure/architecture/patterns/deployment-stamp) pattern to easily scale a workload by adding one or more scale units.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: While scaling helps optimize costs by adjusting capacity to meet demand, it can result in overall increased cost during long periods of high demand.

#### Test scaling

Conduct load and stress tests to evaluate the performance of the workload under different scaling scenarios. You can use nonproduction environments to test vertical and horizontal scaling. Testing can help determine which scaling approach is most effective in improving performance efficiency. To test scaling in a workload, follow these steps:

1. Identify the key workload scenarios that you need to test, such as increasing user traffic, concurrent requests, data volume, or resource use.

1. Create a separate testing environment that closely resembles the production environment in terms of infrastructure, configuration, and data.

1. Define the performance metrics to measure, such as response time, throughput, CPU and memory utilization, and error rates.

1. Develop test cases that simulate different workload scenarios, gradually increasing the load to assess the performance at various levels.

1. Run the tests using the defined test cases and collect performance data at each load level. Monitor system behavior, resource consumption, and performance degradation.

1. Analyze the test results to identify performance bottlenecks, scalability limitations, or areas for improvement. Optimize the system configuration, infrastructure, or code to enhance scalability and performance. It takes time for scaling to complete, so test the effects of scaling delays.

1. Find potential dependency issues. Scaling or partitioning in one area of a workload might cause performance issues on a dependency. The stateful parts of a system, such as databases, are the most common cause of dependency performance issues. Databases require careful design to scale horizontally. You should consider measures, such as [optimistic concurrency](/dotnet/framework/data/adonet/sql/linq/optimistic-concurrency-overview) or data partitioning, to enable more throughput to the database.

1. Repeat the scalability tests after implementing optimizations to validate the improvements and help ensure the system can handle the expected workloads efficiently.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Consider the budget constraints and cost-efficiency goals of your workload. Vertical scaling might involve higher costs due to the need for larger and more powerful resources. Horizontal scaling offers cost savings by using smaller instances that can be added or removed based on demand.

### Partition workload

Partitioning is the process of dividing a large dataset or workload into smaller, more manageable parts called partitions. Each partition contains a subset of the data or workload and is typically stored or processed separately. Partitioning enables parallel processing and reduces contention. Dividing the workload into smaller units allows the application to process each unit independently. The result is better use of resources and faster processing times. Partitioning also helps distribute the data across multiple storage devices, reducing the load on individual devices and improving overall performance.

**Understand partitioning.** The specific partitioning approach you use depends on the type of data or workload you have and the technology you're using. Some common strategies for partitioning include:

- *Horizontal partitioning*: In this approach, the dataset or workload is divided based on specific criteria, such as ranges of values or specific attributes. Each partition contains a subset of the data that meets the defined criteria.

- *Vertical partitioning*: In this approach, the dataset or workload is divided based on specific attributes or columns. Each partition contains a subset of the columns or attributes, allowing for more efficient access to the required data.

- *Functional partitioning*: In this approach, the data or workload is divided based on the specific functions or operations that need to be performed. Each partition contains the data or components necessary for a specific function, enabling optimized processing and performance.

**Plan partitioning.** It's important to consider factors such as data distribution, query patterns, data growth, and system requirements when partitioning. Proper planning and design are essential to help ensure the effectiveness of partitioning and maximize performance efficiency. If you address partitioning as an afterthought, it's more challenging because you already have a live system to maintain. You might need to change data access logic, distribute large quantities of data across partitions, and support continued usage during data distribution.

**Implement partitioning.** It's important to analyze the characteristics of your data, access patterns, concurrency requirements, and scalability goals when deciding which type of partitioning to use. Each type of partitioning has its own advantages and considerations. Here are some factors to consider for each type of partitioning:

- *Horizontal partitioning* is appropriate when you want to distribute the data across multiple resources or servers for better scalability and performance. It's effective when the workload can be parallelized and processed independently on each partition. Consider horizontal partitioning when multiple users or processes need to be able to access or update the dataset concurrently.

- *Vertical partitioning* is appropriate when certain attributes or columns are frequently accessed, while others are accessed less frequently. Vertical partitioning allows for efficient access to the required data by minimizing unnecessary data retrieval.

- *Functional partitioning* is appropriate when different functions require different subsets of the data and can be processed independently. Functional partitioning can optimize performance by allowing each partition to focus on the specific operations it's designed for.

**Test and optimize partitioning.** Test the partitioning scheme to verify the effectiveness and efficiency of the strategy so you can make adjustments to improve performance. Measure factors such as response time, throughput, and scalability. Compare the results against performance goals and identify any bottlenecks or issues. Based on the analysis, identify potential optimization opportunities. You might need to redistribute data across partitions, adjust partition sizes, or change the partitioning criteria.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Partitioning adds complexity to the design and development of a system. Partitioning requires conversations and planning between developers and database administrators.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Partitioning introduces some potential problems that need to be considered and addressed, including:
>
> - *Data skew*: Partitioning can lead to data skew, where certain partitions receive a disproportionate amount of data or workload compared to others. Data skew can result in performance imbalances and increased contention on specific partitions.
>
> - *Query performance*: Poorly designed partitioning schemes can negatively affect query performance. If queries need to access data across multiple partitions, it might require extra coordination and communication between partitions, leading to increased latency.

## Azure facilitation

**Implement scaling.** Azure has the infrastructure capacity to support vertical and horizontal scaling. Azure services have different performance tiers known as SKUs. SKUs allow you to scale vertically. Many of Azure's resources support automatic scaling or other in-place scale options. Some resources support advanced metrics or custom input to support fine-tuning scaling behavior. Most scaling implementations in Azure can set limits and support the necessary observability to be alerted to change.

[Azure Monitor](/azure/azure-monitor/overview) allows you to monitor various metrics and conditions in your applications and infrastructure. You can use Monitor to trigger automated scaling actions based on predefined rules. For example, in Azure Kubernetes Service (AKS), you can use Monitor to enable horizontal pod automatic scaling (HPA) and cluster automatic scaling. Using Monitor's monitoring and alerting capabilities, you can effectively facilitate scaling in Azure and help ensure that your applications and infrastructure can dynamically adjust to meet demand.

**Build custom automatic scaling.** You can use alerts in Monitor for resources that don't have an autoscale feature. These alerts can be set up to be query-based or metric-based and can perform actions using [Azure Automation](/azure/automation/overview). Automation provides a platform for hosting and running PowerShell and Python code across Azure, the cloud, and on-premises environments. It offers features such as deploying runbooks on demand or on a schedule, run history and logging, integrated secrets store, and source control integration.

**Eliminate data locking.** In Azure SQL Database, you can enable [optimized locking](/sql/relational-databases/performance/optimized-locking) to improve performance on databases that require strict consistency.

**Use background tasks.** Azure offer services and guidance for implementing background jobs. For more information, see [Background jobs](/azure/architecture/best-practices/background-jobs).

**Implement load balancing.** Azure provides load balancers that don't require client affinity. These load balancers include [Azure Front Door](/azure/frontdoor/routing-methods#session-affinity), [Azure Application Gateway](/azure/application-gateway/features#session-affinity), and [Azure Load Balancer](/azure/load-balancer/load-balancer-distribution-mode?tabs=azure-portal).

**Implement partitioning.** Azure offers various partitioning strategies for different data stores. These strategies help improve performance and scalability by distributing the data across multiple partitions. For more information, see [Data partition strategies](/azure/architecture/best-practices/data-partitioning-strategies).

## Related links

- [Why partition data?](/azure/architecture/best-practices/data-partitioning#why-partition-data)
- [Best practices for automatic scaling](/azure/azure-monitor/platform/autoscale-best-practices#choose-the-thresholds-carefully-for-all-metric-types)
- [Overview of the autoscale feature in Azure](/azure/azure-monitor/platform/autoscale-overview)
- [Horizontal, vertical, and functional data partitioning](/azure/architecture/best-practices/data-partitioning)
- [Application design considerations](/azure/architecture/best-practices/data-partitioning#application-design-considerations)

## Performance Efficiency checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Performance Efficiency checklist](checklist.md)
