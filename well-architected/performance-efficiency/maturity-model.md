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

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Don't overengineer your data components by choosing different data services for components that you can consolidate into a single data store. Strike a balance between performance versus cost and complexity.

# [**Level 2 - Baseline performance measurements** ](#tab/level2)

![Goal icon](../_images/goal.svg) **Begin measuring performance by using defined metrics and plan for optimization.**

Level 2 of the Performance Efficiency pillar focuses on using information about your workload to inform your performance optimizations. The first step is to identify critical workload flows from a performance perspective. Next, break down your workload into its components and set measurable targets and metrics that you can capture and analyze for future optimizations. Finally, you should investigate design patterns that might help you enhance your performance efficiency and perform an initial capacity planning exercise. These activities help you build a plan for the immediate future of your workload. This approach ensures that performance doesn't suffer as the workload evolves.

#### &#10003; Identify critical flows from a performance perspective

Ranking and categorizing your workload flows is an important strategy in each of the Well-Architected Framework pillars. Analyzing flows from the perspective of each pillar helps you identify areas that need to be optimized. You can compare those optimizations to help you determine where to invest. For example, in some scenarios, security concerns for a flow might outweigh performance concerns. After you categorize and rank the flows, focus on the flows that you identify as critical to start your optimization planning. Performance-specific criteria that can help you identify critical flows include:

- *Frequency:* The number of times that the flow is performed, like searching for a product.

- *Criticality:* The degree of importance that the flow has to the overall success of the application, like a user profile lookup.

- *Risk:* The level of risk that the flow has on overall performance, like generating a complex report.

- *Data intensiveness:* The amount of pressure that the flow puts on the data layer.

- *Architecture intensiveness:* The extent to which the flow interacts with components across the workload.

#### &#10003; Establish key metrics for your workload resources and components that help you meet your targets

Set performance targets for your workload components and resources based on market research, competitive analysis, and surveys. Focus on key metrics like response time, throughput, and latency. Establish targets for different components, user flows, workflows, dataflows, external dependencies, and overall workload performance.

Set realistic and measurable targets for each metric while keeping customer expectations in mind. Use percentiles like P99, P95, and P50 to gain a comprehensive perspective. Perform testing to establish baseline performance, but don't overemphasize optimization.

Record all performance targets in a centralized location accessible to both development and operations teams. Use dashboards and reports to make targets visible and actionable. 

#### &#10003; Perform your initial capacity planning exercise

Capacity planning is an iterative process that should be conducted regularly throughout your workload's life cycle. At this stage, you might not yet be fully familiar with all the technologies in use or might still be evaluating different options. This uncertainty can limit your ability to plan comprehensively for future needs. For this initial capacity planning exercise, the goal is to shift from reactive capacity management, which includes adding resources to meet immediate demand. Instead, the focus is on proactive planning, where you anticipate the capacity required for a defined period. To achieve effective capacity planning, collect and analyze data on resource usage, including historical patterns for existing workloads. Use statistical analysis, trend analysis, and predictive modeling to forecast future needs. Ensure that these forecasts align with workload objectives.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** In a traditional, datacenter-based environment, over-provisioning is a common approach for capacity planning. In a cloud environment, over-provisioning can be a waste of money. Carefully consider your business expectations to develop a timeline for adding capacity in a way that meets your performance needs without negatively affecting your budget.

#### &#10003; Investigate design patterns to support performance efficiency

There are many common application design patterns that can help you optimize your workload for performance. You might achieve performance gains by adding a cache or developing a sharding strategy. For a comprehensive list of patterns that might help you enhance your workload, see [Cloud design patterns](./design-patterns.md).

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Some design patterns might add a degree of complexity to your workload. Compare the extra management burden against your efficiency gains to determine whether a specific pattern is worth implementing.

#### &#10003; Optimize code performance

Code optimization makes your entire workload more efficient. Application tasks run quicker and use less compute resources, so you can maximize the performance of your infrastructure. Consider the following code optimization approaches:

- **Instrument your code.** Instrumenting your code helps you identify problems in your code performance by capturing telemetry during runtime. This process helps you identify and resolve problems early in the development cycle.

- **Identify hot paths.** Instrumenting your code helps you identify hot paths. Hot paths are essential or high-usage sections of a program that require high performance and low latency.

