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

# [**Level 1: Establish baseline performance targets**](#tab/level1)

![Goal icon](../_images/goal.svg) **Choose workload components that provide only the necessary performance capabilities.**

Level 1 of the maturity model focuses on gathering performance expectations and choosing cloud services that help you meet those expectations. At this level, you investigate those resources and components to find the best fit. Prioritize services that provide only the necessary performance capabilities. This approach helps control costs and maintain your development speed.

#### &#10003; Gather requirements and define workload performance targets

Work with stakeholders to understand general expectations for workload performance. These expectations might include targets for page loading times for web apps or response times for interactive systems. At this stage of workload development, treat these targets as guidelines rather than hard requirements because your focus isn't on measuring performance metrics yet. After you gather workload expectations, begin investigating the types of resources that might suit your workload.

#### &#10003; Choose appropriate networking resources

Assess your network needs to determine appropriate services and configurations for your workload. Consider network traffic, bandwidth, latency, and throughput to ensure that the network supports your workload effectively. Use private virtual networks and backbone networks to reduce latency.

Ensure even distribution of network traffic to prevent server overload and reduce response times. Assess different [load balancing services](/azure/architecture/guide/technology-choices/load-balancing-overview) that your cloud provider offers. Consider traffic type, global or regional routing, service-level objectives, and specific features like site acceleration and low-latency load balancing.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Take time to fully investigate and understand the [different options for foundational networking](/azure/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology). Later changes in this area might require a full redesign and redeployment.

#### &#10003; Choose appropriate compute resources 

Assess your workload's compute needs, including instance type, scalability, and service tiers. Consider containerization to achieve performance gains through isolation, resource efficiency, fast startup times, and portability.

Choose a [compute service](/azure/architecture/guide/technology-choices/compute-decision-tree) that can meet your needs, while allowing you to easily scale as your workload evolves. Building your workload is an iterative process. You can start small by using less-performant SKUs and fewer instances. Upgrade these components later in the workload's lifecycle.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Weigh your immediate needs against your budget. Look for opportunities to shut down or deallocate compute resources when they're not in use.

#### &#10003; Choose appropriate data store services 

Determine your workload's needs for storing, retrieving, and managing data. Consider characteristics like:

- *Data types:* What types of data do you expect your workload to ingest, process, or store?

- *Volume:* How much data do you expect to ingest, process, or store?
- *Transaction throughput:* What are the performance requirements for accessing different data types?
- *Consistency:* What are your targets for data consistency across data types?
- *Durability:* What are your targets for data durability across data types?
- *Access patterns:* What types of access patterns does your workload need to support? For example, for a particular component you might need to perform several writes, but few reads. For another component, you might need to do the opposite.

Based on the answers to these questions, [choose the best data service](/azure/architecture/guide/technology-choices/data-options) for each of your workload's use cases. 

**Because of the wide variety of options for data services in cloud environments, you can tailor your design to use different services to best match the functionality of each component in your workload. This approach helps you optimize the performance of each component.**

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Don't over-engineer your data components by choosing different data services for components that could be consolidated into a single data store. Look to strike a balance between performance vs cost and complexity.

# [**Level 2: Baseline performance measurements**](#tab/level2)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

![Goal icon](../_images/goal.svg) **Begin measuring performance by using defined metrics and plan for optimization.**

Level 2 of the Performance Efficiency pillar focuses on using information about your workload to inform your performance optimizations. The first step is to identify crucial workload flows from a performance perspective. Next, break down your workload into its components and set measurable targets and metrics that you can capture and analyze for future optimizations. Finally, you should investigate design patterns that might help you enhance your performance efficiency and perform an initial capacity planning exercise. These activities will help you build a plan for the near-term future of your workload. This approach ensures that performance doesn't suffer as the workload evolves.

#### &#10003; Identify crucial flows from a performance perspective

Ranking and categorizing your workload flows is an important strategy in each of the Well-Architected Framework pillars. Analyzing flows from the perspective of each pillar helps you identify areas that need to be optimized. You can compare those optimizations to help you determine where to invest. For example, in some scenarios, security concerns for a flow might outweigh performance concerns. After you categorize and rank the flows, focus on the flows that you've identified as crucial to start your optimization planning. Performance-specific criteria that can help you identify crucial flows include:

- *Frequency:* The number of times that the flow is performed, like searching for a product.

- *Criticality:* The degree of importance that the flow has to the overall success of the application, like a user profile lookup.

- *Risk:* The level of risk that the flow has on overall performance, like generating a complex report.

- *Data intensiveness:* The amount of pressure that the flow puts on the data layer.

- *Architecture intensiveness:* The extent to which the flow interacts with components across the workload.

