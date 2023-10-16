---
title: Recommendations for establishing and exposing performance targets
description: Learn how to drive continuous improvement by setting and exposing performance targets for your workload. Identify workload objectives, key metrics, and performance targets. Monitor performance and evaluate customer feedback to drive improvement.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for defining performance targets 

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:** 

|[PE:01](checklist.md)| Define performance targets. Performance targets should be numerical values that are tied to workload requirements. You should implement performance targets for all workload flows.| 
|---|---| 

This guide describes the recommendations for establishing and exposing performance targets. Performance targets are metrics that define performance objectives. These metrics are expressed as a single numerical value or a numerical range. They're clear and specific metrics that drive continuous improvement. Performance targets are a numerical foundation for improvements. They help teams align their efforts toward specific goals.

Without clear performance targets, teams might lack focus, resulting in inefficiency and uncertainty. The absence of performance targets can also lead to a lack of accountability for performance issues. By setting performance targets, teams can work toward specific objectives and drive continuous improvement.

**Definitions**

|  Term|                  Definition|
|-|-|
|Data flow|             The movement of data within a workload.|
|Dependency            |A component that a workload relies on.|
|Flow|                  In a workload, a sequence of operations that performs a specific function. It involves the movement of data and the running of processes between components of the workload.|
|Metrics|              Numerical values that are collected at regular intervals. Metrics describe some aspects of a system at a particular time.
|Performance targets|   Metrics that define performance objectives. These metrics are expressed as a single numerical value or a numerical range.|
|User flow             |The sequence that a user follows to accomplish a task.|
|Workflow          |    The sequence of steps that a workload runs to accomplish a task.|


## Key strategies

Establishing performance targets is an essential step for achieving workload performance efficiency. Performance targets define the desired level of performance for your workload and help you gauge its effectiveness in meeting those objectives. Performance targets provide a benchmark to measure and compare a workload's efficiency. This benchmark can help you highlight improvement areas. The targets also align tasks with your organization's objectives and enhance business outcomes. Additionally, performance targets offer guidance in resource allocation, helping you ensure that workloads can adapt to varying demands while maintaining optimal performance.

### Set performance targets early

Set performance targets before you deploy your workload. For a workload in a design, performance targets require research. Conduct market research, competitive analysis, and surveys to generate your performance target ranges. For a production workload that has no performance targets, use production data and customer feedback to establish performance targets.

### Understand workload requirements

Understanding workload requirements means being aware of essential performance metrics like response time, throughput, and latency that are critical for your application. Aligning these performance targets with your organization's business goals ensures the workload meets the desired standards, whether for a best-in-class or average product. For example, you might aim to reduce response times, increase throughput rates, or optimize resource use. 

When setting performance goals, it's important to align the organization's objectives with the distinct needs of the user base. Users ultimately determine the success of performance, emphasizing the need to align performance targets with their expectations. This balance ensures that performance targets capture the intended user experience and the overall efficiency of the workload. To comprehensively gauge and optimize workload performance, you should consider setting performance targets for the following list:

- *Individual components*: These are the separate units or segments of the system, each potentially having distinct performance attributes and demands.

- *User flows*: These pathways chart how users maneuver through the system, and ensuring their fluidity directly enhances user experience.

- *Workflows*: These defined internal processes are crafted to achieve particular results and often dictate operational efficiency.

- *Data flows*: By tracking the movement and interaction of data within the system, potential inefficiencies or bottlenecks can be identified.

- *External dependencies*: These are elements outside the primary system, like integrated third-party services or tools, that can significantly impact performance.

- *Scale units*: These relate to the system's scalable segments. Ensuring robust performance under increased loads is pivotal, especially in growth scenarios.

- *Technology levels*: These are direct performance indicators such as the speed of API access, database operation latencies, and potential network delays.

- *Business transactions*: Representing end-to-end user operations like completing a purchase or booking a service, their seamless execution is directly tied to user satisfaction.

- *Workload all up*: This holistic metric gives an overview of the collective performance encompassing all components and aspects of the workload.

### Identify key metrics

Based on your workload objectives, identify the key metrics that help measure progress toward your goals. Consider metrics related to availability, capacity, and response time.

