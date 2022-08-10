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

When building new or updating existing applications, it's crucial to consider how the solution may impact the climate and if there are ways to improve and optimize. Learn about considerations and recommendations to optimize your code and applications for a more sustainable application design.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Code efficiency

Designing and developing with efficiency in mind cannot only reduce cost and increase performance. It can hold the added benefit of reducing unnecessary emissions into our atmosphere, resulting in improved sustainability.

### Design considerations

- Monolithic applications usually scale as a unit, leaving little room to scale only the individual components that may need it.

- Many modern cloud applications are designed to transact many messages between services and components asynchronously.
  - Consider the format used to encode the payload data.
  - How much information does your application need to communicate, and is there room to reduce the chattiness?
  - Reducing the number of messages transacted between components (chattiness) can significantly help performance and efficiency and ultimately increase energy efficiency.

- Consider how applications render information. Does the application need to critically serve everything in the highest quality, resulting in higher bandwidth and processing?
  - Is there room for reducing the quality of components in the UI to serve sustainability goals better?

### Design recommendations

- Move monoliths to a [microservice architecture](/azure/architecture/guide/architecture-styles/microservices).
  - Allow scaling of only the necessary components during peak load.
  - Reduce the overhead and resources required for deploying monolithic applications.
  - Consider this tradeoff: While reducing the compute resources required, you may increase the amount of traffic on the network, and the complexity of the application may increase significantly.

- Improve API efficiency.
  - Learn about the [chatty I/O antipattern](/azure/architecture/antipatterns/chatty-io/) to better understand how a large number of requests can impact performance and responsiveness.
  - Improve the reliability and reduce unnecessary load to your systems. Implement [advanced request throttling with API Management](/azure/api-management/api-management-sample-flexible-throttling).
  - Minimize the amount of data the application returns from requests by being selective and encoding the messages. See [message encoding considerations](/azure/architecture/best-practices/message-encode).
  - Cache data to avoid re-requesting the same type of information unless necessary.

- Ensure backward software compatibility, so it works on legacy hardware.
  - Support more end-user consumer devices, like older browsers and operating systems. This backward compatibility improves hardware efficiency by reusing existing hardware instead of requiring a hardware upgrade for the solution to work.

- Evaluate server-side vs. client-side rendering.
  - Rendering something once may be more efficient on the server, but rendering something often may be more efficient on the client. Evaluate what and how often your application displays content. Consider where it may be best suited to process the rendering.

- Be aware of UX design for sustainability.
  - Consider reducing the number of components to load and render on pages.
  - If applicable, determine whether the application can render lower resolution images and videos.

- Optimize code for efficient resource usage.
  - Reduce CPU cycles and the number of resources you need for your application.
  - Use optimized and efficient algorithms and design patterns.
  - Consider the [Don't repeat yourself (DRY)](/dotnet/architecture/modern-web-apps-azure/architectural-principles#dont-repeat-yourself-dry) principle.

## Health probes

From a sustainability perspective, a healthy application produces fewer errors and wastes fewer resources, increasing efficiency and lowering the carbon footprint.

### Design considerations

- Consider evaluating and preventing applications from performing operations that are likely to fail. Repeated failures can lead to overhead and unnecessary processing that you can avoid with proper design patterns.

### Design recommendations

- Make use of circuit breaker patterns.
  - A circuit breaker can act as a proxy for operations that might fail and should monitor the number of recent failures that have occurred and use that information to decide whether to proceed.
  - Study the [Circuit Breaker pattern](/azure/architecture/patterns/circuit-breaker), and then consider how you can [implement the Circuit Breaker patterns](/dotnet/architecture/microservices/implement-resilient-applications/implement-circuit-breaker-pattern) to your applications.

## Next step

Review the design considerations for the application platform.

> [!div class="nextstepaction"]
> [Application platform](sustainability-application-platform.md)
