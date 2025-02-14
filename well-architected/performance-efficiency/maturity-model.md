---
title: Performance Efficiency Maturity Model
description: Understand the maturity model levels of the performance efficiency pillar.
author: claytonsiemens77
ms.author: csiemens
ms.date: 1/22/2025  
ms.topic: conceptual
---

# Performance Efficiency maturity model

Performance Efficiency is about maintaining user experience even when there's an increase in load by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

[add art]

:::image type="content" source="../_images/reliability.svg" alt-text="Example alt-text."::: 

# [**Level 1 - Establish baseline performance targets**](#tab/level1)

![Goal icon](../_images/goal.svg) **Choose workload components with just the right performance capabilities**

Level 1 of the maturity model focuses on gathering performance expectations and choosing cloud services that help you meet those expectations. At this level, focus on investigating your options for resources and components that will help you meet the performance expectations for the workload as a whole. Prioritize services that give you just the right performance capabilities. This approach can help you keep your costs under control and maintain your development velocity.

#### &#10003; Gather requirements and define workload performance targets

Work with stakeholders to understand general expectations for the workload performance. These might be targets related to page loading times for web apps or response times for an interactive system. At this stage of your workload development, these targets shouldn't be considered hard requirements as you aren't focusing on measuring performance metrics. Once you've gathered the workload expectations, you can start investigating the types of resources that may be good candidates for your workload.

#### &#10003; Choose appropriate networking resources

Assess your network needs to determine appropriate services and configurations. Consider network traffic, bandwidth, latency, and throughput to ensure the network supports your workload effectively. Use private virtual networks and backbone networks to reduce latency.

Ensure even distribution of network traffic to prevent server overload and reduce response times. Assess different [load balancing services](/azure/architecture/guide/technology-choices/load-balancing-overview) offered by your cloud provider. Consider traffic type, global or regional routing, service-level objectives (SLOs), and specific features like site acceleration and low-latency load balancing.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Be sure that you take time to fully investigate and understand the [different options for the foundational networking](/azure/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology), as changing this at a later time might require a full redesign and redeployment. 

#### &#10003; Choose appropriate compute resources 

Assess your workload's compute needs, including instance type, scalability, and service tiers. Consider containerization for performance gains that might be realized through isolation, resource efficiency, fast startup time, and portability.

Choose a [compute service](/azure/architecture/guide/technology-choices/compute-decision-tree) that can meet your needs, while allowing you to easily scale as your workload evolves. Remember that building your workload is an iterative process and you can start small, using less performant SKUs and fewer instances than you might need later in the workload's lifecycle.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Weigh your immediate needs against your budget. Look for opportunities to shutdown or deallocate compute resources when they're not in use.


#### &#10003; Choose appropriate data store services 

Determine your workload's needs for storing, retrieving, and managing data. Consider characteristics like:

- *Data types:* What types of data will your workload ingest, process, or store?
- *Volume:* How much data do you expect to ingest, process, or store?
- *Transaction thoughput:* What are the performance requirements for accessing different data types?
- *Consistency:* What are your targets for data consistency across your data types?
- *Durability:* What are your targets for data durability across your data types?
- *Access patterns:* What types of access patterns does your workload need to support? For example, for a particular component you may need to perform lots of writes, but few reads. For another component, you may need to do the opposite.

Based on the answers to these questions, you can [choose the best data service](/azure/architecture/guide/technology-choices/data-options) for each of your workload's use cases. 

**Because of the wide variety of options for data services in cloud environments, you can tailor your design to use different services to best match the functionality of each component in your workload. This approach helps you optimize the performance of each component.**


> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Don't over-engineer your data components by choosing different data services for components that could be consolidated into a single data store. Look to strike a balance between performance vs cost and complexity. 


# [**Level 2 - Baseline performance measurements** ](#tab/level2)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

![Goal icon](../_images/goal.svg) **Begin measuring performance using defined metrics and plan for optimization**

