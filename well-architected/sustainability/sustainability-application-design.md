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

When building new or updating existing applications, it's important to think about how the solution may impact the climate and if there are ways to improve and optimize. Learn about considerations and recommendations to optimize your code and applications for a more sustainable application design.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Code efficiency

### Design considerations

- Monolithic applications usually scale as a unit, leaving little room to scale only the individual components that may need it.

- Many modern cloud applications are designed to asynchronously transact many messages between services and components.
  - Consider the format used to encode the payload data.
  - How much information does your application need to communicate, and is there's room to reduce the chattiness?
  - Reducing the number of messages transacted between components (chattiness) can greatly help performance, efficiency, and ultimately increase the energy efficiency.

- Consider how applications render information. Does the application need to critically serve everything in the highest quality, which results in a higher bandwidth and processing, or is there room for reducing the quality of components in the UI to better serve sustainability goals?

### Design recommendations

- Move monoliths to a [microservice architecture](/azure/architecture/guide/architecture-styles/microservices).
  - Allow scaling of only the necessary components during peak load.
  - Reduce the overhead and resources required for deploying monolithic applications.
  - Consider this tradeoff: While reducing the compute resources required, you may increase the amount of traffic on the network, and the complexity of the application may increase significantly.

- Improve API efficiency.
  - Learn about the [chatty I/O antipattern](/azure/architecture/antipatterns/chatty-io/) to better understand how performance and responsiveness can be impacted by a large number of requests.
  - Improve the reliability and reduce unnecessary load to your systems by implementing [advanced request throttling with API Management](/azure/api-management/api-management-sample-flexible-throttling).
  - Minimize the amount of data the application returns from requests by being selective and encoding the messages. See [message encoding considerations](/azure/architecture/best-practices/message-encode).
  - Cache data to avoid re-requesting the same type of information unless necessary.

- Ensure software backwards compatibility so it works on legacy hardware.
  - When more end-user consumer devices can be supported, like older browsers and operating systems, it helps in re-using existing hardware as opposed to requiring a hardware upgrade for the solution to work.

- Evaluate server-side vs. client-side rendering.
  - Rendering something once may be more efficient on the server, but rendering something often may be more efficient on the client. Evaluate what and how often your application displays content, and consider where it may be best suited to process the rendering.

- Be aware of UX design for sustainability.
  - Consider reducing the number of components on a page to ensure there's less things to load and render.
  - If applicable, determine whether the application can render lower resolution images and videos.

## Application modernization

### Design considerations

### Design recommendations

## Application and system health

### Design considerations

### Design recommendations


## Next step

Review the design considerations for application platform.

> [!div class="nextstepaction"]
> [Application platform](sustainability-application-platform.md)