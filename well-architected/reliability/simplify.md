---
title: Recommendations for designing for simplicity and efficiency
description: Learn how to minimize unnecessary complexity and overhead by keeping your workloads simple and efficient. 
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing for simplicity and efficiency

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|[RE:10](checklist.md)| Design your workload to align with business objectives and avoid unnecessary complexity or overhead. Use a practical and balanced approach to make design decisions that deliver the desired results. Contain your design to the necessities to reduce inefficiencies and potential problems. |
|---|---|

This guide describes the recommendations for minimizing unnecessary complexity and overhead to keep your workloads simple and efficient. Choose the best components to perform the necessary workload tasks to optimize the reliability of your workload. To lessen your development and management burdens, take advantage of efficiencies that platform-provided services offer. This design helps you create a workload architecture that's resilient, repeatable, scalable, and manageable.

**Definitions**

|Term  |Definition  |
|---------|---------|
|Workload | A discrete capability or computing task that you can logically separate from other tasks. |

## Key design strategies

A key tenet of designing for reliability is to keep things simple and efficient. Focus your workload design on meeting business requirements to reduce the risk of unnecessary complexity or excess overhead. Consider the recommendations in this article to help you make decisions about your design to create a lean, efficient, and reliable workload. Different workloads might have different requirements for availability, scalability, data consistency, and disaster recovery.

You must justify every design decision with a business requirement. This design principle might seem obvious, but it's crucial for workload design. Does your application support millions of users, or a few thousand? Are there large traffic bursts, or a steady workload? What level of application outage is acceptable? Business requirements drive these design considerations.

Work with stakeholders to:

- **Define and assign a criticality level to your workload's user flows and system flows**. Focus your design on [critical flows](identify-flows.md) to help you determine the required components and the best approach to achieve the required resiliency level.

- **Define functional and nonfunctional requirements**. Consider functional requirements to determine whether an application performs a task. Consider nonfunctional requirements to determine how well the application performs a task. Ensure that you understand nonfunctional requirements like scalability, availability, and latency. These requirements influence design decisions and technology choices.

- **Decompose workloads into components**. Prioritize simplicity, efficiency, and reliability in your design. Determine the components that you need to support your flows. Some components support multiple flows. Identify which challenge a component conceptually addresses, and consider removing a component from individual flows to simplify the overall design while still providing full functionality. For more information, see [Recommendations for performing failure mode analysis](failure-mode-analysis.md).

- **Use failure mode analysis** to identify single points of failure and potential risks. Consider whether you need to account for unlikely situations, for example a geographic area that experiences a major natural disaster that affects all the availability zones in the region. It's expensive and involves significant tradeoffs to mitigate these uncommon risks. Clearly understand your business's tolerance for risk. For more information, see [Recommendations for performing failure mode analysis](failure-mode-analysis.md).

- **Define availability and recovery targets** for your flows to inform your workload's architecture. Business metrics include service-level objectives (SLOs), service-level agreements (SLAs), mean time to recover (MTTR), mean time between failure (MTBF), recovery time objectives (RTOs), and recovery point objectives (RPOs). Define target values for these metrics. This exercise might require compromise and mutual understanding between technology and business teams to ensure that each team's goals meet business objectives and are realistic. For more information, see [Recommendations for defining reliability targets](metrics.md).

You can perform the following recommendations without stakeholder engagement:

- **Strive for simplicity and clarity** in your design. Use the appropriate level of abstraction and granularity for your components and services. Avoid overengineering or under-engineering your solution. For example, if you break down your code into multiple small functions, it's hard to understand, test, and maintain.

- **Concede that all successful applications change over time**, whether to fix bugs, implement new features or technologies, or make existing systems more scalable and resilient.

- **Use platform as a service (PaaS) options** instead of infrastructure as a service (IaaS) when possible. IaaS is like having a box of parts. You can build anything, but you have to assemble it yourself. PaaS options are easier to configure and administer. You don't need to set up virtual machines (VMs) or virtual networks. You also don't have to perform maintenance tasks, such as installing patches and updates.

- **Use asynchronous messaging** to decouple the message producer from the consumer.

- **Abstract infrastructure away from domain logic**. Ensure that domain logic doesn't interfere with infrastructure-related functionality, such as messaging or persistence.

- **Offload cross-cutting concerns to a separate service**. For example, if several services need to authenticate requests, you can move this functionality into its own service. Then you can evolve the authentication service. For example, you can add a new authentication flow without touching any of the services that use it.

- **Evaluate the suitability of common patterns and practices** for your needs. Avoid following trends or recommendations that might not be best for your context or requirements. For example, microservices aren't the best option for every application because they can introduce complexity, overhead, and dependency issues.

### Develop just enough code

The principles of simplicity, efficiency, and reliability also apply to your development practices. In a loosely coupled, componentized workload, determine the functionality that a component provides. Develop your flows to take advantage of that functionality. Consider these recommendations for your development practices:

- Use platform capabilities when they meet your business requirements. For example, to offload development and management, use low-code, no-code, or serverless solutions that your cloud provider offers.

- Use libraries and frameworks.

- Introduce pair programming or dedicated code review sessions as a development practice.

- Implement an approach to identify *dead code*. Be skeptical of the code that your automated tests don't cover.

### Use the best data store for your data

