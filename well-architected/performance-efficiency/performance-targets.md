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

This guide describes the recommendations for establishing and exposing performance targets. Performance targets are metrics that define performance objectives expressed as a single numerical value or a numerical range. They provide clear and specific metrics to drive continuous improvement. Performance targets serve as a numerical foundation for making improvements and help teams align their efforts towards specific goals . Without clear targets, there may be a lack of focus, resulting in inefficiency and uncertainty . Additionally, the absence of performance targets can lead to a lack of accountability for performance issues . By setting performance targets, teams can work towards specific objectives and drive continuous improvement.

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

## Key strategies

Establishing performance targets is an essential step for achieving workload performance efficiency. Performance targets define the desired level of performance for your workload and help you gauge its effectiveness in meeting those objectives. Performance targets provide a clear benchmark to measure and compare workload's efficiency, aiding in highlighting improvement areas. They also align tasks with an organization's objectives and enhance business outcomes. Additionally, these targets offer guidance in resource allocation, ensuring workloads can adapt to varying demands while maintaining optimal performance.

### Establish workload performance targets

Establishing performance targets involves identifying specific metrics that are critical for measuring the performance of your workload. These metrics can include factors such as response times, throughput rates, resource utilization, and other relevant measurements. Performance targets give you a clear way to check if your workload is performing well and help you monitor performance over time, spot trends, and make informed changes for better results.

**Identify workload objectives.** Determine the specific objectives for your workload and understand performance goals you want to achieve. For example, you might aim to reduce response times, increase throughput rates, or optimize resource utilization. A best-in-class product will likely have more ambitious performance targets than an average product. Performance targets should align with business goals.

**Identify key metrics.** Based on your workload objectives, identify the key metrics that will help you measure progress towards those goals. Consider metrics related to performance, efficiency, scalability, and user experience. For example, response time, concurrent users, error rates, or resource consumption.

**Set specific targets.** Once you have identified the key metrics, set specific targets or thresholds for each one. These targets should be measurable, realistic, and aligned with your workload objectives. For example, a target response time of under 500 milliseconds or a target error rate of less than 1%. Avoid qualitative assessments of performance like "fast" or "slow." With numerical targets, you can conduct objective performance assessments over time. The industry standard is to use percentiles to represent the result of performance assessments (P99, P95, P50).

Percentiles are statistical measures that indicate the value below which a certain percentage of data falls. In the context of performance assessments, percentiles help quantify the distribution of response times or other performance metrics. The P99 represents the value below which 99% of the data falls, P95 represents the value below which 95% of the data falls, and P50 represents the median or the value below which 50% of the data falls. Using percentiles as performance indicators provides a more comprehensive understanding of system performance compared to simple averages. Average values can be heavily influenced by outliers or sporadic peaks, whereas percentiles give insights into the overall distribution and variability of performance metrics. This allows organizations to set more realistic and meaningful performance targets based on different percentiles, ensuring better user experiences and system reliability. To measure percentiles, you need to collect performance data over a period of time, typically using monitoring tools or logging mechanisms. This data can then be analyzed to determine the response time values at different percentiles. By establishing performance targets based on specific percentiles, you can focus on optimizing critical flows to meet desired levels of user experience and system efficiency.

**Establish performance targets early.** You should have performance targets before you deploy your workload. For a workload in a design, establishing performance targets requires research. You should conduct market research, competitive analysis, and surveys to generate your performance target ranges. A production workload without performance targets should use production data and user feedback to establish performance targets.

### Establish performance targets for critical flows

Establishing performance targets for critical flows helps you focus on the most important aspects of your workload. Setting performance targets for critical workload flows involves identifying specific metrics that are crucial for measuring the performance of those specific flows. These metrics can include factors such as response times, throughput rates, resource utilization, and other relevant measurements.

**Identify critical workload flows.** Determine the specific flows within your workload that are critical for the successful operation of your business. These can be user interactions, transactional processes, or any other operations that are vital for achieving your business objectives.