#### &#10003; Establish key metrics for your workload resources and components that help you meet your targets

Set performance targets for your workload components and resources based on market research, competitive analysis, and surveys. Focus on key metrics like response time, throughput, and latency. Establish targets for different components, user flows, workflows, data flows, external dependencies, and overall workload performance.

Set realistic and measurable targets for each metric while keeping customer expectations in mind. Use percentiles like P99, P95, and P50 to gain a comprehensive perspective. Perform testing to establish baseline performance, but don't overemphasize optimization.

#### &#10003; Perform your initial capacity planning exercise

Capacity planning is an iterative process that should be conducted regularly throughout your workload's life cycle. At this stage, you might not yet be fully familiar with all the technologies in use or might still be evaluating different options. This uncertainty can limit your ability to plan comprehensively for future needs. For this initial capacity planning exercise, the goal is to shift from reactive capacity management, which includes adding resources to meet immediate demand. Instead, the focus is on proactive planning, where you anticipate the capacity required for a defined period. To achieve effective capacity planning, collect and analyze data on resource usage, including historical patterns for existing workloads. Use statistical analysis, trend analysis, and predictive modeling to forecast future needs. Ensure that these forecasts align with workload objectives.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: In a traditional, datacenter-based environment, over-provisioning is a common approach for capacity planning. In a cloud environment, over-provisioning can be a waste of money. Carefully consider your business expectations to develop a timeline for adding capacity in a way that will meet your performance needs without negatively affecting your budget.

#### &#10003; Investigate design patterns to support performance efficiency

There are many common application design patterns that can help you optimize your workload for performance. You might achieve performance gains by adding a cache or developing a sharding strategy. Review the [design patterns](./design-patterns.md) guide for a comprehensive list of patterns that might help you as you enhance your workload.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Some design patterns might add a degree of complexity to your workload. Compare the extra management burden against your efficiency gains to determine whether a specific pattern is worth implementing.

#### &#10003; Optimize code performance

Optimizing your code makes your entire workload more efficient. Application tasks run quicker and use less compute resources, so you can maximize the performance of your infrastructure. Consider the following code optimization approaches.

- Instrument your code. Instrumenting your code helps you identify issues in your code performance by capturing telemetry during runtime. This helps you identify and resolve issues early in the development cycle.
- Identify hot paths. Instrumenting your code also helps identify hot paths, which are critical or frequently executed sections of a program that require high performance and low latency.
- Optimize code logic. Look for opportunities to simplify your code logic to make it more efficient. Remove unnecessary unnecessary function calls and data processing operations and minimize logging operations, network requests, and memory allocations, and look for opportunitiies to use more performant SDKs and libraries.
- Use concurrency and parallelism. Using concurrency and parallelism can make your application more efficient by executing tasks more efficiently. Concurrency involves managing multiple tasks by interleaving them, while parallelism involves executing multiple tasks simultaneously.

#### &#10003; Collect and monitor performance data for resources across your workload

Collect application performance data like throughput, latency, and completion times to identify bottlenecks and improve user experience. Use distributed tracing and structured logging for easier analysis. Gather metrics and logs for all resources. Use tools like Azure Monitor Insights for performance monitoring. Collect database and storage data, and gather performance metrics for virtual machines. Store all of the collected data in one place for easy access and analysis.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Ensure that you set up log rotation and retention policies because the amount of data that you collect and store can grow quickly and costs can increase unexpectedly.

# [**Level 3 - Incorporating performance signals**](#tab/level3)

![Goal icon](../_images/goal.svg) **Increase performance efficiency through internal and external feedback**

Level 3 of the Performance Efficiency pillar focuses on incorporating internal and external signals to refine performance targets, workload design and configurations, and related operational practices. In earlier maturity levels, you might have set performance targets and configurations based on development velocity needs, and internal testing. As you evolve your workload, taking internal and external user and stakeholder feedback into account, helps you ensure that there are realistic performance targets for your production workload, and that it meets those targets without sacrificing requirements for other pillars.

#### &#10003; Implement robust performance monitoring and alerting to detect performance issues

Moving to production means that you must be prepared to respond immediately to performance issues. Robust performance monitoring and useful, actionable alerting will help ensure that the right teams are notified of issues and can begin their investigation and troubleshooting activities quickly. Strategies to help you define your performance issue response activities include:

- Configure performance alerting in your monitoring platform.
- Tune your alerts to provide actionable warning that the component is at risk of a performance issue. This could be setting a threshold at a static value, or it could be based on trend change for a given metric. Determine the trigger value based on continuous testing to ensure that you don't experience false positives.
- Analyze logs and performance metrics to identify the underlying causes of issues.
- Keep stakeholders informed about the status and resolution progress of performance issues.
- Establish a framework to prioritize performance issues based on their impact.
- Document all steps, processes, and best practices for responding to performance issues.