- **Optimize code logic.** Find ways to simplify your code logic for better efficiency. Remove unnecessary function calls and data processing operations. Minimize logging operations, network requests, and memory allocations. Look for opportunities to use more performant SDKs and libraries.

- **Use concurrency and parallelism.** Using concurrency and parallelism can improve your application's efficiency by managing multiple tasks effectively. Concurrency handles multiple tasks by switching between them, while parallelism processes multiple tasks simultaneously.

#### &#10003; Collect and monitor performance data for resources across your workload

Collect application performance data like throughput, latency, and completion times to identify bottlenecks and improve user experience. Use distributed tracing and structured logging for easier analysis. Gather metrics and logs for all resources. Use tools like Azure Monitor Insights for performance monitoring. Collect database and storage data, and gather performance metrics for virtual machines. Store all of the collected data in one place for easy access and analysis.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Ensure that you set up log rotation and retention policies because the amount of data that you collect and store can grow quickly and costs can increase unexpectedly.

# [**Level 3: Incorporating performance signals**](#tab/level3)

![Goal icon](../_images/goal.svg) **Increase performance efficiency through internal and external feedback.**

Level 3 of the Performance Efficiency pillar focuses on incorporating internal and external signals to refine performance targets, workload design and configurations, and related operational practices. In earlier maturity levels, you might have set performance targets and configurations based on development velocity needs and internal testing. As you evolve your workload, incorporating feedback from internal and external users and stakeholders helps ensure realistic performance targets for your production workload. This approach allows you to meet those targets without compromising requirements for other pillars.

#### &#10003; Implement robust performance monitoring and alerting to detect performance problems

Moving to production means that you must be prepared to respond immediately to performance problems. Robust performance monitoring and useful, actionable alerting help ensure that the right teams are notified of problems and can begin their investigation and troubleshooting activities quickly. The following strategies can help you define your performance problem response activities:

- Configure performance alerting in your monitoring platform.

- Tune your alerts to provide actionable warnings when a component is at risk of a performance problem. This approach can include setting a threshold at a static value or basing it on trend changes for a specific metric. Determine the trigger value through continuous testing to prevent false positives.

- Analyze logs and performance metrics to identify the underlying causes of problems.

- Keep stakeholders informed about the status and resolution progress of performance problems.

- Establish a framework to prioritize performance problems based on their impact.

- Document all steps, processes, and best practices for responding to performance problems.

As part of your performance monitoring strategy, ensure that you specifically monitor the performance of your flows. Understand the effect of each flow on workload performance. For the flows, identify potential problems through user behavior insights. Track response times, errors, and other metrics to optimize critical flows. Analyze logs to trace flow performance and identify problems.

#### &#10003; Continuously evaluate and adjust performance targets based on feedback

Regularly review internal and external user feedback on performance to assess whether your targets align with expectations. Consider using customer satisfaction polling, comment systems, and targeted user testing to gather useful feedback.

When practical for your workload, consider using a Real User Monitoring (RUM) approach. This approach can help you determine whether the user experience is meeting your expectations for performance. [Azure Monitor Application Insights](/azure/azure-monitor/app/javascript-sdk) and [Azure Traffic Manager](/azure/azure-monitor/app/javascript-sdk) provide functionality for capturing RUM data for websites.

#### &#10003; Intelligently perform performance analysis and capacity planning

Track your app's performance using metrics that align with your goals and targets. Compare these metrics with user and business stakeholder feedback to ensure that you collect the right data. Keep business-related metrics separate from performance data for easier analysis, even if some overlap exists. This approach makes the data clearer and helps teams make informed decisions.

Analyzing trends helps you plan for your capacity requirements. Update your predictions to match the workload goals and user demand so that you always have enough resources. Regularly review your cloud providers service-level agreements to ensure that you can add resources as needed without running into service limits.

As you evolve your capacity planning, work closely with business decision-makers to stay aligned with business goals.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Balance performance capacity planning with reliability and budget requirements. Work with stakeholders to find practical compromises when requirements conflict.

#### &#10003; Optimize your scaling strategy

Employ advanced scaling techniques to optimize your resource utilization. Adjust the thresholds used for scaling operations based on internal and external feedback. Build automation to perform scaling operations for components that lack native automatic scaling functionality. Use scheduled scaling for components that are lightly used or idle at predictable times throughout the day, week, or month. Continuously evaluate scaling configurations and make refinements to better meet the fluctuating demands of a workload.

