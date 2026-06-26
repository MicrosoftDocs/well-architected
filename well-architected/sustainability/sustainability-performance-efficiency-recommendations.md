---
title: Performance Efficiency recommendations for sustainable workloads on Azure
description: Consolidated Performance Efficiency-focused recommendations mapped from sustainable workload design areas on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 06/25/2026
ms.topic: concept-article
---

# Performance Efficiency recommendations for sustainable workloads on Azure

Sustainability in cloud workloads and performance are tightly linked because they're both fundamentally driven by the same underlying constraint: resource consumption (compute, memory, storage, network, and time).

Performance can be a strong sustainability driver. This article examines sustainability through the lens of the Performance Efficiency pillar and answers these design questions: 

- How can you improve application-level performance (API patterns, rendering, UX, and content delivery) to reduce compute, network, and energy consumption without degrading user experience?
- How should you optimize compute, storage, and network usage end-to-end so that performance gains translate into lower environmental impact, not just faster execution?
- How can you tune scaling, service-tier choices, and CI/CD practices to avoid hidden waste from overprovisioning, idle capacity, and repetitive pipeline work?
- How do you modernize and continuously operate workloads so efficiency improvements are sustained over time while balancing tradeoffs in cost, reliability, and operational complexity?


## Improve application efficiency

Cloud applications often rely on frequent communication between services, APIs, and distributed components. Every API request consumes network bandwidth, compute resources, memory, and consequently energy.

Applications might generate excessive service-to-service communication patterns that increase network traffic and backend processing requirements. Large or inefficient payloads can result in unnecessary data transfer, higher latency, and increased energy consumption. Without controls, excessive API requests can create avoidable load on backend services, leading to higher resource utilization and reduced efficiency.

Certain Azure services support sustainable APIs. For example, see [Environmentally sustainable APIs in Azure API Management](/azure/api-management/sustainability).

An unsuitable rendering strategy can lead to inefficient resource use and unnecessary processing on either the client or server. Rendering on the server can reduce the processing burden on end-user devices, take advantage of centralized caching, and use more energy-efficient infrastructure. Conversely, client-side rendering can reduce server resource consumption and take advantage of modern device capabilities and browser caching.

User experience design can have a significant impact on the sustainability of an application. Complex interfaces, large media assets, excessive page content, and inefficient design choices can increase network traffic, data processing, and energy consumption across both client and server environments.

| Recommendation                                                                                                                                  | Sustainability Benefit                                                                                                              |
| ----------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| Optimize API communication patterns by reducing chatty interactions, minimizing payload sizes, and using efficient message encoding techniques. | Reduces network traffic, data transfer volumes, backend processing, and overall energy consumption.                                 |
| Implement request throttling and response caching by using services such as Azure API Management.                                                  | Prevents unnecessary resource consumption, reduces repeated processing, and improves the efficiency of backend services.            |
| Monitor API usage patterns and continuously optimize high-volume or inefficient endpoints.                                                      | Identifies opportunities to improve performance efficiency, reduce resource utilization, and lower the workload's carbon footprint. |
| Evaluate rendering strategies and select server-side or client-side rendering based on user device capabilities, network conditions, and workload requirements. | Optimizes compute utilization across client and server environments, reducing unnecessary energy consumption and improving overall workload efficiency. |
| Leverage caching mechanisms, whether centralized on the server or within the browser, to minimize repeated rendering and data processing operations.            | Reduces compute, network traffic, and resource consumption, lowering the environmental impact of delivering application content.                        |
| Design lightweight and efficient user interfaces by minimizing unnecessary components, pages, and media assets.                                              | Reduces network traffic, data processing, and compute resource consumption, improving overall energy efficiency.              |
| Optimize content delivery by using appropriately sized images and videos, avoiding unnecessary asset downloads, and leveraging system fonts where practical. | Lowers bandwidth usage, client-side processing requirements, and associated carbon emissions.                                 |
| Improve user navigation and findability, and consider offering resource-efficient experiences such as simplified interfaces or dark mode options.            | Reduces unnecessary data retrieval and energy consumption while helping users interact more efficiently with the application. |



## Optimize compute resources

If you don't measure how applications consume CPU, memory, and processing capacity, you might unknowingly deploy inefficient code or underutilize available infrastructure.

| Recommendation                                                                                                                                             | Sustainability Benefit                                                                                                               |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Profile workloads to understand concurrency patterns, resource utilization, and opportunities for performance optimization or parallelization.             | Improves compute efficiency and ensures resources are used effectively, reducing unnecessary energy consumption.                     |
| Evaluate specialized compute options, such as GPU-based resources, where they provide efficiency gains. | Can improve processing efficiency and reduce the overall compute resources required to complete workloads.                           |
| Establish CPU and memory baselines and incorporate sustainability-focused testing, monitoring, alerts, and quality gates into deployment pipelines. | Helps identify inefficient code and resource consumption early, prevents unsustainable workloads from reaching production, and supports continuous improvement in workload efficiency. |


> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: GPU-based resources can significantly improve performance and processing efficiency for machine learning and highly parallel workloads, reducing execution times and potentially improving energy efficiency. However, these resources typically incur higher operational costs than CPU-based alternatives. Evaluate these options to ensure the performance benefits justify the additional expense.
>
>   Review and adjust resource utilization baselines and thresholds over time as application functionality evolves to avoid generating false positives or unnecessarily blocking deployments.


## Reduce network traversal

Network traffic directly contributes to the energy consumption and carbon footprint of cloud workloads. Excessive data transfers, long network paths, and inefficient content delivery patterns can increase bandwidth utilization, processing requirements, and latency. By optimizing how data is delivered, cached, compressed, and routed, you can reduce unnecessary network activity, improve application performance, and lower the environmental impact associated with moving data across distributed systems.

