---
title: Design Essentials Overview
author: PageWriter-MSFT
ms.date: 11/14/2025
ms.author: prwilk
description: Comprehensive collection of design guides that provide actionable guidance for implementing Azure Well-Architected Framework principles.
ms.topic: concept-article
---

# Design essentials overview

Design guides in the Azure Well-Architected Framework provide prescriptive guidance that translates the framework's key strategies into actionable steps. Built on the foundational strategies defined within the pillar recommendations, these guides span multiple pillars to show how architectural considerations work together in practice. Instead of walking through full, end-to-end scenarios, each design guide focuses on a single practice or decision point. This approach provides clear direction to help teams apply Well-Architected Framework principles confidently and consistently.

## Design guides summary

| Article | Description |
|:---------|:-------------|
| [Recommendations for developing background jobs](./design-guides/background-jobs.md) | Learn how to develop background jobs to minimize UI load, improve availability, and reduce response time. Covers when and how to use background jobs for tasks like batch processing and workflows. |
| [Develop a disaster recovery (DR) plan for multi-region deployments](./design-guides/disaster-recovery.md) | Guidance for creating a DR plan based on business priorities and reliability objectives. Explains DR strategies, terminology, and how to plan for wide-scope failures. |
| [Recommendations for handling transient faults](./design-guides/handle-transient-faults.md) | Learn how to handle transient faults in cloud applications, including retry strategies and best practices for maintaining reliability if temporary failures occur. |
| [Health modeling for workloads](./design-guides/health-modeling.md) | Use health modeling to improve workload reliability by quantifying application health and building custom health models that combine business context with monitoring data. |
| [Complete an Azure Well-Architected Review assessment](./design-guides/implementing-recommendations.md) | Overview of the Azure Well-Architected Review assessment process, including how to receive, prioritize, and integrate recommendations for continuous workload improvement. |
| [Optimize workload design by using flows](./design-guides/optimize-workload-using-flows.md) | Explore a three-step process for optimizing workloads through structured flow design, aligning flows with business processes, and applying best practices. |
| [Data partitioning recommendations for reliability](./design-guides/partition-data.md) | Learn how to partition data in distributed systems to improve scalability, reduce contention, and optimize performance, with strategies for different partitioning approaches. |
| [Architecture strategies for using availability zones and regions](./design-guides/regions-availability-zones.md) | Guidance for when and how to deploy workloads across availability zones or regions for reliability, cost, and performance, including trade-offs and best practices. |
| [Recommendations for using continuous integration](./design-guides/release-engineering-continuous-integration.md) | Learn how to use continuous integration in release engineering, including source control, automated testing, and best practices for modern software development. |
