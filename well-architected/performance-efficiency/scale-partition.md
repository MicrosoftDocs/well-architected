---
title: Recommendations for scaling and partitioning
description: Learn best practices for for scaling and partitioning.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for scaling and partitioning: PE 05

This guide describes the recommendations for scaling and partitioning. Scaling and partitioning helps ensure there are enough resources available to meet legitimate usage demands. Scaling and partitioning are necessary to ensure performance efficiency and optimal resource utilization in a workload. A workload that doesn't scale or partition experiences poor performance in high demand periods and underutilizes capacity in low demand periods.

## Definitions

|  Terms|                                Definition|
 |-|-|
  |Scalability|                          The ability of a workload to change capacity limits dynamically.|
|  Autoscale|                            A feature of some Azure services to automate changes in capacity limits based on configurations.|
|  SKUs|                                 Specification tiers for Azure services.|
|  Capacity|                             The upper limit of a given service or service feature.|
|  Partitioning                         |Dividing a workload or data to manage it more efficiently.|
| Vertical scaling|                     Increasing the capacity of a resource to handle more demand.|
|  Horizontal scaling                   |Adding instances of the resource with the same capacity to handle more demand.|
| Scale unit|                           Resources that need to scale proportionately together.|
|  Client affinity|                      When requests from a single client are intentionally routed to a single server instance. It's also known as session affinity and instance pinning.|
|  Data locking                         |A way to prevent simultaneous updates to the same piece of data.|
| State affinity|                       When a single server stores client session data.|
|  Optimistic concurrency|               An approach for updating databases that uses snapshots to make updates instead of database locks.|
|  Consistency (distributed database)|   The uniformity of data in a distributed database|
| Consistency (relational database)|    The idea that a transaction should bring a database from one state to another.|
|  Consistency level|                    A configuration that describes how and when data is replicated.|

## Your responsibility

**Test scaling behavior.** Scaling might not work how you intend. You should test scaling to understand scaling delays, costs, and issues. You can test on a proof-of-concept. The final scaling strategy for a workload should use this data.

**Understand scaling use cases.** Vertical scaling relies on manually changing SKUs. There are a finite number of SKUs for each Azure service. There's a limit to how much you can scale up. Any scaling beyond the capacity limits must be horizontal. You can usually scale up or down without changing the application. Horizontal scaling usually gives you a much greater and more dynamic scaling capacity. You can scale out (add instances) and in (remove instances) automatically to meet demand or align to a fixed schedule. Horizontal scaling often requires changes to your application if it wasn't designed to support multiple concurrent instances originally. You need to pick the right scaling strategy for your workload.

### Application

**Define scale units.** A scale unit typically consists of multiple resources that support a flow or entire workload. You should document a scale unit design for components expected to be scaled together.  For example, 100 virtual machines might require 2 queues and 3 storage accounts to handle the extra workload. The scale unit would be 100 virtual machines, 2 queues, and 3 storage accounts.

**Design applications to scale.** There are techniques that make it easier to scale applications. You can use the [deployment stamps](/azure/architecture/patterns/deployment-stamp) pattern, so that it's easily scaled by adding one or more scale units.

**Align scaling methods with architecture.** You need to understand the architecture and design of your workload to choose the most effective scaling strategy. A workload can use vertical scaling, horizontal scaling, and partitioning. Microservices also use container orchestration. You need to configure scaling to accommodate your architecture and load.

**Identify dependencies**. You should conduct performance testing on scaling and partitioning implementations to find potential performance issues. Scaling or partitioning in one area of a workload might cause performance issues on a dependency. The stateful parts of a system, such as databases, are the most common cause of dependency performance issues. Databases require careful design to scale horizontally. You need to consider measures, such as [optimistic concurrency](/dotnet/framework/data/adonet/sql/linq/optimistic-concurrency-overview) or data partitioning, to enable more throughput to the database.

**Know upper scaling limits.** Every Azure resource has an upper scale limit. You should partition your workload if you need to go beyond scale limits.

**Know what components to scale independently**. You should independently scale all the components that experience capacity use fluctuation.

**Plan for partitioning.** If you address partitioning as an afterthought, it\'s more challenging because you already have a live system to maintain. You might need to change data access logic, distribute large quantities of data across partitions, and support continued usage during data distribution.

-   **Use functional partitioning:** Functional partitioning separates a workload into distinct functions. You should partition your workloads so you can scale specific parts of the application that experience higher demand. This selective scaling avoids the need to scale the entire application.

