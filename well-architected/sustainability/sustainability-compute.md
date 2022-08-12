---
title: Considerations for sustainable compute workloads on Azure
description: This design area explores sustainability for compute workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/27/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - compute
products: Azure
ms.custom:
  - sustainability
---

# Considerations for sustainable compute workloads on Azure

Processing solutions in Azure of any kind often rely on compute resources. Whether it's a web app, an API, or a set of VMs, the compute considerations should be considered carefully. Learn about ways to optimize compute workloads for sustainability.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Compute efficiency

### Design considerations

- Seeing oversized compute workloads is not uncommon where much of the capacity is never utilized, ultimately leading to a waste of energy.

- Some data centers and regions have a different carbon footprint than others. Understanding which available regions have a lower carbon footprint is essential to making an informed decision about how and when our workloads should process data.

### Design recommendations

- Utilize [auto-scaling](/azure/architecture/best-practices/auto-scaling) and bursting capabilities.
  - Consider that it may require tuning to prevent unnecessary scaling during short bursts of high demand, as opposed to a static increase in demand.
  - Consider the application architecture as part of scaling considerations. For example, logical components should scale independently to match the demand of that component, as opposed to scaling the entire application if only a portion of the components needs scaling.

- Run [batch workloads](/azure/architecture/data-guide/big-data/batch-processing) during low carbon intensity periods.
  - Potential tradeoffs may include the effort and time it takes to move to a low-carbon region. Additionally, migrating data between data centers may not be carbon efficient, and the cost for new regions&mdash;including low-carbon regions&mdash;may be more expensive.

## App modernization

### Design considerations

### Design recommendations

|Design principle|Pillar|SGF Pillar|Considerations|
|---|---|---|---|
|Move to serverless|Cost Optimization|Hardware Efficiency|TBD|
|Use spot VMs|Cost Optimization|Hardware Efficiency|TBD|
|Use PaaS over IaaS where possible|Cost Optimization|Hardware Efficiency|TBD|

## Right sizing

### Design considerations

### Design recommendations

|Design principle|Pillar|SGF Pillar|Considerations|
|---|---|---|---|
|Maximize/efficient workload processing on compute|Cost Optimization|Hardware Efficiency|TBD|
|Power off workloads out of hours (such as Dev/Test workloads)|Cost Optimization|Energy Efficiency|TBD|

## Next step

Review the design considerations for networking and connectivity.

> [!div class="nextstepaction"]
> [Network and connectivity](sustainability-networking.md)