As part of your performance monitoring strategy, ensure that you specifically monitor the performance of your flows. Understand each flow's impact on workload performance. For the flows, identify potential issues through user behavior insights. Track response times, errors, and other metrics to optimize critical flows. Analyze logs to trace flow performance and identify issues.

#### &#10003; Continuously evaluate and adjust performance targets based on feedback

Regularly review internal and external user feedback on performance and evaluate whether your targets meet expectations. Explore using customer satisfaction polling, comment systems, and targeted user testing to gather useful feedback.

When practical for your workload, consider using a Real User Monitoring (RUM) approach. This approach can help you determine whether the user experience is meeting your expectations for performance. [Azure Monitor Application Insights](/azure/azure-monitor/app/javascript-sdk) and [Azure Traffic Manager](/azure/azure-monitor/app/javascript-sdk) offer functionality for capturing RUM data for websites.

#### &#10003; Intelligently perform performance analysis and capacity planning

Track your app's performance using metrics that match your goals and targets. Compare the metrics against what user and business stakeholder feedback to ensure you're collecting the right data. Keep business-related metrics separate from performance data for easier analysis, even if some overlap exists. This helps everyone understand the data and make smart decisions.

Analyzing trends also helps you plan for how much capacity you'll need. Update your predictions to match the workload goals and user demand, so you always have enough resources. Regularly review your cloud providers SLAs to ensure that you'll be able to add resources as needed without running into service limits.

As you evolve your capacity planning, work closely with business decision-makers to stay aligned with business goals.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Weigh performance capacity planning against your reliability and budget requirements. Engage with stakeholders to find compromises between these considerations when there are conflicting requirements.

#### &#10003; Optimize your scaling strategy

Employ advanced scaling techniques to optimize your resource utilization. Adjust the thresholds used for scaling operations based on internal and external feedback, and build automation to perform scaling operations for components that lack native automatic scaling functionality. Used scheduled scaling for components that are lightly used or idle at known times throughout the day, week, or month. Continously evaluate scaling configurations and make refinements to better meet the fluctuating demands of a workload.

#### &#10003; Optimize data management 
Inefficiencies in data management can cause performance issues across your workload. Use these strategies to optimize your data estate.

- Divide large datasets or workloads into smaller parts called partitions for separate storage or processing. This enables parallel processing, reduces contention, and improves resource use and processing times. It also distributes data across multiple storage devices, reducing individual load and enhancing overall performance. Align partitioning types with use cases: partitioning can be horizontal, vertical, or functional. Review the [Performance Efficiency design patterns](./design-patterns.md) to learn about design patterns that use partitioning.
- Optimize queries using tooling like [Query Performance Insight for Azure SQL Database](/azure/azure-sql/database/query-performance-insight-use).
- Ensure your data model suits your workload, considering normalization, indexing, and partitioning.
- Align storage infrastructure with workload requirements, optimizing settings like buffer size and caching.

#### &#10003; Conduct performance tests and compare against a baseline

Design performance tests that can help you understand how your workload will perform in different production scenarios. Employ a variety of tests like load, stress, soak, spike, and compatibility, based on criteria and metrics to understand different dimensions of your workload performance. Metrics should include performance aspects like response time, throughput, and memory and CPU utilization. Define acceptance criteria that aligns with targets. Review the [performance testing](./performance-test.md) article for detailed guidance.

Performance testing should be performed in a dedicated environment as part of your overall testing strategy. This includes reliability and security testing.

Review test results to identify bottlenecks and other inefficiencies and compare results against goals, predefined criteria, or previous runs.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Testing environments can be significant cost drivers. Design test environments with the minimum required resources to emulate the specific scenario being tested and ensure that resources are shutdown or deleted after testing to minimize utilization costs.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Ensure that production data is cleansed of sensitive data before copying it into test environments if you need to use production data. Use synthetic data when practical.

Another critical testing function is to build your performance baseline. Baselines provide reference points for comparing performance over time. Identify performance metrics and use these as baseline metrics.  Evaluate future performance against these baselines to identify improvements or degradation. Regularly review and update baselines to capture updated design elements and features. Outdated baselines can lead to unrealistic and unachievable targets as the workload matures and new features are added.

#### &#10003; Adopt a business-driven mindset to drive performance efficiency

Foster an environment of continuous improvement where teams learn from production and listen to internal and external feedback. Equip workload teams with the necessary skills and mindset to optimize performance and handle demand fluctuations. Allocate time for monitoring and addressing performance issues. Set clear expectations with visible performance targets, baselines, and acceptable deviation thresholds.


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