-   **Use data partitioning**: Data partitioning separates data across multiple data stores. Data partitioning allows the workload to distribute the load across multiple data stores. Partitioning allows you to manage and divert traffic away from high traffic flows. You can use categories like geographic location, tenant data, date, or organizational units to partition data.

-   **Use the right data partitioning strategies.** You need to use the right strategy to partition data in various Azure data stores. To learn more, see the following data storage links details.

    -   [Partitioning Azure SQL Databases](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-sql-database)

    -   [Partitioning Azure table storage](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-table-storage)

    -   [Partitioning Azure blob storage](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-blob-storage)

    -   [Partitioning Azure storage queues](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-storage-queues)

    -   [Partitioning Azure Service Bus](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-service-bus)

    -   [Partitioning Azure Cosmos DB](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-cosmos-db)

    -   [Partitioning Azure Search](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-search)

    -   [Partitioning Azure Cache for Redis](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-cache-for-redis)

    -   [Partitioning Azure Service Fabric](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-service-fabric)

    -   [Partitioning Azure Event Hubs](/azure/architecture/best-practices/data-partitioning-strategies#partitioning-azure-event-hubs)

### Infrastructure and platform

**Verify region capacity and available SKUs.** A region might offer a specific Azure service but not have the SKUs to support the predicted increase in load. You should use the Azure portal to check the availability of SKUs your desired region.

**Use services with autoscaling.** You should prefer platform-as-a-service (PaaS) offerings that have built-in scaling operations. Azure services that autoscale can scale automatically match demand to workload. For example, you can scale out infrastructure-as-a-service offerings, such as virtual machines, but you need to place them behind a load balancer. Each virtual machine needs to handle any client request. The application must either be stateless or store state externally. PaaS offerings handle most of these tasks. The ease of scaling on PaaS is a major advantage.

**Use meaningful load metrics.** Scaling should use meaningful load metrics as scaling triggers. Meaningful load metrics include simple metrics (CPU, memory) or more advanced metrics (queue depth, SQL queries, custom metrics queries, HTTP queue length). You should consider using a combination of simple and advanced load metrics as your scaling trigger.

**Scale to meet workload demand**. Components should scale fast enough to meet workload demand. Scaling shouldn't give you too much capacity. You need to understand resource scale units and set scaling thresholds at the correct levels to meet speed and capacity needs. The goal is to ensure the end user is not disrupted by the scaling.

**Constrain autoscaling by setting scaling limits.** Scaling limits prevent autoscaling too much capacity. You should set scaling limits to prevent over scaling. Sometimes you can't set scaling limits. In these cases, you should set alerts to notify you when the component has reached the maximum scale limit and when it's has over scaled.

**Know if services can scale down.** Some Azure services scale out or up but do not scale in or down. You need to research and test the scaling capabilities of each Azure service in your workload.

**Use a buffer.** A buffer is unused capacity to handle spikes in demand. A well-designed workload plans for unexpected spikes in workload. You should add a buffer to handle spikes for horizontal and vertical scaling.

**Understand autoscaling delays.** Scaling operations can be fast, but they take time to complete. You need to test scaling delays. The goal is to figure out the effects on the application under load and the level of acceptable performance.

**Control scale increment and decrement size and prevent flapping.** Autoscale lets you chose how much you want to increase or decrease capacity in horizontal scaling. Before you choose a scale size, you need to factor in the cool down period. A cooldown period is the amount of time a resource waits after a scale operation before scaling again. For example, if cooldown = "PT10M", autoscale doesn't attempt to scale again for another 10 minutes. The cooldown is to allow the metrics to stabilize after the addition or removal of instances, to prevent autoscale flapping. You want scaling operations to provide a considerable move away from scaling thresholds. You need to configure autoscale to provide tangible capacity changes.

### Distributing load

**Design workload to distribute load.** You must design a workload to distribute load. Just because you can add additional replicas at the infrastructure level doesn't mean your application is adapted to use the replicas. Where possible, avoid solutions that require client affinity, data locking, or state affinity for a single instance. You want to route a client or process to a resource that has capacity. This enables simplified load balancing & scale operations.

**Eliminate client affinity.** Avoiding reliance on client affinity in your application helps enable requests to be freely distributed across multiple server instances to improve performance.

**Review your load balancing algorithm.** A load balancing algorithm can cause unintentional and artificial client pinning to a backend instance. Potential causes include enable session affinity or tuple-based hashes without enough request variation.

**Eliminate data locking.** Data locking ensures high consistency but has performance disadvantages. It can cause lock escalations and negatively impact concurrency, latency, and availability. To eliminate data locking, you should implement [optimistic concurrency](/sql/connect/ado-net/optimistic-concurrency). Nonrelational databases should use [optimistic concurrency control](/azure/cosmos-db/nosql/database-transactions-optimistic-concurrency#optimistic-concurrency-control) and have the right [consistency level](/azure/cosmos-db/consistency-levels). Your data partitioning strategy should also support your concurrency needs. For Azure SQL, you can enable [optimized locking](/sql/relational-databases/performance/optimized-locking) to improve performance on databases that require strong consistency.

**Eliminate server-side session state.** You should design applications to be stateless where possible. For stateful applications, you should use a performant session state store external to your server. It allows you to store session state without state affinity. Externalizing state allows state stores to be sized and scaled independently from any compute.

**Avoid singletons.** You should design workflows to avoid singletons. Singletons are frequent causes of workload performance limitations. For example, singletons often appear in queues. The application requires message ordering, but the queue doesn't use portioning. You should design queue processing with partitions and to be agnostic about message ordering. Queues should be idempotent and have low to no coordination necessary. Singletons are also frequent data processing that doesn't fan out the processing. You should break long-running tasks into smaller tasks so that they scale better. You should also use architecture patterns such as fan-out/fan-in or Pipes and Filters to avoid singletons in workflows.

**Use dynamic service discovery.** Clients shouldn't be able to take a direct dependency on a specific instance in your solution. To avoid these dependencies, you should use a proxy to distribute and redistribute client connections. This approach also enables your service instances to support your scaling and partitioning strategy.

**Enable distributed tracing.** A workload is the union of all the instances, components, and partitions. Designing for distributing load also means that you can still trace transitions and gather performance metrics on workflows. You need to gather performance metrics on workflows in a distributed components and partitions. You should enable distributed tracing.

## Azure facilitation

-   Azure services have different performance tiers known as SKUs. SKUs allow you to scale vertically.

-   Azure has the infrastructure capacity to support horizontal scaling, vertical scaling, and partitioning.

-   Many of Azure\'s resources support autoscaling or other in-place scale options. Some support advanced metrics or custom input to support fine-tunning scaling behavior.

-   Many Azure storage and queue resources support for various partitioning strategies.

-   Most scaling implementations in Azure can set limits and support the necessary observability to be alerted to change.

-   Azure provides load balancers that don't require client affinity. These load balancers include [Azure Front Door](/azure/frontdoor/routing-methods#session-affinity) (global), [Azure Application Gateway](/azure/application-gateway/features#session-affinity) (regional), and [Azure Load Balancer](/azure/load-balancer/load-balancer-distribution-mode?tabs=azure-portal) (regional).

-   Several Azure compute services have an [integrated load balancer](/azure/architecture/guide/technology-choices/compute-decision-tree#scalability) that distributes load with minimal to no configuration required.

-   [Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview) is a database engine that can process relational data and nonrelational data. It has an optimized locking feature that improves performance if you need strong consistency.

-   [Azure Cosmos DB](/azure/cosmos-db/introduction) is a database that offers APIs for NoSQL, MongoDB, PostgreSQL, Cassandra, Gremlin, and Table. It has five different consistency levels to choose from.

-   [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview) is an in-memory data store that you can use store session state. You can use it alone or paired with Azure SQL and Azure Cosmos DB.

## Tradeoff

-   Scaling helps optimize costs by adjusting capacity to meet demand. It also increases cost during long periods of increased demand.

-   Scaling and partitioning require testing. They might require more time and effort during development and testing for workloads to reach production.

-   Partitioning data introduces complexities that could affect operations and security. These complexities include data access logic and on-going maintenance.

-   Increased latency (network hop to get state).

-   Increased complexity in replica management, idempotency implementations.

## Related links

-   [Why partition data?](/azure/architecture/best-practices/data-partitioning#why-partition-data)

-   [Best practices for autoscale](/azure/azure-monitor/platform/autoscale-best-practices#choose-the-thresholds-carefully-for-all-metric-types)

-   [Overview of autoscale in Azure](/azure/azure-monitor/platform/autoscale-overview)

-   [Horizontal, vertical, and functional data partitioning](/azure/architecture/best-practices/data-partitioning)

-   [Application design considerations](/azure/architecture/best-practices/data-partitioning#application-design-considerations)

