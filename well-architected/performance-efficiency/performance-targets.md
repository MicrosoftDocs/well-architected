---
title: Best practices for establishing and exposing performance targets
description: Examine a performance efficiency checklist to make sure your workload can scale. Review application design, data management, and implementation guidance.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
ms.custom:
  - checklist
---

# Recommendations for defining performance targets: PE 01

This guide describes the best practices for establishing and exposing performance targets. Performance targets provide specific performance goals for critical workload flows. These specific targets serve as a common reference point for the teams responsible for the application. It creates a numerical foundation for making performance improvements. Without performance targets, teams have unclear expectations. Unclear expectations result in team inefficiency due to a lack of focus. It also causes a lack of accountability for performance issues.

## Definitions

|  Term|                  Definition|
|-|-|
  |Metrics|               Metrics are numerical values that Azure collects at regular intervals and describe aspects of a system at a particular time.
  |Performance targets|   Metrics that define performance objectives expressed as a single numerical value or a numerical range.|
  |Flow|                  A sequence of operations in a workload that performs a specific function.|
  |User flow             |The sequence a user follows to accomplish a task.|
|Workflow          |    The sequence of steps a workload executes to accomplish a task.|
  |Data flow|             The movement of data within a workload.|
|Dependency            |A component that your workload relies on.|
  |Health model         | Flow metrics that define healthy and unhealthy performance|

## Key design strategy

### Performance targets

**Understand performance targets.** Performance targets should be relevant to the objectives of the workload. You need to understand organizational and user requirements as you define performance objectives. From these requirements, you can derive performance targets. For example, a business expects a workload to generate \$1M in the fiscal year. This income requires 100M transactions per month. The performance targets should align with these business targets. A best-in-class product will likely have more ambitious performance targets than an average product.

**Establish performance targets early.** You should have performance targets before you deploy your workload. You should also continuously evaluate your performance targets as part of your commitment to continuous performance optimization. You should conduct market research, competitive analysis, and surveys to generate your performance target ranges.

**Use numerical targets.** Empirical performance targets create clear objectives to measure workload performance. The goal is to avoid qualitative assessments of performance like fast or slow. With numerical targets, you can conduct objective performance assessments over time. The industry standard is to use percentiles to represent the result of performance assessments (P99, P95, P50).

### Critical flows 

**Decompose each critical flow.** One business transaction can require many different metrics across various technologies. You need to break down (decompose) the critical flow into its architectural components and create relevant targets for each component. Example targets include API access time, database latency, and network latency.

**Identify dependencies for critical flow.** A critical flow can have upstream and downstream dependencies within the workload. You need to identify all upstream and downstream dependencies. Then you need to understand the guarantees the upstream dependencies provide for this flow. You also need to understand the guarantees this flow provides to downstream dependencies. For example, a critical flow might need to have a 400ms response time. The user flow has an external API call that takes 200ms. The performance target excluding the API call should be 200ms. You should separate all upstream dependencies without performance guarantees from the performance targets for the critical flow. Sometimes dependencies are not under your control. You need to invest time to understand the performance profile of that dependencies. You also need to understand the guarantees that dependency has on your critical flow. You must factor these guarantees into your performance targets.

**Define performance targets for critical flows.** Critical flows perform essential functions for your organization or business and include user flows, data flows, and workflows. For example, the checkout flow for an ecommerce website might have a performance target of 3 seconds for checkout and throughput that can handle 100 checkouts per minute. You should focus on the important flows to improve efficiency and reduce noise. The goal for a flow should determine the performance targets.

### Monitoring 

**Align with health model and alerting strategy.** You should include performance in your workload's health model and alerting strategy. You need to use the same performance targets as part of the health model for each flow, subprocess, and flow components. You should also use the performance targets to build incremental thresholds in your alerting.

**Expose all defined targets.** All responsible teams should be able to review and create actionable tasks from the performance targets. You should use information radiators, such as dashboards and reports, to make the performance targets accessible. Workloads that have a performance-based SLA or SLO should make the SLA or SLO known to the end user.

**Factor in user/billing models.** Workloads can have different access tiers. Higher-paying customers should receive the best performance. For example, multi-tenant solutions require performance targets to be available at the tenant level. Paid versus free access tiers may have distinct performance expectations and should have distinct performance targets.

## Azure facilitation

-   [Azure Monitor](/azure/azure-monitor/overview) is a full-stack monitoring service that provides a complete set of features to monitor your Azure resources and generate performance targets. Azure Monitor collects platform metrics and provides turn-key dashboards. It allows you to configure alerts based on metrics. It also stores and correlates metrics to ensure a single source of truth.

-   Azure Advisor provides [performance recommendations](/azure/advisor/advisor-performance-recommendations) that could inform your performance targets.

## Tradeoffs

In general, ambitious performance targets require a more expensive solution than less ambitious targets. The recommendation is to focus on critical workload flows. Knowing your critical flows allows you to invest in those flows and less in non-critical flows. Targeted investments of time and technology help optimize costs within a workload. For example, a critical flow in an ordering system is more important than the performance of an internal alerting system.

## Related links