It's important to clearly state the beginning and end of the flow to make it measurable. This ensures you clearly define the performance targets and can be effectively tracked and monitored. For example, in the case of a shopping cart transaction, the preconditions can be defined as "user is logged in." The starting point can be when the user clicks a button to add an item to the shopping cart, and the ending point can be when the number on the shopping cart icon is increased to reflect the added item. By clearly defining these points, you can track and measure the efficiency and effectiveness of the process. It also provides a basis for analyzing and improving the process over time.

When you have the critical flows, you should decompose each critical flow. Break down (decompose) the critical flow into its architectural components and create relevant targets for each component. Example targets include API access time, database latency, and network latency.

Remember to factor in the user and billing models in your workload. Workloads can have different access tiers. Higher-paying customers should receive the best performance. For example, multi-tenant solutions require performance targets to be available at the tenant level. Paid versus free access tiers may have distinct performance expectations and should have distinct performance targets.

**Identify dependencies.** A critical flow can have upstream and downstream dependencies within the workload. You need to identify all upstream and downstream dependencies. Then you need to understand the guarantees the upstream dependencies provide for this flow. You also need to understand the guarantees this flow provides to downstream dependencies. For example, a critical flow might need to have a 500ms response time, and the user flow has an external API call that takes 200ms. The performance target for the user flow should exclude the API call (200ms) and should target 300ms. You should separate all upstream dependencies without performance guarantees from the performance targets for the critical flow. Sometimes dependencies are not under your control. You need to invest time to understand the performance profile of that dependencies. You also need to understand the guarantees that dependency has on your critical flow. You must factor these guarantees into your performance targets.

**Identify key metrics.** Based on the identified critical flows, identify the key metrics that will help you measure the performance of these flows. Consider metrics that directly impact the efficiency, responsiveness, and reliability of the critical flows. For example, response time, error rates, or transaction throughput.

**Set targets.** Set specific performance goals or targets for each identified metric. These targets should be aligned with your business objectives and reflect the desired level of performance for the critical flows. Consider factors such as acceptable response times, maximum error rates, or desired transaction throughput. For example, you might set a performance target for a checkout flow at 3 seconds and throughput at 100 checkouts per minute. You should focus on the important flows to improve efficiency and reduce noise. The goal for a flow should determine the performance targets.

### Monitoring

Monitoring performance targets allows you to track the performance of the workload and critical flows against set performance targets and adjust resources to peak performance.

**Use monitoring tools.** You should use monitoring tools and services to collect data on the defined KPIs. These tools can include application performance monitoring (APM) solutions, logging services, and metrics platforms. Use the collected data to analyze performance trends, identify bottlenecks, and gain insights into workload behavior. Data visualization tools can help present this information in a clear and actionable format, enabling you to make informed decisions.

**Configure alerts.** Set up alerts based on the defined threshold values. When a KPI exceeds the specified thresholds, an alert will be triggered, providing real-time notification of potential performance issues. This allows you to take immediate action to investigate and resolve any problems.

**Expose all defined targets.** All responsible teams should be able to review and create actionable tasks from the performance targets. You should use information radiators, such as dashboards and reports, to make the performance targets accessible. Workloads that have a performance-based SLA or SLO should make the SLA or SLO known to the end user.

**Take action.** Use the insights gained from monitoring to optimize performance. This may involve adjusting resource allocation, optimizing code, improving infrastructure, or scaling resources based on workload demands.

## Azure facilitation

- **Setting performance targets:** Azure Advisor provides [performance recommendations](/azure/advisor/advisor-performance-recommendations) that could inform your performance targets.

- **Monitoring:** [Azure Monitor](/azure/azure-monitor/overview) is a full-stack monitoring service that provides a complete set of features to monitor your Azure resources and measure performance targets. It collects platform metrics and provides turn-key dashboards. It allows you to configure alerts based on metrics. It also stores and correlates metrics to ensure a single source of truth.

## Related links