In the past, many organizations stored all their data in large relational SQL databases. Relational databases provide atomic, consistent, isolated, and durable (ACID) guarantees for relational data transactions. But these databases come with disadvantages:

- Queries can require expensive joins.

- You need to normalize the data and restructure it for schema on write.

- Lock contention can affect performance.

#### Alternatives to relational databases

In a large solution, a single data store technology likely doesn't meet all your needs. Alternatives to relational databases include:

- Key-value stores

- Document databases

- Search engine databases

- Time series databases

- Column family databases

- Graph databases

Each option has pros and cons. Different data types are better suited for different data store types. Pick the storage technology that's the best fit for your data and how you use it.

For example, you might store a product catalog in a document database, such as Azure Cosmos DB, which supports a flexible schema. Each product description is a self-contained document. For queries over the entire catalog, you might index the catalog and store the index in Azure Cognitive Search. Product inventory might go into a SQL database because that data requires ACID guarantees.

#### Recommendations

- Consider other data stores. Relational databases aren't always appropriate. For more information, see [Understand data store models](/azure/architecture/guide/technology-choices/data-store-overview).

- Remember that data includes more than just persisted application data. It also includes application logs, events, messages, and caches.

- Embrace polyglot persistence or solutions that use a combination of data store technologies.

- Consider the type of data that you have. For example, store:

  - Transactional data in a SQL database.

  - JSON documents in a document database.

  - Telemetry in a time series database.

  - Application logs in Azure Cognitive Search.

  - Blobs in Azure Blob Storage.

- Prioritize availability over consistency. The [CAP theorem](/azure/well-architected/carrier-grade/carrier-grade-design-area-data-model#cap-theorem) implies that you have to make tradeoffs between availability and consistency in a distributed system. You can't completely avoid network partitions, which is the other component of the CAP theorem. But you can adopt an eventual consistency model to achieve higher availability.

- Consider the skill set of your development team. There are advantages to using polyglot persistence, but it's possible to go overboard. It requires new skill sets to adopt a new data storage technology. To get the most out of the technology, your development team must:

  - Optimize queries.

  - Tune for performance.

  - Work with the appropriate usage patterns.

Consider these factors when you choose a storage technology:

- Use compensating transactions. With polyglot persistence, a single transaction might write data to multiple stores. If there's a failure, use compensating transactions to undo any steps that have finished.

- Consider bounded contexts, which is a domain-driven design concept. A bounded context is an explicit boundary around a domain model. A bounded context defines which parts of the domain that the model applies to. Ideally, a bounded context maps to a subdomain of the business domain. Consider polyglot persistence for bounded contexts in your system. For example, products might appear in the product catalog subdomain and the product inventory subdomain. But most likely, these two subdomains have different requirements for storing, updating, and querying products.

## Azure facilitation

Azure offers the following services:

- [Azure Functions](https://azure.microsoft.com/products/functions) is a serverless compute service that you can use to build orchestration with minimal code.

- [Azure Logic Apps](https://azure.microsoft.com/products/logic-apps) is a serverless workflow integration platform that you can use to build orchestration with a GUI or by editing a configuration file.

- [Azure Event Grid](https://azure.microsoft.com/products/event-grid) is a highly scalable, fully managed publish-subscribe message distribution service that offers flexible message consumption patterns that use the MQTT and HTTP protocols. With Event Grid, you can build data pipelines with device data, build event-driven serverless architectures, and integrate applications.

For more information, see:

- [Choose an Azure compute service](/azure/architecture/guide/technology-choices/compute-decision-tree)
- [Choose a compute option for microservices](/azure/architecture/microservices/design/compute-options)
- [Review your data options](/azure/architecture/guide/technology-choices/data-options)

## Tradeoffs

A complex solution can offer more features and flexibility, but it might affect the reliability of the workload because it requires more coordination, communication, and management of components.

Alternatively, a simpler solution might not fully meet user expectations, or it might have a negative effect on scalability and extensibility as the workload evolves.

Refer to the other guides in this series for tradeoffs related to the points in this guide:

- [Background jobs](background-jobs.md#tradeoffs)
- [Data partitioning](partition-data.md#tradeoffs)
- [Define reliability targets](metrics.md#tradeoffs)
- [Disaster recovery](disaster-recovery.md#tradeoffs)
- [Failure mode analysis](failure-mode-analysis.md#tradeoff)
- [Identify and rate flows](identify-flows.md#tradeoffs)
- [Monitoring and alerting](monitoring-alerting-strategy.md#tradeoffs)
- [Redundancy](redundancy.md#tradeoffs)
- [Scaling strategy](scaling.md#tradeoffs)
- [Self-healing and self-preservation](self-preservation.md#tradeoffs)
- [Testing strategy](testing-strategy.md#tradeoffs)
- [Transient faults](handle-transient-faults.md#tradeoffs)

## Example

For an example workload that determines components and their features based on requirements, see [Reliable Web App pattern](/azure/architecture/web-apps/guides/reliable-web-app/dotnet/plan-implementation).

## Related links

- [Azure serverless](https://azure.microsoft.com/solutions/serverless)
- [Cloud-native applications](https://azure.microsoft.com/solutions/cloud-native-apps)
- [Types of databases on Azure](https://azure.microsoft.com/products/category/databases)

## Reliability checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Reliability checklist](checklist.md) 
