---
title: Design methodology for HPC workloads on Azure
description: Understand the architectural process of building a mature HPC application on Microsoft Azure.
author: padmalathas
ms.author: padmalathas
ms.date: 03/10/2026
ms.topic: concept-article
---

# Design methodology for HPC workloads on Azure

High Performance Computing (HPC) workloads solve compute‑intensive problems by running tasks across clusters of machines in parallel. Designing HPC workloads on Azure requires careful alignment between workload behavior, infrastructure choices, and operational practices.

This article describes a design methodology that you can use to systematically define and refine requirements as it applies to the characteristics and common problems of HPC workloads. If you're uncertain about various design decisions and technology options, revisit this methodology to stay aligned with the patterns that're applicable to this class of workloads. It applies to both new deployments and existing HPC environments being optimized.


## Understand workload coupling and communication

HPC workloads can be categorized in several ways, such as by compute intensity, data access patterns, acceleration requirements, or job throughput. The get started guidance introduces these broader workload categories to help establish overall context.

Focus on workload coupling and communication patterns. Because coupling has a direct and disproportionate impact on network selection, node placement, and performance-sensitive design decisions. Other workload characteristics such as whether a workload is GPU‑accelerated, data‑intensive, or high‑throughput are important, and can lead to architectural constraints to an extent.

HPC workloads are composed of components with different communication patterns. Identifying these patterns early is critical because they directly influence compute, network, and placement decisions.

| Coupling pattern | Key characteristics | Design guidance |
| --------------   | ------------------- | --------------- |
| Tightly coupled  | Tasks communicate frequently and run in lockstep | Use low‑latency, high‑bandwidth networking. Co‑locate nodes. Prioritize performance. |
| Loosely coupled  | Tasks run independently or communicate infrequently | Standard networking is sufficient. Use cost‑efficient and scalable resources. |

Most HPC applications contain both types. For example, a simulation core may be tightly coupled, while post‑processing or reporting tasks might be loosely coupled.

Match compute and network choices to communication patterns. Use high‑performance resources only where they provide real benefit.

> [!NOTE]
> Other workload characteristics introduced in [Getting started](get-started-overview.md) such as GPU acceleration, data intensity, or high‑throughput execution are addressed in later design areas where they more directly influence storage architecture, scheduling strategy, and cost optimization.

## Design scalable orchestration and scheduling

Efficient orchestration maximizes cluster throughput and reduces job wait times. Scaling should be automated and driven by workload demand rather than static capacity.

Consider these key scaling signals when deciding on auto-scaling thresholds:
- Job queue depth: Scale out when queued jobs increase, scale in when demand drops.
- Resource utilization: Add capacity when nodes remain saturated, remove idle nodes.
- Job priority: Allow urgent or high‑priority jobs to trigger faster scaling.

Monitor scheduling metrics such as queue wait time, utilization, throughput, and cost per job. Use these insights to refine scaling policies over time and detect predictable demand patterns.

Scaling strategy isn't a one time activity. Make sure you refine thresholds based on observed behavior during peak, steady, and off-peak load. 


## Build fault tolerance capabilities

HPC jobs often run for hours or days, making them vulnerable to failures. As a general practice always avoid single points of failure. To maintain compute resiliency, maintain spare capacity that can absorb node failures.

Be prepared to handle failures with adequate recoverability measures. Keep infrastructure and configuration reproducible for fast recovery. For HPC workloads, checkpointing is a key strategy so that jobs can resume reliably. In case of failed jobs make sure you have mechanisms in place that can requeue critical jobs faster.

Plan for inevitable failures by ensuring workloads can resume with minimal delay and no loss of data or progress.


## Manage costs with predictability

HPC environments can scale quickly, and so can costs. Cost control must be built into the architecture from the start.

- Define budgets and quotas that reflect bursty HPC usage patterns, and set alerts to track consumption as large jobs scale up and down. 

- Monitor resource utilization to detect over or under‑provisioning and adjust capacity accordingly.

- Account for the total cost of HPC workloads, including compute, storage, data movement, and software licenses, not just compute spend.

- Tag resources or jobs by project or user to enable accurate cost attribution and informed optimization decisions.

- Balance performance with cost by sizing resources to actual demand and reviewing usage regularly.


## Design for observability and automation

Operational success depends on visibility and repeatability. Observability and automation must be first‑class design concerns.

Monitor the signals that reveal how jobs and clusters behave at scale: job completion rates, queue wait times, throughput, and per-node utilization. Track hardware health trends to catch failures before they disrupt long-running computations. Correlate cost patterns with workload execution to understand how scheduling and scaling decisions affect efficiency.

Automation in HPC environments works best when it’s intentional and measured. Use infrastructure-as-code to deploy clusters consistently, then automate scaling, recovery, and routine maintenance around workload demand. 

Introduce automation incrementally, validate behavior under failure conditions, and use observability signals to confirm system state. The goal is reducing manual effort while retaining control over performance and reliability.


## Next step

Start your design journey by reviewing how the Well-Architected Framework pillars apply to HPC workloads.

> [Design principles for HPC workloads](get-started-overview.md)