In level 1 of the Performance Efficiency pillar, the focus is on understanding the performance expectations for the workload and choosing infrastructure services that can help you meet those expectations. Level 2 focuses on decomposing your workload into flows and components and setting measurable targets and metrics that you can capture and analyze for future optimizations. You should also investigate design patterns that may help you enhance your performance efficiency and perform an initial capacity planning exercise. These activities will help you build a plan for the near-term future of your workload, ensuring that performance doesn't suffer as the workload evolves.

#### &#10003; Rank and prioritize flows from a performance perspective

Map and understand every user path and component communication within your workload to find performance bottlenecks and opportunities for optimization. Evaluate each flow's alignment with business objectives, user impact. Analyze performance data to identify patterns and anomalies.  Categorize flows as High, Medium, or Low based on criteria like:

- *Frequency:* How often the flow is performed, like searching for a product.
- *Criticality:* How essential the flow is to the overall success of the application, like a user profile lookup.
- *Risk:* How risky the flow is to overall performance, like generating a complex report.
- *Data intensiveness:* How much pressure the flow puts on the data layer.
- *Architecture intensiveness:* How many components the flow touches across the workload.

#### &#10003; Establish key metrics for your workload resources and components that help you meet your targets

Set performance targets for your workload components and resources based on market research, competitive analysis, and surveys. Focus on key metrics like response time, throughput, and latency. Establish targets for different components, user flows, workflows, data flows, external dependencies, and overall workload performance.

Define realistic and measurable targets for each metric, considering customer expectations and using percentiles (for example, P99, P95, and P50) for a comprehensive view. Perform testing to establish baseline performance, but don't overemphasize optimization.

#### &#10003; Perform your intial capacity planning exercise

Capacity planning is an iterative practice that should be regularly performed throughout your workload's lifecycle. At this stage, you might not be fully familiar with all of the technologies in use, or you may still be testing different 
options, so your ability to thoroughly plan for future needs might be limited. For this initial capacity planning exercise, the goal should be to evolve from reactive capacity management (adding resources to meet immediate demand) to proactive planning (anticipating what capacity you'll need for the next X amount of time). To do this, collect and analyze data on resource usage, including historical patterns for any existing workloads. Use statistical analysis, trend analysis, and predictive modeling to forecast future needs. Ensure these forecasts align with workload objectives.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: In a traditional, datacenter-based environment, over-provisioning is a common approach for capacity planning. In a cloud environment, over-provisioning can be a waste of money and you should take careful consideration of your business expectations to develop a timeline for adding capacity in a way that will meet your performance needs without negatively affecting your budget.

#### &#10003; Investigate design patterns to support performance efficiency

There are many common application design patterns that can help you optimize your workload for performance. You might achieve performance gains by adding a cache or by developing a sharding strategy. Review the [design patterns](./design-patterns.md) guide for a comprehensive list of patterns that might help you as you enhance your workload.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Some design patterns may add a degree of complexity to your workload, so weigh the additional management burden against your efficiency gains to determine whether a particular pattern is worth implementing.

#### &#10003; Collect and monitor performance data for resources across your workload

Collect application performance data like throughput, latency, and completion times to identify bottlenecks and improve user experience. Use distributed tracing and structured logging for easier analysis. Gather metrics and logs for all resources, and use tools like Azure Monitor Insights for performance monitoring. Collect database and storage data, and gather performance metrics for virtual machines. Store all of the collected data in one place for easy access and analysis.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Be sure to setup log rotation and retention policies as the amount of data that you collect and store can grow quickly and costs can increase unexpectedly.



# [Level 3](#tab/level3)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Risk mitigation

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

# [Level 4](#tab/level4)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Operations

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

# [Level 5](#tab/level5)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: 

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

---

## Next steps
<!-- Provide at least one next step and no more than three. Include some 
context so the customer can determine why they would click the link.
-->
