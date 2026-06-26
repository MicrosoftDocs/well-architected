---
title: Reliability recommendations for sustainable workloads on Azure
description: Consolidated reliability-focused recommendations mapped from sustainable workload design areas on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 06/25/2026
ms.topic: concept-article
---

# Reliability recommendations for sustainable workloads on Azure

Sustainability can be achieved by delivering the required level of reliability with the least unnecessary resource consumption possible. In many cases, designing for reliability and sustainability creates a natural architectural alignment, where resilient systems directly contribute to more sustainable workloads.

Poor reliability often increases environmental impact to compensate for instability. These reactive patterns increase compute usage, operational overhead, and long-term resource consumption across the workload lifecycle.

This article examines sustainability through the lens of the Reliability pillar and answers these design questions:

- How do you avoid wasting resources during failure?
- How do you avoid permanent over-engineering?
- How do you maintain efficient utilization over time?
- How do you maximize workload density while minimizing unnecessary infrastructure usage?
- How do you ensure testing improves reliability without creating avoidable emissions?

## Prevent failure amplification

Failures in distributed systems rarely remain isolated. A single dependency failure can trigger cascading retries, duplicate requests, queue buildup, and prolonged recovery activity across dependent services. These reactions increase compute usage, network traffic, storage operations, and operational overhead while delivering little or no additional business value.

From a sustainability perspective, workloads become inefficient when failure conditions generate unnecessary work. Systems that continuously retry failing operations or repeatedly process the same requests consume additional energy and extend recovery time.

For example, a temporary database outage can cause application instances to repeatedly retry failed requests while queues continue to grow behind the dependency. Although the original issue may be short-lived, the workload can consume significantly more resources during recovery than during normal operation.

| Recommendation                                                    | Sustainability benefit                                                          |
| ----------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Use circuit breaker patterns to stop repeated failing operations. | Prevents unnecessary compute consumption during dependency failures.            |
| Implement request throttling during overload conditions.          | Reduces cascading traffic amplification and backend stress.                     |
| Use graceful degradation patterns where possible.                 | Maintains partial functionality without triggering excessive recovery behavior. |
| Detect failure patterns early with monitoring and alerting.       | Reduces prolonged inefficient operation and accelerates recovery.               |
| Design workloads to isolate dependency failures.                  | Prevents localized issues from propagating across the system.                   |

## Design resiliency intentionally

A common anti-pattern is designing for maximum resiliency without aligning the architecture to actual business requirements.

Architecture approaches like excessive redundancy and unnecessary multi-region deployments have tradeoffs with sustainability. While these approaches may improve resiliency objectives, they also increase infrastructure footprint, embodied carbon, operational overhead, and continuous network synchronization costs.

For example, deploying active-active infrastructure across multiple regions for a workload with modest recovery requirements can permanently double compute and replication overhead even when failover events are rare.

| Recommendation                                                               | Sustainability benefit                                                  |
| ---------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Align resiliency architecture to defined business continuity requirements.   | Prevents unnecessary always-on infrastructure and idle redundancy.      |
| Evaluate whether multi-region deployments are truly required.                | Reduces continuous replication traffic and duplicated infrastructure.   |
| Assess monolith versus microservice tradeoffs carefully.                     | Avoids unnecessary service-to-service traffic and platform duplication. |
| Right-size failover environments instead of mirroring peak production scale. | Minimizes standby infrastructure while maintaining recovery objectives. |
| Regularly review backup, replication, and retention strategies.              | Reduces excessive data duplication and long-term storage growth.        |

## Stabilize demand to reduce waste

Consider a video processing platform that may receive large upload spikes during specific hours of the day. Without workload shaping, the platform rapidly scales compute resources to absorb bursts, leaving significant infrastructure idle once demand subsides. 

Sudden demand spikes and burst-driven scaling patterns can trigger rapid infrastructure expansion followed by prolonged periods of underutilized capacity. This volatility reduces overall platform efficiency and increases unnecessary resource consumption.
 
Strive for stable utilization design patterns improve both cloud efficiency and workload sustainability. In the video processing example, asynchronous processing and queue-based buffering, can make uploads be processed more steadily while maintaining acceptable completion times.

| Recommendation                                                              | Sustainability benefit                                                    |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| Use asynchronous processing for burst-prone operations.                     | Smooths workload demand and reduces short-lived scale spikes.             |
| Implement buffering and queue-based workload management.                    | Stabilizes infrastructure utilization over time.                          |
| Batch non-urgent operations where appropriate.                              | Improves compute efficiency by reducing repeated processing overhead.     |
| Tune autoscaling policies to avoid aggressive short-term scaling reactions. | Prevents unnecessary infrastructure expansion during temporary spikes.    |


