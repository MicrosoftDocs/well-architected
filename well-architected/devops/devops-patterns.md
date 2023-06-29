---
title: Operational Excellence patterns
titleSuffix: Cloud Design Patterns
description: Learn about management and monitoring patterns to support cloud applications that contain unique challenges because the applications run in a remote datacenter.
author: dragon119
ms.author: martinek
ms.date: 05/04/2023
ms.topic: conceptual
ms.custom:
  - design-pattern
keywords:
  - design pattern
---

# Operational Excellence patterns

Cloud applications run in a remote datacenter where you don't have full control of the infrastructure or, in some cases, the operating system. This situation can make management and monitoring more difficult than an on-premises deployment. Applications must expose runtime information that administrators and operators can use to manage and monitor the system. Applications must also support changing business requirements and customization without requiring the application to be stopped or redeployed.

|                              Pattern                               |                                                              Summary                                                              |
|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
|                   [Ambassador](/azure/architecture/patterns/ambassador)                   |                 Create helper services that send network requests on behalf of a consumer service or application.                 |
|        [Anti-corruption Layer](/azure/architecture/patterns/anti-corruption-layer)        |                       Implement a façade or adapter layer between a modern application and a legacy system.                       |
| [External Configuration Store](/azure/architecture/patterns/external-configuration-store) |                Move configuration information out of the application deployment package to a centralized location.                |
|          [Gateway Aggregation](/azure/architecture/patterns/gateway-aggregation)          |                          Use a gateway to aggregate multiple individual requests into a single request.                           |
|           [Gateway Offloading](/azure/architecture/patterns/gateway-offloading)           |                              Offload shared or specialized service functionality to a gateway proxy.                              |
|              [Gateway Routing](/azure/architecture/patterns/gateway-routing)              |                                   Route requests to multiple services using a single endpoint.                                    |
|              [Geodes](/azure/architecture/patterns/Geodes)              |                                   Deploy backend services into a set of geographical nodes, each of which can service any client request in any region.                                    |
|   [Health Endpoint Monitoring](/azure/architecture/patterns/health-endpoint-monitoring)   |   Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals.    |
|   [Materialized View](/azure/architecture/patterns/materialized-view)   |   Generate prepopulated views over the data in one or more data stores when the data isn't ideally formatted for required query operations.    |
|                      [Sidecar](/azure/architecture/patterns/sidecar)                      |         Deploy components of an application into a separate process or container to provide isolation and encapsulation.          |
|                    [Strangler Fig](/azure/architecture/patterns/strangler-fig)                    | Incrementally migrate a legacy system by gradually replacing specific pieces of functionality with new applications and services. |