| Recommendation                                                                                                                                                                                    | Sustainability Benefit                                                                                                                                |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| Use content delivery networks (CDNs), caching strategies, including in-memory caching where appropriate, and data compression techniques to reduce network traffic and optimize content delivery. | Reduces bandwidth consumption, server load, repeated processing, and energy required to deliver application content.                                  |
| Deploy workloads and content closer to users and keep dependent resources within the same cloud and region where practical.                                                                       | Minimizes network traversal, reduces reliance on public internet routing, improves performance, and lowers the environmental impact of data movement. |
| Regularly review and tune caching configurations, such as cache retention and expiration settings, to maximize efficiency and resource utilization.                                               | Improves cache effectiveness, reduces unnecessary data transfers, and supports ongoing reductions in energy consumption.                              |


> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Network optimization techniques such as caching and compression can reduce bandwidth requirements and improve sustainability outcomes, but they might require additional compute, memory, or storage resources. Tune cache retention settings and compression strategies to balance performance, resource utilization, and overall sustainability benefits.

## Reduce storage footprint

Efficient storage design plays a key role in reducing the environmental impact of cloud workloads by minimizing unnecessary data retention, transfer, and processing. Inefficient storage practices, such as uncompressed data, poorly optimized queries, excessive backups, and overly broad logging, can decrease energy efficiency.

Unmanaged backup recovery points and excessive log retention can silently accumulate over time, increasing storage footprint and environmental impact without delivering proportional value.

| Recommendation                                                                                                                                                                                          | Sustainability Benefit                                                                                                                             |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| Apply storage compression, optimize database queries (e.g., for Azure SQL Database), and reduce unnecessary data transfer to improve storage efficiency and performance.                                | Reduces storage capacity requirements, lowers bandwidth consumption, and decreases compute overhead associated with data processing and retrieval. |
| Align data with appropriate storage access tiers and lifecycle policies, ensuring frequently accessed data is kept in hot storage while infrequently accessed data is moved to cold or archive storage. | Minimizes energy consumption by reducing unnecessary high-cost data access and optimizing storage resource utilization.                            |
| Implement and regularly review backup, recovery point, and log retention policies using services such as Azure Backup and Azure Monitor to prevent unnecessary data accumulation.                       | Reduces long-term storage growth, minimizes idle data retention, and lowers the environmental impact of storing and processing unused data.        |


## Balance with scalability needs

Workloads that rely on fixed or overprovisioned capacity might consume more compute resources than necessary, leading to underutilization and inefficient energy use. Selecting higher service tiers for features that aren't fully utilized can result in idle capacity that still incurs environmental and operational cost. Without regular review of scalability requirements, organizations might accumulate infrastructure that exceeds actual demand, reducing overall efficiency and sustainability performance.


| Recommendation                                                                                                                 | Sustainability Benefit                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| Review platform and service tier decisions to ensure they align with actual workload demand and resource utilization patterns. | Reduces underutilized compute capacity and improves overall hardware efficiency.                                |
| Prefer services and architectures that support dynamic scaling based on demand rather than fixed provisioned capacity.         | Ensures resources are allocated only when needed, minimizing idle compute and lowering energy consumption.      |
| Evaluate whether higher-tier services are necessary for required features, and avoid overprovisioning where possible.          | Prevents unnecessary resource allocation and reduces the environmental impact of unused or idle infrastructure. |

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Higher-tier services might be required to access specific features or capabilities regardless of actual utilization. In such cases, organizations must balance functional requirements against potential inefficiencies from provisioned but underused capacity.

## Drive efficiency in deployments

CI/CD pipelines can become a hidden source of unnecessary resource consumption. Repetitive build processes can lead to redundant CPU cycles, longer pipeline durations, and increased network traffic for dependency retrieval and artifact transfer.

| Recommendation                                                                                                                  | Sustainability Benefit                                                                                     |
| ------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Implement caching in CI/CD pipelines to store and reuse build outputs and artifacts between runs when inputs don't change.        | Reduces repeated compute cycles, lowers pipeline execution time, and decreases overall energy consumption. |
| Use local caching on self-hosted CI/CD agents where possible to minimize network transfers and external data movement.          | Reduces network traffic and associated energy usage while improving build efficiency and performance.      |

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Caching introduces extra storage requirements and might need cache invalidation strategies to ensure correctness. Poorly managed caches can lead to stale outputs, so caching strategies must be carefully designed to balance efficiency gains with build reliability.

## Use modern technology 

Legacy applications that you don't update or modernize might run inefficiently on current cloud infrastructure, consuming more compute resources than necessary. Older architectures might not take advantage of modern scalability, automation, or energy-efficient platform capabilities. However, modernization efforts can require significant engineering effort and might introduce risk or complexity, especially when dealing with deprecated systems or tightly coupled dependencies. Additionally, frequent upgrades must consider compatibility and operational stability to avoid disruption.

| Recommendation                                                                                                                              | Sustainability Benefit                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Identify and modernize legacy applications by migrating to modern cloud-native architectures such as serverless or optimized PaaS services. | Improves compute efficiency, reduces infrastructure overhead, and leverages more energy-efficient platform capabilities. |
| Keep platforms, services, and operating systems up to date by using automated update mechanisms such as Azure Automation Update Management.    | Ensures workloads benefit from the latest performance and energy efficiency improvements in modern software versions.    |




## Related guidance

- [Performance Efficiency pillar](/azure/well-architected/performance-efficiency/)