## Factor in sustainability into platform choices

Sustainability improves when workload density is maximized on shared cloud infrastructure instead of maintaining unnecessary dedicated or idle capacity.

Platform choices affect how efficiently a workload uses cloud resources over time. Services that are oversized, rarely used, or difficult to manage often consume more energy than necessary and increase operational overhead.

Modern managed services can improve both reliability and sustainability. PaaS and serverless platforms make better use of shared cloud infrastructure and reduce the need to manage dedicated resources. They also benefit from ongoing platform optimizations that improve efficiency over time.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Platform decisions involve tradeoffs. A lower-carbon region might increase latency for users. Spot VMs can reduce infrastructure waste, but they can also introduce instability if the workload cannot tolerate interruptions. Autoscaling can improve utilization, but poorly tuned scaling policies may create unnecessary infrastructure churn.


| Recommendation                                                                                           | Sustainability benefit                                                                                                |
| -------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Evaluate managed PaaS and serverless services where appropriate.                                         | Improves infrastructure utilization through shared platform efficiency and reduces self-managed operational overhead. |
| Keep platform services, runtimes, and dependencies updated.                                              | Benefits from ongoing platform efficiency improvements, reliability enhancements, and better resource utilization.    |
| Match service tiers and scalability models to actual workload demand.                                    | Prevents unnecessary overprovisioning and reduces idle infrastructure consumption.                                    |
| Remove zombie workloads, orphaned resources, and inactive environments regularly.                        | Eliminates infrastructure that continues consuming resources without delivering business value.                       |
| Shut down non-production environments when not in use.                                                   | Reduces unnecessary compute consumption and idle platform capacity.                                                   |
| Avoid dedicated infrastructure allocations unless workload requirements justify them.                    | Maximizes utilization of shared hyperscale infrastructure and reduces permanently reserved capacity.                  |
| Tune autoscaling behavior to avoid aggressive short-term scaling reactions.                              | Stabilizes infrastructure utilization and reduces unnecessary scale churn.                                            |
| Use Spot VMs only for interrupt-tolerant workloads.                                                      | Improves infrastructure utilization without introducing avoidable reliability instability.                            |
| Choose regions close to users when latency affects workload reliability.                                 | Reduces network traversal overhead and improves request stability.                                                    |
| Evaluate carbon intensity, latency, and resiliency tradeoffs together when selecting deployment regions. | Balances sustainability goals with workload reliability and user experience requirements.                             |
                 |

## Run tests that provide meaningful resiliency validation

Workloads often behave differently under real-world stress such as traffic spikes, outages, and degraded dependencies. Load testing, performance testing, and chaos engineering help validate how a system responds under these conditions and expose reliability issues before they impact production.

However, these testing practices require significant compute resources. A key sustainability consideration is that reliability testing should be purposeful and proportionate, not continuous or excessive.

| Recommendation                                                                                                                                                             | Sustainability benefit                                                                                               |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Use load testing and chaos engineering to assess how the workload behaves under outages, traffic spikes, and traffic dips.                                                 | Improves service resilience and enables more efficient fault handling, reducing waste during real failure scenarios. |
| Use chaos engineering to simulate conditions that increase energy usage and identify inefficient execution paths.                                                          | Helps expose energy-inefficient behavior and supports optimization of resource consumption under stress.             |
| Define expected system behavior under these conditions, including how the workload should respond when operating inefficiently or under constraint.                        | Ensures predictable recovery and avoids prolonged inefficient operating states.                                      |
| Consider implementing an “eco version” of the application that reduces features or performance to minimize energy usage. Use it as a benchmark for sustainability scoring. | Provides a measurable baseline for reduced-energy operation and enables continuous sustainability optimization.      |
| Evaluate the tradeoff of running chaos engineering and load tests, since these activities themselves consume additional compute and increase emissions.                    | Prevents unnecessary test-related resource consumption while maintaining reliability validation.                     |
| Schedule and scope chaos engineering intentionally to balance resilience validation with the environmental cost of testing.                                                | Reduces avoidable emissions from excessive or poorly targeted testing.                                               |


## Related guidance

- [Well-Architected Framework - Reliability pillar](/azure/well-architected/reliability/)
