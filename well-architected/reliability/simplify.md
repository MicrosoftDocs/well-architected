---
title: Recommendations for designing for simplicity and efficiency
description: Learn how to minimize unnecessary complexity and overhead by keeping your workloads simple and efficient. 
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing for simplicity and efficiency

**Applies to: RE 10**

This guide describes the recommendations for minimizing unnecessary complexity and overhead by focusing on keeping your workloads simple and efficient. This focus will help you optimize the reliability of your workload by choosing the best components to perform the necessary workload tasks and taking advantage of efficiencies offered by platform-provided services to lessen your development and management burdens. This design methodology will help you deliver a workload architecture that is resilient, repeatable, scalable, and manageable.

**Definitions**

|Term  |Definition  |
|---------|---------|
|Workload     | A discrete capability or computing task that can logically be separated from other tasks        |

## Key design strategies

A key tenet of designing for reliability is to keep things simple and efficient. If you narrowly focus your workload design on meeting business requirements, you can reduce the risk of having unnecessary complexity or overhead to manage. The principles discussed in this guide can help you make decisions about your design that will help you meet the goal of a lean, efficient, and reliable workload. Workload in the context of this guide and the other guides in this series means a discrete capability or computing task that can logically be separated from other tasks. Different workloads might have different requirements for availability, scalability, data consistency, and disaster recovery.

Every design decision must be justified by a business requirement. This design principle might seem obvious, but is crucial to keep in mind when designing workloads. Must your application support millions of users, or a few thousand? Are there large traffic bursts, or a steady workload? What level of application outage is acceptable? Ultimately, business requirements drive these design considerations.

Work with stakeholders to:

-   Define and assign a measure of criticality to the user and system flows for your workload. Approaching your design with a focus on critical flows will help you determine the components required and the approach to achieving the required level of resilience. Refer to the Recommendations for identifying and rating flows (link to re01-identifying-flows) guide for further guidance on this topic.

-   Define functional and nonfunctional requirements. Functional requirements determine whether an application performs its task. Nonfunctional requirements determine how well the application performs. Make sure you understand nonfunctional requirements like scalability, availability, and latency. These requirements influence design decisions and technology choices.

-   Decompose workloads into components. As you begin to plan your design with a focus on simplicity, efficiency, and reliability, determine what types of components you will need to have in place to support your flows. Some components will support multiple flows and others may not but strive to identify which challenge the component conceptually addresses, and consider whether removing a component from individual flows might simplify the overall design while still allowing full functionality. Refer to the Recommendations for Failure Mode Analysis (link to re02-failure-mode-analysis) guide for further guidance on this topic.

-   Use failure mode analysis to identify single points of failure and potential risks. Consider whether you need to account for less likely situations, like a geographic area experiencing a major natural disaster that might affect all of the availability zones in the region. Mitigating these uncommon risks is generally more expensive and involves significant tradeoffs, so have a clear understanding of the business\'s tolerance for risk. Refer to the Recommendations for Failure Mode Analysis (link to re02-failure-mode-analysis) guide for further guidance on this topic.

-   Define availability and recovery targets for the workload in the context of your flows to inform your architecture. Business metrics include those focusing on availability like service level objectives (SLOs) and service level agreements (SLAs) and those focusing on recovery like mean time to recover (MTTR), mean time between failure (MTBF), recovery time objectives (RTOs) and recovery point objectives (RPOs). Defining target values for these metrics is an exercise in compromise and mutual understanding between technology and business teams to ensure that they both meet business objectives and are realistic. Refer to the Define reliability targets (link to re03-business-metrics) guide for further guidance on this topic.

The following recommendations can be performed outside of the scope of stakeholder engagement:

-   Strive for simplicity and clarity in your design. Use the appropriate level of abstraction and granularity for your components and services. Avoid over-engineering or under-engineering your solution. For example: breaking down your code into too many small functions can make it harder to understand, test, and maintain.

-   All successful applications change over time, whether to fix bugs, add new features, bring in new technologies, or make existing systems more scalable and resilient.

-   Use platform as a service (PaaS) options. When it\'s possible, use PaaS instead of infrastructure-as-a-service (IaaS). IaaS is like having a box of parts. You can build anything, but you have to assemble it yourself. PaaS options are easier to configure and administer. You don\'t need to set up virtual machines (VMs) or virtual networks. You also don\'t have to handle maintenance tasks, such as installing patches and updates.

-   Use asynchronous messaging. Asynchronous messaging is a way to decouple the message producer from the consumer.

-   Abstract infrastructure away from domain logic. Don\'t let domain logic get mixed up with infrastructure-related functionality, such as messaging or persistence.

-   Offload cross-cutting concerns to a separate service. For example, if several services need to authenticate requests, you could move this functionality into its own service. Then you could evolve the authentication service --- for example, by adding a new authentication flow --- without touching any of the services that use it.

-   Evaluate the suitability of common patterns and practices for your specific needs. Avoid blindly following trends or recommendations that may not be the best fit for your context or requirements. For example: microservices are not always the best option for every application, as they can introduce complexity, overhead, and dependency issues.

### Develop just enough code

The principles of simplicity, efficiency and reliability do not only apply to your workload components. Apply these principles to your development practices as well. In a loosely coupled, componentized workload, you can focus on only the functionality provided by a given component and develop to best take advantage of that functionality in the context of your flows. Use these recommendations to help you in your development practices:

