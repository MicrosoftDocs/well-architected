---
title: Design principles for sustainability workloads on Azure
description: Understand the design principles for building a sustainable application on Microsoft Azure.
author: Zimmergren
ms.author: tozimmergren
ms.date: 07/22/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories:
  - management-and-governance
  - web
products: Azure
ms.custom:
  - sustainability
---

# Design principles of a sustainable workload

The sustainability design methodology provides a framework to record, report and reduce the environmental impact of your workloads.

To achieve an increase in carbon efficiency, consider how your workload directly, and indirectly, can reduce carbon emissions through:

- Using less physical and virtual resources
- Using less energy
- Using energy and resources more intelligently
- Supporting older devices

It is important to effectively record, report, and reduce carbon emissions through actionable insights.

- Gain transparency into your current carbon impact
- Estimate savings
- Take action to accelerate progress

These critical design principles for sustainability resonate and extend the quality pillars of the Azure Well-Architected Framework&mdash;[Reliability](/azure/architecture/framework/#reliability), [Security](/azure/architecture/framework/security/), [Cost Optimization](/azure/architecture/framework/cost/), [Operational Excellence](/azure/architecture/framework/devops/), and [Performance Efficiency](/azure/architecture/framework/scalability/).

## Principles of green software

The principles of green software are the starting point to understand the SCI model and how this will be included in our framework.

Each design principle map to one or more of the pillars in the Azure Well-Architected Framework.

### Carbon Efficient

The application or software must emit the least amount of carbon possible. A carbon efficient cloud application is one that is optimized, and the starting point is the cost – streamlining the application infrastructure and cost will ensure that no unnecessary resources are wasted in the cloud to run the software. But this is, obviously not enough, as you might have cost optimized your application but still waste tons of resources that emit carbon for no reason.

|Design principle|Pillar|Considerations|
|---|---|---|
|Deploy low-carbon regions and process when carbon intensity is low|Performance Efficiency|TBD.|
|Leverage repeatable/automated practices to deploy, operate and maintain your solution to maximize environmental efficiency|Operational Excellence|TBD|
|Allow team members to share their workload and company-specific best practices for sustainable operations|Operational Excellence|TBD|

### Electricity

The goal of this principle is that you build applications that are energy-efficient. This is a common pattern for mobile applications, since they must rely on a battery powered device and are usually optimizing its consumption. It is less common, however, for desktop or web applications, since until now, developers have never been asked to optimize the electricity consumption of their software.

|Design principle|Pillar|Considerations|
|---|---|---|
|[Update services, OS, software libraries to gain performance efficiencies](/azure/architecture/hybrid/azure-update-mgmt)|Performance Efficiency|TBD|
|Consider ARM SKUs for VMs/compute|Performance Efficiency|TBD|
|Profile workloads to make use of parallelization where possible with GPUs vs CPUs for efficiency gains (e.g. inference). Profile workloads to get balance of CPU and memory usage.|Performance Efficiency|TBD|
|[Optimize for async access patterns. Queue requests that do not require immediate processing - buffer then process in batch. Flatten consumption to avoid spiky requests](/azure/architecture/patterns/async-request-reply)|Performance Efficiency|TBD|
|[Select regions based on where the consumer resides](/azure/architecture/solution-ideas/articles/move-azure-resources-across-regions)|Performance Efficiency|TBD|
|[Consider CDN and/or enable local caching](/azure/architecture/best-practices/cdn)|Performance Efficiency|TBD|
|Enable network compression. Reduce network payload|Performance Efficiency|TBD|
|[Use storage best suited to data access patterns](/azure/architecture/guide/design-principles/use-best-data-store)|Performance Efficiency|TBD|
|[Monoliths -> microservices to scale only necessary components (if it fits workload profile)](/azure/architecture/guide/architecture-styles/microservices)|Performance Efficiency|TBD|
|Optimise code for efficient resource usage, e.g. modularity; DRY; efficient algorithms e.g. O(n) > O(n^2)|Performance Efficiency|TBD|
|[Reduce chattiness of APIs. Throttle APIs. Reduce payload returned](/azure/architecture/best-practices/message-encode)|Performance Efficiency|TBD|
|Evaludate server-side vs client-side rendering. That is code rendered once preferable over rendering per request.|Performance Efficiency|TBD|
|Be aware of UX design for sustainability e.g. use SD rather than HD|Performance Efficiency|TBD|
|Keep OS, language runtimes and libraries up to date for latest efficiency gains|Operational Excellence|TBD|
|Review platform updates regularly, and upgrade to newer/more efficient services as they become available|Operational Excellence|TBD|
|Use DDoS protection to mitigate the compute impact of a successful attack|Security|TBD|
|Use AV in passive mode when third party AV apps are used to avoid high CPU usage|Security|TBD|
|Archive log data to cold storage|Security|TBD|
|[Restartability - amount of processing to recover from failure](/azure/backup/manage-recovery-points#impact-of-expired-recovery-points-for-items-in-soft-deleted-state)|Reliability|TBD|
|Circuit breaker patterns - if a service isn't available, don't continue to ping it/retry it|Reliability|TBD|
|Power off workloads out of hours (such as Dev/Test workloads)|Cost Optimization|TBD|

### Carbon Aware Computing

We need to make the application aware of how much carbon it’s emitting. This way, we can react to specific conditions of energy supply, using demand shifting and demand shaping techniques:

- Demand shifting means moving the workloads and resources to regions or datacenters where the energy supply is higher (and incidentally the cost is lower)
- Demand shaping means changing the application’s behavior and appearance to match the energy supply in real-time. A good practice is to build a eco-version of the app and keep it as benchmark for demand shaping but also for carbon optimization.

### Embodied Carbon

Embodied carbon is the carbon that was emitted to build a device. Therefore, a sustainable application will make sure older devices are supported and will maximize the efficiency of each device.  The goal is to build hardware-efficient applications.

### Reducing emissions

In order to reduce carbon emissions, we have three possible solutions:
-	Carbon neutralization – compensating carbon emissions
-	Carbon avoidance – not emitting carbon in the first place
-	Carbon removal – subtract carbon from the atmosphere
Carbon removal is referred to as “the only way to bring our atmospheric levels back to normal and undo the damage already done.” The goal of green software is to avoid emitting unnecessary emissions in the first place.

### Measuring

Measuring carbon emissions of a cloud application is a complex task, as it involves the whole ecosystem of the software: from the cloud infrastructure (where we have the emissions dashboards to help us out), to the network path that is crossed, to the edge technology and user devices. With the SCI, we aren't targeting a discrete measurement of carbon emissions, but a score that will change over time and with our optimization techniques.

## Next steps

Review cross-cutting concerns associated with sustainable workloads.

> [!div class="nextstepaction"]
> [Cross-cutting concerns](sustainability-cross-cutting-concerns.md)