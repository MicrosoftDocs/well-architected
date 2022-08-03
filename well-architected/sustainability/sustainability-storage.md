---
title: Data and storage design considerations for sustainable workloads on Azure
description: This design area explores considerations around storage design for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/27/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - storage
products: Azure
ms.custom:
  - sustainability
---

# Data and storage design considerations for sustainable workloads on Azure

[Intro paragraph]

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Storage operations and governance

### Design considerations

### Design recommendations

|Design principle|Pillar|SGF Pillar|Considerations|
|---|---|---|---|
|Classify data and move stale data to cold storage; delete data no longer needed|Operational Excellence|Hardware Efficiency|TBD|
|Reduce copies of data or backups where possible|Cost Optimization|Hardware Efficiency|TBD|

## Storage efficiency

### Design considerations

### Design recommendations

|Design principle|Pillar|SGF Pillar|Considerations|
|---|---|---|---|
|[Use storage best suited to data access patterns](/azure/architecture/guide/design-principles/use-best-data-store)|Performance Efficiency|Energy Efficiency|TBD|
|[Enable storage compression](/azure/cdn/cdn-improve-performance)|Performance Efficiency|Hardware Efficiency|TBD|
|[Store only what is relevant. Implement policies to streamline the process](/azure/architecture/guide/design-principles/use-best-data-store)|Performance Efficiency|Hardware Efficiency|TBD|
|[Reducing number of recovery points in place](/azure/backup/manage-recovery-points#impact-of-expired-recovery-points-for-items-in-soft-deleted-state)|Reliability|Hardware Efficiency|TBD|

## Next step

Review the considerations for application design.

> [!div class="nextstepaction"]
> [Operations](sustainability-application-design.md)