-   Use platform capabilities when they meet your business requirements. For example, you can use low-code, no-code, and serverless solutions offered by your cloud provider to offload development and management.

-   Consider using libraries and frameworks if possible.

-   Consider introducing pair programming or dedicated code review sessions as part of your development practices.

-   Consider introducing an approach to identify "dead code" (be skeptical about code that is not covered by your automated tests).

## Use the best data store for your data

In the past, many organizations stored all their data in large relational SQL databases. Relational databases are good at providing atomic, consistent, isolated, and durable (ACID) guarantees for transactions that involve relational data. But these databases come with costs:

-   Queries can require expensive joins.

-   You need to normalize the data and restructure it for schema on write.

-   Lock contention can affect performance.

### Alternatives to relational databases

In a large solution, a single data store technology probably doesn\'t meet all your needs. Alternatives to relational databases include:

-   Key/value stores

-   Document databases

-   Search engine databases

-   Time series databases

-   Column family databases

-   Graph databases

Each has pros and cons, and different types of data fit more naturally into different data store types. Pick the storage technology that\'s the best fit for your data and how you use it.

For example, you might store a product catalog in a document database, such as Azure Cosmos DB, which supports a flexible schema. In that case, each product description is a self-contained document. For queries over the entire catalog, you might index the catalog and store the index in Azure Cognitive Search. Product inventory might go into a SQL database, because that data requires ACID guarantees.

### Recommendations

-   Don\'t use a relational database for everything. Consider other data stores when it\'s appropriate. For information on common storage models, see [Understand data store models](https://learn.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview).

-   Remember that data includes more than just persisted application data. It also includes application logs, events, messages, and caches.

-   Embrace polyglot persistence, or solutions that use a mix of data store technologies.

-   Consider the type of data that you have. For example:

    -   Put transactional data into a SQL database.

    -   Store JSON documents in a document database.

    -   Use a time series database for telemetry.

    -   Put application logs into Azure Cognitive Search.

    -   Choose Azure Blob Storage for blobs.

-   Prefer availability over (strong) consistency. The CAP theorem implies that distributed systems have to make trade-offs between availability and consistency. You can never completely avoid network partitions, the other leg of the CAP theorem. But you can often achieve higher availability by adopting an eventual consistency model.

-   Consider the skill set of your development team. There are advantages to using polyglot persistence, but it\'s possible to go overboard. Adopting a new data storage technology requires a new set of skills. To get the most out of the technology, the development team needs to understand how to:

    -   Optimize queries.

    -   Tune for performance.

    -   Work with appropriate usage patterns.

Consider these factors when you choose a storage technology.

-   Use compensating transactions. A side effect of polyglot persistence is that a single transaction might write data to multiple stores. If something fails, use compensating transactions to undo any steps that have already finished.

-   Look at bounded contexts, a concept from domain-driven design. A bounded context is an explicit boundary around a domain model. A bounded context defines which parts of the domain the model applies to. Ideally, a bounded context maps to a subdomain of the business domain. The bounded contexts in your system are a natural place to consider polyglot persistence. For example, products might appear in the Product Catalog subdomain and the Product Inventory subdomain. But most likely, these two subdomains have different requirements for storing, updating, and querying products.

## Azure facilitation

Azure offers low-code, no-code and serverless services including:

[Azure Functions](https://learn.microsoft.com/azure/azure-functions/functions-overview?pivots=programming-language-csharp) is a serverless compute service that allows you to build orchestration through developing minimal code.

[Azure Logic Apps](https://learn.microsoft.com/azure/logic-apps/logic-apps-overview) is a serverless workflow integration platform that allows you to build orchestration through a GUI or by editing a configuration file.

[Azure Event Grid](https://learn.microsoft.com/azure/event-grid/overview) is a highly scalable, fully managed Pub Sub message distribution service that offers flexible message consumption patterns using the MQTT and HTTP protocols. With Azure Event Grid, you can build data pipelines with device data, integrate applications, and build event-driven serverless architectures.

Refer to the [Choose an Azure compute service - Azure Architecture Center](https://learn.microsoft.com/azure/architecture/guide/technology-choices/compute-decision-tree) [Choose a compute option for microservices - Azure Architecture Center](https://learn.microsoft.com/azure/architecture/microservices/design/compute-options) guides to learn about these and other Azure compute and serverless services to help determine which technologies might be the best fit for your workload design.

Azure offers many relational and non-relational database services. Refer to the [Review your data options](https://learn.microsoft.com/azure/architecture/guide/technology-choices/data-options) guide to learn more about all of the database services in Azure and how they compare to each other.

## Tradeoff

Complex solutions may offer more features and flexibility, but they may have a potential impact on reliability because they require more coordination, communication, and management of different components. On the other hand, a simpler solution may not fully meet user expectations or may have a negative impact on scalability and extensibility as the workload evolves over time.

Refer to the other guides in this series for tradeoffs related to the points discussed in this guide.

Link to each guide -- Tradeoff anchor

## Example

Refer to the Reliable Web Pattern [reference architecture](https://learn.microsoft.com/azure/architecture/web-apps/guides/reliable-web-app/dotnet/plan-implementation) for an example of a workload that uses requirements to base decisions about components on.

## Related links

[Cloud-Native Applications](https://azure.microsoft.com/solutions/cloud-native-apps)

[Azure Serverless](https://azure.microsoft.com/solutions/serverless/)

[Azure Databases - Types of Databases on Azure](https://azure.microsoft.com/products/category/databases/)
