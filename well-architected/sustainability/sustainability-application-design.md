---
title: Application design of sustainable workloads on Azure
description: This design area explores application design considerations for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/27/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - networking
products: Azure
ms.custom:
  - sustainability
---

# Application design of sustainable workloads on Azure

[Intro paragraph]

## Carbon-aware application designs

### Design considerations

- Consider designing apps according to recommended design patterns to help reduce the carbon footprint of the workload.

### Design recommendations

- Apply [cloud-native design patterns](/azure/architecture/patterns/) when writing or updating your workload code.

## Design for energy efficiency

Reducing the electricity used by apps and solutions can help in achieving sustainability targets. During the design of new or existing applications, consider how to best optimize the workload to reduce wasted energy.

### Design considerations

- Decouple backend processing from a frontend host.
  - Consider if the frontend can be designed to answer requests while the backend processing can be asynchronous.
- Monolithic applications usually scales as a unit, leaving little room to scale only the individual components that may need it.
- Apps that are not optimized can lead to issues with scalability, deployability, code maintenance, and a waste of energy.
- Many modern cloud applications are designed to asynchronously transact many messages between services and components.
  - Consider the format used to encode the payload data.
  - Think about how much information your applications need to communicate, and if there is room to reduce the chattiness of your apps.
  - Reducing the amount of messages transacted between components (chattiness) can greatly help performance, efficiency, and ultimately increase the energy efficiency.
- How is your app rendering? By using client-side rendering or server-side?

### Design recommendations

- [Optimize for async access patterns](/azure/architecture/patterns/async-request-reply).
- Move toward a [microservice architecture style](/azure/architecture/guide/architecture-styles/microservices).
  - Allow scaling of only the necessary components during peak load.
  - Reduce the overhead for deploying monolithic applications.
- Optimize the code for efficient resource usage.
  - Learn about the [DRY principle](/archive/msdn-magazine/2019/june/patterns-and-practices-super-dry-development-for-asp-net-core).
  - Build efficient algorithms. For example, ```O(n) > O(n^2)```
- Apply appropriate designs for application messaging.
  - Learn about the [chatty I/O antipattern](/azure/architecture/antipatterns/chatty-io/) to better understand how performance and responsiveness can be impacted by a large number of requests.
  - Improve the reliability and reduce unnecessary load to your systems by implementing [advanced request throttling with API Management](/azure/api-management/api-management-sample-flexible-throttling).
  - Minimize the amount of data the application returns from requests by being selective and encoding the messages. See [message encoding considerations](/azure/architecture/best-practices/message-encode).
- Evaluate server-side vs client-side rendering.
  - That is code rendered once preferable over rendering per request.

TODO:

- [Autoscale components to optimize utilization](/azure/architecture/best-practices/auto-scaling)
- Update legacy code, identify inefficient legacy code for modernization, make use of serverless, PaaS
  - Operational Excellence, Hardware Efficiency
- Circuit breaker patterns - if a service isn't available, don't continue to ping it/retry it
  - Reliability, Energy efficiency
- [Demand shaping - Run batch workloads during low carbon intensity periods](/azure/architecture/data-guide/big-data/batch-processing)
  - Performance Efficiency, Carbon Awareness
- Ensure software backwards compatibility so it works on legacy hardware
  - Performance Efficiency, Hardware efficiency
- Be aware of UX design for sustainability, e.g.,  use SD rather than HD
  - Performance Efficiency, Energy efficiency

- Smaller failure units
  - Reliability, hardware efficiency

## Next step

Review the design considerations for secure and sustainable workloads.

> [!div class="nextstepaction"]
> [Operations](sustainability-security.md)