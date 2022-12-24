---
title: Application design of mission-critical workloads on Azure
description: This design area explores requisite application design patterns for building a highly reliable application on Azure.
author: calcof
ms.author: prwilk
ms.date: 12/15/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories: networking
products: azure
ms.custom:
  - mission-critical
---

# Application design of mission-critical workloads on Azure

Both functional application requirements and non-functional requirements are critical when designing your application. This design area describes architecture patterns and scaling strategies that will make your application resilient to failures.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)

## Scale-unit architecture

All functional aspects of the solution must be capable of scaling to meet changes in demand.
Using a scale-unit architecture  is recommended to optimize end-to-end scalability through compartmentalization and also to standardize the process of adding and removing capacity. A _scale-unit_ is a logical unit or function that can be scaled independently. A unit can be code components, application hosting platforms, the [deployment stamps](/azure/architecture/patterns/deployment-stamp) that cover the related components, and even subscriptions to support multi-tenant requirements.

This approach is recommended because it addresses the scale limits of individual resources and the entire application. It helps with complex deployment and update scenarios because a scale-unit can be deployed as one unit. Also, you can test and validate specific versions of components in a unit before directing user traffic to it.

Suppose your mission-critical application is an online product catalog. It has a user flow for processing product comments and ratings. The flow uses APIs for retrieving and posting comments and ratings, and supporting components such as an OAuth endpoint, datastore, and message queues. The stateless API endpoints represent granular functional units that must adapt to changes on demand. Also, the underlying application platform must also be able to scale accordingly. To avoid performance bottlenecks, the downstream components and dependencies must also scale to an appropriate degree. They can either scale independently, as a separate scale-unit, or together, as part of a single logical unit.

### Example scale units

This image shows the possible scopes for scale units. The scopes range from microservice pods to cluster nodes, and regional deployment stamps.

:::image type="content" source="./images/mission-critical-scale-units.png" alt-text="Diagram showing multiple scopes illustrated in the reference implementation." lightbox="./images/mission-critical-scale-units.png ":::

### Design considerations

- **Scope**. The scope of a scale unit, relationship between scale-units, and their components. They should be defined according to a capacity model, taking into consideration non-functional requirements for performance.

- **Scale limits**. [Azure subscription scale limits and quotas](/azure/azure-resource-manager/management/azure-subscription-service-limits) might have a bearing on application design, technology choices, and the definition of scale-units. Scale units can help to overcome scale limits of a service. For example, if an AKS cluster in one unit can have only 1000 nodes, two units can increase that limit to 2000 nodes.

- **Expected load**. Number of requests for each user flow, the expected peak request rate (requests per second), and daily/weekly/seasonal traffic patterns are critical to inform core scale requirements. Also factor in the expected growth patterns for both traffic and data volume.

- **Acceptable degraded performance**. Is a degraded service with high response times acceptable under load? The required performance of the solution under load is a critical decision factor when modeling required capacity.

- **Non-functional requirements**. Technical and business scenarios have distinct considerations for resilience, availability, latency, capacity, and observability. Analyze these requirements in the context of key end-to-end user-flows. You’ll have relative flexibility in the design, tailoring decisions, and technology choices at a user-flow level.

### Design recommendations

- Define the scope of a scale-unit and the limits beyond within which the unit will scale.

- Ensure all application components can scale independently or as part of a scale-unit that has other related components.

- Define the relationship between scale-units, according to a capacity model and non-functional requirements.

- Define a regional deployment stamp to unify the provisioning, management, and operation of regional application resources, into a heterogenous but inter-dependent scale-unit. As the load increases, extra stamps  can be deployed within the same or different Azure regions, in order to horizontally scale the solution.

- Use Azure subscription as the scale unit so that scale limits within a single subscription don't constrain the scalability. This approach is applicable for high-scale application scenarios with significant traffic volume.

- Model the required capacity around identified traffic patterns to make sure sufficient capacity is provisioned at peak times to prevent service degradation. Alternatively, optimize capacity during off-peak hours.

- Measure the time taken to do scale out and scale in operations to ensure that the natural variations in traffic don't create an unacceptable level of service degradation. Track the scale operation durations as an operational metric.

> [!NOTE]
> When deploying within an Azure landing zone, ensure the landing zone subscription is dedicated to the application, in order to provide a clear management boundary and to avoid potential the [Noisy Neighbor antipattern](/azure/architecture/antipatterns/noisy-neighbor).

## Global distribution

Failure is impossible to avoid in any highly distributed environment. Here are some strategies to mitigate many fault scenarios. The application must be able to withstand regional and zonal failures. It must be deployed in an active-active model so that the load is distributed among all regions.

> [!VIDEO 7cea20d8-8265-4c5c-aaba-5e174731c2e3]  

### Design considerations

