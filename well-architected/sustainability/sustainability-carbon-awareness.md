---
title: Carbon awareness considerations for sustainable workloads on Azure
description: This design area explores building carbon aware computing workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/22/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - networking
products: Azure
ms.custom:
  - sustainability
---

# Carbon awareness considerations for sustainable workloads on Azure

[Intro paragraph]

## Design principles

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

## Another H2

## More H2

## Next step
