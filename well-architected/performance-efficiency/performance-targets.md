---
title: Recommendations for establishing and exposing performance targets
description: Learn how to drive continuous improvement by setting and exposing performance targets for your workload. Identify workload objectives, key metrics, and performance targets. Monitor performance and evaluate customer feedback to drive improvement.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for defining performance targets 

**Applies to: PE 01**

This guide describes the recommendations for establishing and exposing performance targets. Performance targets are metrics that define performance objectives. These metrics are expressed as a single numerical value or a numerical range. They're clear and specific metrics that drive continuous improvement. Performance targets are a numerical foundation for improvements. They help teams align their efforts toward specific goals.

Without clear performance targets, teams might lack focus, resulting in inefficiency and uncertainty. The absence of performance targets can also lead to a lack of accountability for performance issues. By setting performance targets, teams can work toward specific objectives and drive continuous improvement.

**Definitions**

|  Term|                  Definition|
|-|-|
  |Metrics|              Numerical values that are collected at regular intervals. Metrics describe some aspects of a system at a particular time.
  |Performance targets|   Metrics that define performance objectives. These metrics are expressed as a single numerical value or a numerical range.|
  |Flow|                  In a workload, a sequence of operations that performs a specific function. It involves the movement of data and the running of processes between components of the workload.|
  |User flow             |The sequence that a user follows to accomplish a task.|
|Workflow          |    The sequence of steps that a workload runs to accomplish a task.|
  |Data flow|             The movement of data within a workload.|
|Dependency            |A component that a workload relies on.|

## Key strategies

Establishing performance targets is an essential step for achieving workload performance efficiency. Performance targets define the desired level of performance for your workload and help you gauge its effectiveness in meeting those objectives. Performance targets provide a benchmark to measure and compare a workload's efficiency. This benchmark can help you highlight improvement areas. The targets also align tasks with your organization's objectives and enhance business outcomes. Additionally, performance targets offer guidance in resource allocation, helping you ensure that workloads can adapt to varying demands while maintaining optimal performance.

### Set workload performance targets

To establish performance targets, identify metrics that are critical for measuring the performance of your workload. Performance targets can help you monitor workload performance over time, spot trends, and make informed changes for better results.

**Identify workload objectives.** Determine the objectives for your workload and understand your performance goals. For example, you might aim to reduce response times, increase throughput rates, or optimize resource use. A best-in-class product likely has more ambitious performance targets than an average product. Performance targets should align with business goals.

**Identify key metrics.** Based on your workload objectives, identify the key metrics that help measure progress toward your goals. Consider metrics related to availability, capacity, and response time.

- *Availability* is measured by the error rate, which represents the percentage of successful requests. For example, a success rate of 99.9 percent of requests is a common target.

- *Capacity* is evaluated based on throughput, which refers to the ability to handle a specific number of transactions within a given time period. For instance, an application might need to sustain 100 million transactions per month.

- *Response time* is assessed by analyzing the response time of the application. A common target is a completion rate of 99 percent of sign-in requests in less than 1 second.

**Set specific targets.** After you identify the key metrics, specify targets or thresholds for each metric. Targets should be measurable, realistic, and aligned with your workload objectives. For example, you might set a target response time of less than 500 milliseconds (ms) or a target error rate of less than 1 percent. Avoid qualitative assessments of performance like *fast* or *slow*. By using numerical targets, you can objectively assess performance over time.

- *Consider the customer:* When you set performance targets, adopt a customer-centric perspective. Recognizing the customer as the ultimate judge of performance helps ensure that performance targets align with customer expectations. This alignment involves considering both organizational objectives and the distinct requirements of the customer base. When you integrate these two aspects, you can tailor performance targets to reflect the desired customer experience and overall workload effectiveness. By defining performance objectives that consider customer expectations, you can strive to provide a high-quality customer experience and meet the needs of your customers.

- *Use percentiles:* Percentiles, such as P99, P95, and P50, are the industry standard way to represent the result of performance assessments. Percentiles are statistical measures that indicate how much data the number includes. In the context of performance assessments, percentiles help quantify the distribution of response times or other performance metrics. The P99 represents the value that encompasses 99 percent of the data. The P95 represents 95 percent of the data, and P50 represents 50 percent of the data. 

   Use percentiles, rather than simple averages, to provide a more comprehensive understanding of system performance. Outliers heavily affect the average value. Percentiles give insights into the overall distribution and variability of performance metrics. You can then set more realistic and meaningful performance targets based on different percentiles, ensuring better customer experiences and system reliability. 

   To measure percentiles, collect performance data over a period of time, typically using monitoring tools or logging mechanisms. Then analyze this data to determine the response time values at different percentiles. By establishing performance targets based on specific percentiles, you can focus on optimizing critical flows to meet desired levels of customer experience and system efficiency.

**Set performance targets early.** Set performance targets before you deploy your workload. For a workload in a design, performance targets require research. Conduct market research, competitive analysis, and surveys to generate your performance target ranges. For a production workload that has no performance targets, use production data and customer feedback to establish performance targets.

### Set performance targets for critical flows

In addition to establishing targets for the whole workload, establish performance targets for critical flows. These flows are essential to the workload's objectives. Performance targets for critical flows help you focus on the most important aspects of your workload.

