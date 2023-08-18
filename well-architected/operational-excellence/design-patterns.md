---
title: Cloud design patterns that support operational excellence
description: Learn about industry patterns that support operational excellence and can help you address common challenges in cloud workloads.  
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Cloud design patterns that support operational excellence

When you design workload architectures, you should use industry patterns that address common challenges. Patterns can help you make intentional tradeoffs within workloads and optimize for your desired outcome. These patterns are backed by real-world experience, are designed for cloud scale and operating models, and are inherently vendor agnostic. Using well-known patterns as a way to standardize your workload design is itself a component of operational excellence.

Many design patterns directly support one or more architecture pillars. Design patterns that support the Operational Excellence pillar use topologies that provide a solid foundation for safe deployment practices and facilitate architecture evolution over time, migration scenarios, and observability.

## Design patterns for operational excellence

The following table summarizes cloud design patterns that support the goals of operational excellence.

|Pattern|Summary|
|-|-|
|[Anti-Corruption Layer](/azure/architecture/patterns/anti-corruption-layer)|Protects new system components from the behavior or implementation choices of legacy systems by adding a mediator layer to proxy interactions between legacy and new components. This pattern helps ensure that new component design remains uninfluenced by legacy implementations that might have different data models or business rules when you integrate with these legacy systems. The pattern is especially useful in gradual system migrations. It reduces technical debt in new components while still supporting existing components.|
|[Choreography](/azure/architecture/patterns/choreography)|Coordinates the behavior of autonomous distributed components in a workload by using decentralized, event-driven communication. This pattern can be useful when you expect to update or replace services frequently during the lifecycle of a workload. Because the distributed components are autonomous, you can modify the workload with less overall change to the system.|
|[Compute Resource Consolidation](/azure/architecture/patterns/compute-resource-consolidation)|Optimizes and consolidates compute resources by increasing density. This pattern combines multiple applications or components of a workload on a shared infrastructure. The consolidation leads to a more homogenous compute platform, which can simplify management and observability, reduce disparate approaches to operational tasks, and reduce the amount of tooling that's required.|
|[Deployment Stamps](/azure/architecture/patterns/deployment-stamp)|Provides an approach for releasing a specific version of an application and its infrastructure as a controlled unit of deployment, based on the assumption that the same or different versions will be deployed concurrently. This pattern aligns with immutable infrastructure goals, supports advanced deployment models, and can facilitate safe deployment practices.|
|[External Configuration Store](/azure/architecture/patterns/external-configuration-store)|Extracts configuration to a service that's externalized to the application to support dynamic updates to configuration values without requiring code changes or application redeployment. This separation of application configuration from application code supports environment-specific configuration and applies versioning to configuration values. External configuration stores are also a common place to manage feature flags to enable safe deployment practices.|
|[Gateway Aggregation](/azure/architecture/patterns/gateway-aggregation)|Simplifies client interactions with your workload by aggregating calls to multiple backend services in a single request. This topology enables backend logic to evolve independently from clients, allowing you to change the chained service implementations, or even data sources, without needing to change client touchpoints.|
|[Gateway Offloading](/azure/architecture/patterns/gateway-offloading)|Offloads request processing to a gateway device before and after forwarding the request to a backend node. Adding an offloading gateway to the request process enables you to manage the configuration and upkeep of the offloaded functionality from single point instead of managing it from multiple nodes.|
|[Gateway Routing](/azure/architecture/patterns/gateway-routing)|Routes incoming network requests to various backend systems based on request intents, business logic, and backend availability. Gateway routing enables you to decouple requests from backends, which in turn enables your backends to support advanced deployment models, platform transitions, and a single point of management for domain name resolution and encryption in transit.|
|[Health Endpoint Monitoring](/azure/architecture/patterns/health-endpoint-monitoring)|Provides a way to monitor the health or status of a system by exposing an endpoint that's specifically designed for that purpose. Standardizing which health endpoints to expose, and the level of analysis in the results, across your workload can help you triage issues.|
|Messaging Bridge|Provides an intermediary to enable communication between messaging systems that are otherwise incompatible because of protocol or format. This decoupling provides flexibility when you transition messaging and eventing technology within your workload or when you have heterogeneous requirements from external dependencies.|
|[Publisher/Subscriber](/azure/architecture/patterns/publisher-subscriber)|Decouples components of an architecture by replacing direct client-to-service or client-to-services communication with communication via an intermediate message broker or event bus. This layer of indirection can enable you to safely change the implementation on either the publisher or subscriber side without needing to coordinate changes to both components.|
|[Sidecar](/azure/architecture/patterns/sidecar)|Extends the functionality of an application by encapsulating non-primary or cross-cutting tasks in a companion process that exists alongside the main application. This pattern provides an approach to implementing flexibility in tool integration that might enhance the application's observability without requiring the application to take direct implementation dependencies. It enables the sidecar functionality to evolve independently and be maintained independently of the application's lifecycle.|
|[Strangler Fig](/azure/architecture/patterns/strangler-fig)|Provides an approach for systematically replacing the components of a running system with new components, often during a migration or modernization of the system. This pattern provides a continuous improvement approach, in which incremental replacement with small changes over time is preferred rather than large systemic changes that are riskier to implement.|

## Next steps

Review the cloud design patterns that support the other Azure Well-Architected Framework pillars:

- [Cloud design patterns that support reliability ](../reliability/design-patterns.md)
- [Cloud design patterns that support security](../security/design-patterns.md)
- [Cloud design patterns that support cost optimization](../cost-optimization/design-patterns.md)
- [Cloud design patterns that support performance efficiency](../performance-efficiency/design-patterns.md)