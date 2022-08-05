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

- Decouple backend processing from a frontend host.
  - Consider if the frontend can be designed to answer requests while the backend processing can be asynchronous.
- Monolithic applications usually scale as a unit, leaving little room to scale only the individual components that may need it.
- Apps that aren't optimized can lead to issues with scalability, deployability, code maintenance, and a waste of energy.
- Many modern cloud applications are designed to asynchronously transact many messages between services and components.
  - Consider the format used to encode the payload data.
  - Think about how much information your applications need to communicate, and if there's room to reduce the chattiness of your apps.
  - Reducing the number of messages transacted between components (chattiness) can greatly help performance, efficiency, and ultimately increase the energy efficiency.
- How is your app rendering? By using client-side rendering or server-side?
- [Autoscale components to optimize utilization](/azure/architecture/best-practices/auto-scaling)

---

- [Optimize for async access patterns](/azure/architecture/patterns/async-request-reply).
- Apply appropriate designs for application messaging.
  - Learn about the [chatty I/O antipattern](/azure/architecture/antipatterns/chatty-io/) to better understand how performance and responsiveness can be impacted by a large number of requests.
  - Improve the reliability and reduce unnecessary load to your systems by implementing [advanced request throttling with API Management](/azure/api-management/api-management-sample-flexible-throttling).
  - Minimize the amount of data the application returns from requests by being selective and encoding the messages. See [message encoding considerations](/azure/architecture/best-practices/message-encode).
- Evaluate server-side vs client-side rendering.
  - That is code rendered once preferable over rendering per request.

## Code efficiency

### Design considerations

- Move toward a [microservice architecture style](/azure/architecture/guide/architecture-styles/microservices).
  - Allow scaling of only the necessary components during peak load.
  - Reduce the overhead for deploying monolithic applications.

- Consider designing apps according to recommended design patterns to help reduce the carbon footprint of the workload.

- Optimize the code for efficient resource usage.
  - Learn about the [DRY principle](/archive/msdn-magazine/2019/june/patterns-and-practices-super-dry-development-for-asp-net-core).
  - Build efficient algorithms. For example, ```O(n) > O(n^2)```

### Design recommendations

|Design principle|Pillar|SGF Pillar|
|---|---|---|
|Move monoliths to microservices only to scale only necessary components (if it fits workload profile)|
|Optimize code for efficient resource usage, e.g. modularity; DRY; efficient algorithms e.g. O(n) > O(n^2)|Performance efficiency|Energy efficiency|
|Improve API efficiency - Reduce chattiness of APIs, throttle APIs, reduce payload returned|Performance efficiency|Energy efficiency|
|Ensure software backwards compatibility so it works on legacy hardware|Performance Efficiency|Hardware efficiency|
|Evaluate server-side vs client-side rendering|Performance efficiency|Energy efficiency|
|Be aware of UX design for sustainability, for example,  use SD rather than HD|Performance Efficiency|Energy efficiency|

## Application modernization

### Design considerations

### Design recommendations

|Design principle|Pillar|SGF Pillar|
|---|---|---|
|Apply [cloud-native design patterns](/azure/architecture/patterns/) when writing or updating your workload code|Operational Excellence|Carbon awareness|
|Update legacy code, identify inefficient legacy code for modernization, make use of serverless, PaaS|Operational Excellence|Hardware Efficiency|

## Application and system health

### Design considerations

### Design recommendations

|Design principle|Pillar|SGF Pillar|
|---|---|---|
|Circuit breaker patterns - if a service isn't available, don't continue to ping it/retry it|Reliability|Energy efficiency|

## Next step

Review the design considerations for application platform.

> [!div class="nextstepaction"]
> [Application platform](sustainability-application-platform.md)