**Identify critical workload flows.** Determine the flows within your workload that are critical for the successful operation of your business. These flows can be customer interactions, transactional processes, or any other operations that are vital to business objectives.

Clearly state the beginning point and end point of the flow to make it measurable. By stating these points, you define the performance targets and you can effectively track and monitor the targets. For example, in a shopping cart transaction, you can define the precondition as *customer is logged in*. The starting point can be when the customer selects a button to add an item to the shopping cart. The ending point can be when the number on the shopping cart icon increases to reflect the added item. 

By clearly defining beginning and end points, you can track and measure the efficiency and effectiveness of the process. These definitions also provide a basis for analyzing and improving the process over time.

After you identify the critical flows, decompose each critical flow. Break down, or decompose, the critical flow into its architectural components. Then create relevant targets for each component. Example targets include API access time, database latency, and network latency.

Remember to factor in the customer and billing models in your workload. A workload can have different access tiers. Higher-paying customers should experience the best performance. For example, multitenant solutions require performance targets to be available at the tenant level. Paid versus free access tiers might have distinct performance expectations and should have distinct performance targets.

**Identify dependencies.** A critical flow can have upstream dependencies and downstream dependencies within the workload. Identify all upstream and downstream dependencies. Then identify the guarantees that the upstream dependencies provide for this flow. Also identify the guarantees that this flow provides to downstream dependencies. 

For example, a critical flow might need to have a 500-ms response time, and the customer flow has an external API call that takes 200 ms. The performance target for the customer flow should exclude the API call (200 ms) and should be within 300 ms. For the performance targets for the critical flow, exclude all upstream dependencies that lack performance guarantees. 

Sometimes dependencies aren't under your control. Invest time to understand the performance profile of those dependencies. Learn about the guarantees that the dependencies have on your critical flow. Factor these guarantees into your performance targets.

**Identify key metrics.** Determine the key metrics that help you measure the performance of the critical flows. Consider metrics that directly affect the efficiency, responsiveness, and reliability of the critical flows. For example, consider response time, error rates, transaction throughput, resource use, and other relevant measurements.

**Set targets.** Set performance goals or targets for each identified metric. These targets should align with your business objectives and reflect the desired level of performance for the critical flows. Consider factors such as acceptable response times, maximum error rates, and desired transaction throughput. For example, you might set a performance target for a checkout flow at 3 seconds and a throughput at 100 checkouts per minute. 

Focus on the important flows to improve efficiency and reduce noise. The goal for a flow should determine the performance targets.

### Monitor performance

Track the performance of the workload and critical flows against performance targets. Then adjust resources to drive peak performance.

**Use monitoring tools.** Use monitoring tools and services to collect data about the defined key performance indicators (KPIs). Monitoring tools can include application performance monitoring (APM) solutions, logging services, and metrics platforms. Use the collected data to analyze performance trends, identify bottlenecks, and gain insights into workload behavior. Data visualization tools can help you present this information in a clear and actionable format, enabling you to make informed decisions.

**Configure alerts.** Set up alerts based on the defined threshold values. When a KPI exceeds the specified thresholds, an alert is triggered, providing a real-time notification of potential performance issues. Alerts allow you to take immediate action to investigate and resolve any problems.

**Expose all defined targets.** All responsible teams should be able to review and create actionable tasks from the performance targets. Use information radiators, such as dashboards and reports, to make the performance targets accessible. If a workload has a performance-based service-level agreement (SLA) or service-level objective (SLO), communicate the SLA or SLO to the customer.

**Take action.** Use insights from monitoring to optimize performance. You might need to adjust resource allocation, optimize code, improve infrastructure, or scale resources based on workload demands.

### Evaluate customer feedback

When you engage with customers, gather feedback, and adapt performance targets, you can enhance the performance of your workload and deliver optimal customer experiences. To ensure performance targets are aligned with customer expectations, maintain a dialog with customers. Gather customer feedback to learn about evolving preferences, requirements, and technology trends. Regular communication with customers allows you to refine and adapt performance targets based on the changing needs of customers.

Incorporate a customer-centric perspective into the performance optimization strategy to create a foundation for a workload that resonates with customers. This approach not only ensures that the workload meets technical benchmarks but also establishes a dynamic feedback loop for continuous improvement based on customer needs. By prioritizing customer satisfaction and loyalty, you position the workload for sustained success.

Continual customer feedback helps you understand customer expectations so you can tailor performance targets and optimize the workload. The ongoing conversation ensures that the workload constantly evolves to meet customer needs and delivers an enhanced experience.

## Azure facilitation

**Set performance targets:** Azure Advisor provides [performance recommendations](/azure/advisor/advisor-performance-recommendations) that can inform your performance targets.

**Monitor:** [Azure Monitor](/azure/azure-monitor/overview) is a full-stack monitoring service that provides a complete set of features to monitor your Azure resources and measure performance targets. It collects platform metrics and provides ready-to-use dashboards. It allows you to configure alerts based on metrics. It also stores and correlates metrics to ensure a single source of truth.

## Related links

- [Azure Advisor performance recommendations](/azure/advisor/advisor-performance-recommendations)
- [Azure Monitor](/azure/azure-monitor/overview)

## Next steps

We recommend that you review the Performance Efficiency checklist to explore other concepts.

> [!div class="nextstepaction"] 
> [Performance Efficiency checklist](checklist.md) 