#### &#10003; Optimize data management

Inefficiencies in data management can cause performance problems across your workload. Use the following strategies to optimize your data estate:

- Divide large datasets or workloads into smaller parts, called *partitions*, for separate storage or processing. This approach enables parallel processing, reduces contention, and improves resource use and processing times. It also distributes data across multiple storage devices, which reduces individual load and enhances overall performance. Align partitioning types with specific use cases. Partitioning can be horizontal, vertical, or functional, depending on the system's needs. For more information about design patterns that use partitioning, see [Performance Efficiency design patterns](./design-patterns.md).

- Optimize queries by using tooling like [Query Performance Insight for Azure SQL Database](/azure/azure-sql/database/query-performance-insight-use).

- Ensure that your data model is well-suited to your workload by considering factors such as normalization, indexing, and partitioning.

- Align storage infrastructure with workload requirements by optimizing settings such as buffer size and caching.

#### &#10003; Conduct performance tests and compare against a baseline

Design performance tests that can help you understand how your workload performs in different production scenarios. Employ a variety of tests such as load, stress, soak, spike, and compatibility based on criteria and metrics to measure how each test affects your workload performance. Metrics should include performance aspects like response time, throughput, and memory and CPU utilization. Define acceptance criteria that align with targets. For more information, see [Recommendations for performance testing](./performance-test.md).

Performance testing should be conducted in a dedicated environment as part of your overall testing strategy. This testing approach includes reliability and security testing.

Review test results to pinpoint bottlenecks and inefficiencies. Compare findings against goals, predefined criteria, or previous runs.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Testing environments can be major cost drivers. Design test environments with only the necessary resources to accurately emulate the specific scenario being tested. To minimize utilization costs, ensure that resources are shut down or deleted after testing.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** If you need to use production data, ensure that sensitive data is removed before you copy it into test environments. Use synthetic data when practical.

Another critical testing function is to build your performance baseline. Baselines provide reference points for comparing performance over time. Identify performance metrics and use them as baseline metrics. Evaluate future performance against these baselines to identify improvements or degradation. Regularly review and update baselines to capture updated design elements and features. Outdated baselines can result in unrealistic and unachievable targets as the workload matures and new features are added.

#### &#10003; Adopt a business-driven mindset to drive performance efficiency

Foster an environment of continuous improvement where teams learn from production and listen to internal and external feedback. Equip workload teams with the necessary skills and mindset to optimize performance and handle demand fluctuations. Allocate time for monitoring and addressing performance issues. Set clear expectations with visible performance targets, baselines, and acceptable deviation thresholds.

# [Level 4 - Learning from production experience](#tab/level4)

![Goal icon](../_images/goal.svg) **Applying learnings from operating in production to achieve efficiencies in performance**

The Level 4 stage of the maturity model assumes that your workload is in production and has been operating for enough time to gather useful information about its normal running state. At this level, you should use this information to make necessary updates and further improvements.

Use telemetry and feedback mechanisms to identify performance issues like bottlenecks and develop plans to make improvements. Use mature change management practices to ensure that you don't inadvertently cause additional issues when making changes to your environment. 

Keep in mind that iterative improvements will reach a point of diminishing returns. You'll need to decide when you've reached a "good enough" running state, when the cost and burden of developing fixes for inefficiencies won't outweigh the minor performance improvements you gain.

Also remember that any change you make to your environment to improve performance will directly impact other pillars of the Well-Architected Framework, with cost optimization being the most common trade-off. Carefully assess the impact to other pillars before making performance improvements to maintain the right balance for your requirements.

#### &#10003; Uplift your performance monitoring and testing

In Level 4, you should be monitoring and testing for performance in production. You should have well-developed test plans and scenarios and test data already in place. The goal of testing is to ensure that you're staying ahead of potential performance issues before they appear in production. So, monitoring and testing should be rigorous, standardized, and thoroughly documented. Consider the following recommendations.