- *Availability*: Avaliability is measured by the error rate, which represents the percentage of successful requests. A common target for availability is a success rate of 99.9 percent of requests.

- *Capacity*: Capacity is evaluated based on throughput, which refers to the ability to handle a specific number of transactions within a given time period. For instance, an application might need to sustain 100 million transactions per month.

- *Response time:* Response time is assessed by analyzing the time it takes for the application to respond to a request. A common target is a completion rate of 99 percent of sign-in requests in less than 1 second.

### Set specific targets 

After you identify the key metrics, you need to specify targets or thresholds for each metric. Targets should be measurable, realistic, and aligned with your workload objectives. For example, you might set a target response time of less than 500 milliseconds (ms) or a target error rate of less than 1 percent. Avoid qualitative assessments of performance like *fast* or *slow*. By using numerical targets, you can objectively assess performance over time. 

- *Consider the customer*: When you set performance targets, adopt a customer-centric perspective. Recognizing the customer as the ultimate judge of performance helps ensure that performance targets align with customer expectations. This alignment involves considering both organizational objectives and the distinct requirements of the customer base. When you integrate these two aspects, you can tailor performance targets to reflect the desired customer experience and overall workload effectiveness. By defining performance objectives that consider customer expectations, you can strive to provide a high-quality customer experience and meet the needs of your customers.

- *Use percentiles*: Percentiles, such as P99, P95, and P50, are the industry standard to represent the result of performance assessments. Percentiles are measures that indicate how much data the number includes. For example, P99 covers 99% of the data. Use percentiles, rather than simple averages, to provide a more comprehensive understanding of workload performance. To measure percentiles, collect performance data over a period of time, typically using monitoring tools or logging mechanisms. Then analyze this data to determine the response time values at different percentiles.

### Faciliate collaboration

Meeting performance targets is a shared responsibility between development and operations teams. To ensure that the workload consistently meets or exceeds these targets, it's crucial to provide teams with the tools and processes that simplify the review and action cycle. To facilitate collaboration, consider these recommendations: 

- *Document performance targets*: Document all performance targets. Ensure that all performance targets are documented in a centralized location, easily accessible by both development and operations teams. It promotes alignment and aids in real-time decision-making.
  
- *Expose performance targets*: All responsible teams should be able to review and create actionable tasks from the performance targets. Use information radiators, such as dashboards and reports, to make the performance targets accessible.

- *Make it actionable*: The documenation and information radiators should suggest clear next steps. For example, a rise in errors might prompt an immediate check, or meeting a target consistently might suggest a reevaluation of that benchmark.

### Evaluate customer feedback

When you engage with customers, gather feedback, and adapt performance targets, you can enhance the performance of your workload and deliver optimal customer experiences. To ensure performance targets are aligned with customer expectations, maintain a dialog with customers. Gather customer feedback to learn about evolving preferences, requirements, and technology trends. Regular communication with customers allows you to refine and adapt performance targets based on the changing needs of customers.

Incorporate a customer-centric perspective into the performance optimization strategy to create a foundation for a workload that resonates with customers. This approach not only ensures that the workload meets technical benchmarks but also establishes a dynamic feedback loop for continuous improvement based on customer needs. By prioritizing customer satisfaction and loyalty, you position the workload for sustained success.

Continual customer feedback helps you understand customer expectations so you can tailor performance targets and optimize the workload. The ongoing conversation ensures that the workload constantly evolves to meet customer needs and delivers an enhanced experience.

## Azure facilitation

**Set performance targets.** Azure Advisor provides [performance recommendations](/azure/advisor/advisor-performance-recommendations) that can inform your performance targets.

**Monitor.** [Azure Monitor](/azure/azure-monitor/overview) is a full-stack monitoring service that provides a complete set of features to monitor your Azure resources and measure performance targets. It collects platform metrics and provides ready-to-use dashboards. It allows you to configure alerts based on metrics. It also stores and correlates metrics to ensure a single source of truth.

## Related links

- [Azure Advisor performance recommendations](/azure/advisor/advisor-performance-recommendations)
- [Azure Monitor](/azure/azure-monitor/overview)

## Performance Efficiency checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Performance Efficiency checklist](checklist.md) 
