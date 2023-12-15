---
title: Design review checklist for Performance Efficiency
description: Use this checklist to assess and verify the completeness of your design for performance efficiency.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Design review checklist for Performance Efficiency

This checklist presents a set of recommendations for you to scale your system so it can grow and meet your workload usage demand. The goal of **performance** is to maintain the efficiency of every interaction with a healthy system as demand increases. When you design and implement for performance, focus on the **efficiency and effectiveness** of cost, complexity, supporting new requirements, technical debt, reporting, and toil.

For every system, there's a limit to how much you can scale it without redesigning, introducing a workaround, or incorporating human involvement. If you don't include performance efficiency practices and consider the tradeoffs, your design is potentially at risk. Carefully consider all the points covered in the checklist to instill confidence in your system's success.

## Checklist

|&nbsp; |Code  |Recommendation  |
|-|-|-|
| &#9744; | [PE:01](performance-targets.md) | **Define performance targets.** Performance targets should be numerical values that are tied to workload requirements. You should implement performance targets for all workload flows. |
| &#9744; | [PE:02](capacity-planning.md) | **Conduct capacity planning.** Capacity planning should be done before there are predicted changes in usage patterns, such as seasonal variations, product updates, marketing campaigns, special events, or regulatory changes. |
| &#9744; | [PE:03](select-services.md) | **Select the right services.** The services, infrastructure, and tier selections must support your ability to reach the workload's performance targets and accommodate expected capacity changes. The selections should also weigh the benefits of using platform features or building a custom implementation.|
| &#9744; | [PE:04](collect-performance-data.md) | **Collect performance data.** Workload components and flows should provide automatic, continuous, and meaningful metrics and logs. Collect data at different levels of the workload, such as the application, platform, data, and operating system levels. |
| &#9744; | [PE:05](scale-partition.md) | **Optimize scaling and partitioning.** Incorporate reliable and controlled scaling and partitioning. The scale unit design of the workload is the basis of the scaling and partitioning strategy. |
| &#9744; | [PE:06](performance-test.md) | **Test performance.** Perform regular testing in an environment that matches the production environment. Compare results against the performance targets and the performance benchmark.|
| &#9744; |[PE:07](optimize-code-infrastructure.md) | **Optimize code and infrastructure.** Use code that's performant, and ensure that it offloads responsibilities to the platform. Use code and infrastructure only for their core purpose and only when necessary. |
| &#9744; | [PE:08](optimize-data-performance.md)| **Optimize data usage.** Optimize data stores, partitions, and indexes for their intended and actual use in the workload.|
| &#9744; | [PE:09](prioritize-critical-flows.md)| **Prioritize the performance of critical flows.** The allocation of workload resources and performance optimization efforts should prioritize the flows that support the most important business processes, users, and operations.|
| &#9744; | [PE:10](optimize-operational-tasks.md)| **Optimize operational tasks.** Monitor and minimize the effects of the software development lifecycle and other routine operations on workload performance. These operations include virus scans, secret rotations, backups, reindexing databases, and deployments. |
| &#9744;| [PE:11](respond-live-performance-issues.md)| **Respond to live performance issues.** Plan how to address performance problems by incorporating clear lines of communication and responsibilities. When a problematic situation occurs, use what you learn to identify preventive measures and incorporate them in your workload. Implement methods to return to normal operations faster when similar situations occur. |
| &#9744;| [PE:12](continuous-performance-optimize.md)| **Continuously optimize performance.** Focus on components that show deteriorating performance over time, such as databases and networking features. |

## Next steps

We recommend that you review the Performance Efficiency tradeoffs to explore other concepts.

> [!div class="nextstepaction"]
> [Performance Efficiency tradeoffs](tradeoffs.md)