- *Continuously revisit your baseline.* Apply learnings from your performance monitoring to ensure that your baseline reflects actual performance metrics. Your baseline is important to maintain because tests should be run against the baseline to ensure that deployments like feature updates don't negatively impact your performance.
- *Shift left in your testing.* Integrate testing earlier in your development cycle to catch potential issues before they appear in production.
- *Shift right in your testing.* Test in production. To ensure that testing is non-disruptive, use strategies like  blue-green deployments and A/B testing.
- *Use synthetic transactions in your testing.* Using synthetic transactions allows you to consistently test real-world user experience, helping you identify issues and potential improvements before users are affected.
- *Automate monitoring and testing.* Use industry-proven tools to automate monitoring and alerting against your baseline and for performance testing. Automation reduces manual steps and ensures consistency.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Testing in production can be complex and normally requires significant effort from DevOps teams. This can impact development velocity and other operational functions. Blue-green and A/B testing can also add costs to the workload by using duplicate resources when testing. Include these considerations in your budget and development planning.

#### &#10003; Implement advanced data management optimizations

In Level 4, you should have many data management optimization strategies in place already. Through your production experience you can make further optimizations to fine-tune your data management to ensure your workload performs efficiently as it continues to evolve.

- Use lossless and lossy data compression to reduce your data footprint. This helps save storage space and bandwidth usage and can lead to faster data transfers and access times.
- Develop and implement an archive and deletion policy for data that is rarely used or is no longer used. Moving data off to separate, less expensive storage or removing it altogether also helps save storage space and bandwidth usage.
- Fine-tune your caching and sharding strategies based on production learnings.
- Replicate data to regions close to your end users to reduce latency. Some database technologies, like Azure Cosmos DB, offer readable and writeable replicas across multiple regions, which further reduces latency when deploying your workload across regions. 
- Include storage performance monitoring in your workload monitoring solution. Performance issues can quickly appear when storage limits are breached. Many cloud storage services impose throttling when thresholds are crossed, causing bottlenecks that affect downstream application functionalities. Monitoring storage performance and watching for trends can help you proactively avoid limits.

#### &#10003; Optimize critical flows through isolation

When practical, isolate critical flows to ensure they aren't impacted by resource contention and to increase the ease of management. Strategies to consider include:

- Dedicate compute, storage, and networking resources to critical flows. 
- Use containerization approaches to isolate critical flows at the software (logical) level.
- Explicitly allocate capacity for CPU, memory, and disk I/O to critical flows to ensure they're properly provisioned.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Isolating flows through dedicated resources is a costlier approach than sharing resources across flows. Perform a cost-benefit analysis before implementing this approach to ensure that it's the best approach for your use case.

#### &#10003; Extend code optimizations derived from production learnings

Revisit the code optimizations that you made earlier in your workload development to find areas for further enhancements. For example, you should now have telemetry from production that can help you find inefficiencies like memory leaks. You can also confirm the hot paths that you've identified using production runtime data, or find unexpected hot paths. 

#### &#10003; Optimize operational tasks

Operational tasks like virus scans, secret rotations, backups, index optimization (reorganization or rebuilding), and deployments can all affect the performance of your workload, so optimizing their efficiency keeps your workload performant. Consider the following strategies for optimizing these types of tasks.

- Fine-tune operational tooling. Test and understand the performance impact of essential tools like file integrity monitoring and virus scanning, then fine-tune configurations for them. For example, create exclusion lists for virus scans to minimize their duration.
- Fine-tune database operations. Look for opportunities to fine-tune operations like database backups, schema changes, performance tuning, and monitoring. Use native tools like [automatic tuning in Azure SQL Database](/azure/azure-sql/database/automatic-tuning-enable?view=azuresql) to help this effort.

#### &#10003; Evaluate new platform features that enhance performance

Investigate and test new platform features that become available to determine if they can help you gain efficiencies. Consistently monitor feedback and performance metrics from these new additions to refine your approach.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Be aware of how your cloud platform packages capabilities into SKUs. Some SKUs that offer higher performance may be overprovisioned for your present use case but might save you time and effort in the future for migrating.
> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Adopting some types of services can mean migrating your application or data, which might mean there will be downtime for the migration. As part of your evaluation, determine whether you can tolerate the necessary downtime to successfuly migrate.


#### &#10003; Prioritize optimization efforts

Proactively optimizing performance means improving workload efficiency before issues arise by identifying bottlenecks and implementing optimizations. Based on analysis, make specific improvements through code changes, infrastructure adjustments, or configuration updates.

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