- **Redundancy**. The application must be deployed to multiple regions. Additionally, within a region, using [Availability Zones](/azure/availability-zones/az-overview#availability-zones) (AZ) is highly recommended to allow for fault tolerance at the datacenter level. Availability Zones have a latency perimeter of less than 2 milliseconds between availability zones. For workloads that are 'chatty' across zones, this latency can introduce a performance penalty and incur bandwidth charges for inter-zone data transfer.

- **Active-active model**. An active-active deployment strategy represents the gold standard because it maximizes availability and allows for higher composite Service Level Agreement (SLA). However, it can introduce challenges around data synchronization and consistency for many application scenarios. Address the challenges at a data platform level while considering trade-offs from increased cost and engineering effort.

  An active-active deployment across other cloud providers might be a potential solution to mitigate dependency on global resources within a single cloud provider. However, a multicloud active-active deployment strategy introduces a significant amount of complexity around CI/CD. Also, given the differences in resource specifications and capabilities between cloud providers, you'll need specialized deployment stamps for each cloud.

- **Geographical distribution**. The workload might have compliance requirements around geographical data residency, data protection, and data retention. **Are there specific regions where data must reside or where resources have need to be deployed?**

- **Request origin.** The geographic proximity and density of users or dependent systems should inform design decisions around the global distribution.

- **Connectivity**. How the workload is accessed by users or external systems will influence your design. Consider whether the application is available over the public internet or private networks using either VPN or ExpressRoute circuits.


For design recommendations and configuration choices at the platform level, see [Application platform: Global distribution](mission-critical-application-platform.md#global-distribution-of-platform-resources).

##### Example - Global distribution approach

This example shows both global and regional resources, with regional resources deployed across multiple regions to provide geo-availability and to bring services closer to end-users. These regional deployments also serve as scale-units stamps to provide extra capacity and availability when required.

![Mission-Critical Global Distribution](./images/mission-critical-global-distribution.gif "Mission-Critical Global Distribution")

## Loosely coupled event-driven architecture

The _loose_ aspect allows an application component to operate independently while _coupling_ allows for inter-service communication through well-defined interfaces. Using [microservices as your architecture style](/azure/architecture/guide/architecture-styles/microservices) resonates with mission-critical requirements. It facilitates high availability by preventing cascading failures.

For loose coupling, incorporating [event-driven design](/azure/architecture/guide/architecture-styles/event-driven) is highly recommended. Asynchronous message processing through an intermediary can build resiliency.

:::image type="content" source="./images/mission-critical-asynchronous-communication.png " alt-text="Diagram showing asynchronous event-driven communication." lightbox="./images/mission-critical-asynchronous-communication.png ":::

In some scenarios, applications can combine loose and tight-coupling, depending on business objectives.

### Design considerations

- **Runtime dependencies**. Loosely coupled services shouldn’t be constrained to use the same compute platform, programming language, runtime, or operating system.

- **Scaling**. Services should be able to scale independently by optimizing the use of infrastructure and platform resources.  

- **Fault tolerance**. Failures should be handled separately and shouldn’t affect client transactions.

- **Transactional integrity**. Consider the impact of data creation and persistence that happens in separate services.

- **Distributed tracing**. End-to-end tracing may require complex orchestration.

### Design recommendations

- Align microservice boundaries with critical user-flows.

- Use event-driven asynchronous communication where possible to support sustainable scale and optimal performance.

- Use patterns like the Outbox and Transactional Session to guarantee consistency so that [every message is processed correctly](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-data-platform#every-message-must-be-processed).

##### Example - Event-driven approach

The [Mission-Critical Online](https://github.com/Azure/Mission-Critical-online) reference implementation uses microservices to process a single business transaction. It applies write operations asynchronously with a message broker and worker, while read operations are synchronous with the result directly returned to the caller.

:::image type="content" source="./images/mission-critical-event-driven.png " alt-text="Diagram showing event-driven communication." lightbox="./images/mission-critical-event-driven.png":::

## Resiliency patterns and error handling in application code

A mission-critical application must be designed to be resilient to address as many failure scenarios as possible in order to maximize service availability and reliability. It should have self-healing capabilities by using appropriate design patterns. Such as [retries with back-off](/dotnet/architecture/microservices/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly) and [circuit breaker](/dotnet/architecture/microservices/implement-resilient-applications/implement-circuit-breaker-pattern).

For non-transient failures that can't be fully mitigated within application logic, it becomes the role of the health model and operational wrappers to take corrective action. So, application code must incorporate proper instrumentation and logging to inform the health model and facilitate subsequent troubleshooting or root cause analysis when required. [Distributed tracing](/dotnet/core/diagnostics/distributed-tracing-concepts) must be implemented to provide the caller with a comprehensive error message that includes a correlation ID when a failure occurs.

Tools like [Azure Application Insights](/azure/azure-monitor/app/distributed-tracing) can help significantly to query, correlate, and visualize application traces.

### Design considerations


- **Proper configurations**. It's not uncommon for transient issues to cause cascading failures.  For example, retry without appropriate back-off will exacerbate the issue when a service is being throttled. Retry delays can be linearly spaced or increased exponentially to backoff through growing delays.

- **Health endpoint**. Exposing functional checks within application code through health endpoints can poll to retrieve application component health status.

### Design recommendations

Here are some [common software engineering patterns](/azure/architecture/patterns/) for resilient applications:

|Pattern|Summary|
|---|---|
|[Queue-Based Load Leveling](/azure/architecture/patterns/queue-based-load-leveling)| Introduces a buffer between consumers and requested resources to ensure consistent load levels. As consumer requests are queued, a worker process handles them against the requested resource at a pace set by the worker, and the requested resource's ability to process the requests. If consumers expect replies to their requests, a separate response mechanism will also need to be implemented. Apply a prioritized ordering so that the most important activities are performed first.|
|[Circuit Breaker](/azure/architecture/patterns/circuit-breaker)| Provides stability by either waiting for recovery, or quickly rejecting requests rather than blocking while waiting for an unavailable remote service or resource. Also, handles faults that might take a variable amount of time to recover from when connecting to a remote service or resource.|
|[Bulkhead](/azure/architecture/patterns/bulkhead)|Strives to partition service instances into groups based on load and availability requirements, isolating failures to sustain service functionality.|
|[Saga](/azure/architecture/reference-architectures/saga/saga)| Manage data consistency across microservices with independent datastores by ensuring services update each other through defined event or message channels. Each service performs local transactions to update its own state and publishes an event to trigger the next local transaction in the saga. If a service update fails, the saga executes compensating transactions to counteract preceding service update steps. Individual service update steps can themselves implement resiliency patterns, such as retry.|
|[Health Endpoint Monitoring](/azure/architecture/patterns/health-endpoint-monitoring)|Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals. Interpret responses from the endpoint with key operational metrics to inform application health and trigger operational responses, such as raising an alert or performing a compensating roll-back deployment.|
|[Retry](/azure/architecture/patterns/retry)|Handles transient failures elegantly and transparently.</br> - Cancel if the fault is unlikely to be transient and is unlikely to succeed if the operation is reattempted. </br> - Retry if the fault is unusual or rare and the operation is likely to succeed if attempted again immediately. </br>- Retry after a delay if the fault is caused by a condition that may need a short time to recover, such as network connectivity or high load failures. Apply a suitable 'backoff' strategy with growing retry delays.|
|[Throttling](/azure/architecture/patterns/throttling)| Controls the consumption of resources used by application components, protecting them from becoming over encumbered. When a resource reaches a load threshold, it should defer lower-priority operations, and degrading non-essential functionality so that essential functionality can continue until sufficient resources are available to return to normal operation.|

- Use vendor provided SDKs, such as the Azure SDKs, to connect to dependent services. Also use built-in  resiliency capabilities instead of implementing custom functionality.

 - Ensure a suitable back-off strategy is applied when retrying failed dependency calls to avoid a self-inflicted DDoS scenario.

- Define **common engineering criteria** for all application microservice teams to drive consistency and acceleration regarding the use application-level resiliency patterns.

- Implement resiliency patterns using proven standardized packages, such as [ [Polly](http://www.thepollyproject.org/) for C# or [Sentinel](https://github.com/alibaba/Sentinel) for Java.

- Use correlation IDs for all trace events and log messages to tie them to a given request. Return correlation IDs to the caller for all calls not just failed requests.
- Use structured logging for all log messages. Select a unified operational data sink for application traces, metrics, and logs to enable operators to seamlessly debug issues. For more information, see [Collect, aggregate, and store monitoring data for cloud applications]( /azure/architecture/framework/devops/monitor-collection-data-storage).

- Ensure operational data is used with business requirements to inform an [application health model](./mission-critical-health-modeling.md).

## Programming language selection

Selecting the right programming languages and frameworks is a critical design decision. This decision is often driven by the skill set or standardized technologies within an organization. However, it's essential to evaluate the performance, resilience aspects, and overall capability of different languages and frameworks.

### Design considerations

- **Development kit capability**. There are differences in the capabilities offered by Azure service SDKs in different languages, which may influence the selection of an Azure service or programming language.  For instance, if Azure Cosmos DB is feasible option, 'Go' may not be an appropriate development language because there's no first-party SDK.

- **Feature updates**. Consider how often the SDK is updated with new features for the selected language. Commonly used SDKs, such as .NET and Java libraries, are updated often while other SDKs or SDKs for other languages might receive updates less frequently.

- **Multiple programming languages or frameworks**. Multiple options can be used to support various composite workloads. However, significant sprawl should be avoided because it introduces management complexity and operational challenges.

- **Compute option**. Legacy or proprietary software might not run in PaaS services and/or can't be included in containers.  

### Design recommendations

- Evaluate all relevant Azure SDKs for the needed capabilities, the selected programming languages, and ensure alignment with non-functional requirements.

- Optimize the selection of programming languages and frameworks at the microservice level; embrace multiple technologies where appropriate.

- Prioritize the .NET SDK to optimize reliability and performance because .NET Azure SDKs typically provide more capabilities and are updated often.

## Next step

Review the considerations for the application platform.

> [!div class="nextstepaction"]
> [Application platform](./mission-critical-application-platform.md)
