---
title: Design review checklist for Performance Efficiency
description: Use this checklist to assess and verify the completeness of your design for performance efficiency.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Design review checklist for Performance Efficiency

This checklist presents a set of recommendations for your system to scale its ability to grow to meet workload usage demand. The goal of **performance** is that every valid interaction to a healthy system continues to be efficient even as demand increases. This must be designed and implemented with focus on **efficiency and effectiveness** in cost, complexity, supporting new requirements, technical debt, reporting, and toil factored in.

Every system has a limit on how much it can be scaled without redesigning, introducing a workaround, or having human involvement. If you haven't checked the boxes and considered the tradeoffs, the design could be at risk. Careful consideration of all the points covered in the checklist will give you confidence in success.

||  Code|        Recommendation|
|-|-|-|
| :::image type="icon" source="../_images/checkbox-icon.png":::|[PE:&nbsp;01](performance-targets.md)| **Define performance targets.** Performance targets are numerical values that derive from your requirements. Performance targets should exist for all workload flows.|
| :::image type="icon" source="../_images/checkbox-icon.png":::|[PE: 02](plan-capacity.md)|      **Conduct formal capacity planning.** Capacity planning should occur before predicted changes in usage patterns such as seasonal variations, product updates, marketing campaigns, special events, and regulatory changes.|
| :::image type="icon" source="../_images/checkbox-icon.png":::|  [PE: 03](select-right-services.md)|      **Select the right infrastructure, resources, and SKUs.** The selections must support your ability to reach the workload's performance targets and accommodate expected capacity changes. The selections should also weigh the benefits of using platform features or building a custom implementation.|
| :::image type="icon" source="../_images/checkbox-icon.png":::|  [PE: 04](collect-metrics-logs.md) |     **Collect performance metrics and logs.** Data collection should be on workload components and flows. It should be automatic, continuous, meaningful, and analyzable. The data should support the workload health model.|
| :::image type="icon" source="../_images/checkbox-icon.png":::|  [PE: 05](scale-partition.md)  |    **Implement reliable scaling and partitioning**. Scaling and partitioning should be reliable and controlled. The scale unit design of the workload should be the basis of the scaling and partitioning strategy.|
| :::image type="icon" source="../_images/checkbox-icon.png":::  |[PE: 06](test.md)     | **Test the workload.** Testing should be regular and performed in an environment that matches the production environment. Results should be compared against performance targets and the performance benchmark.|
| :::image type="icon" source="../_images/checkbox-icon.png":::|[PE: 07](optimize-code-resources.md)|      **Optimize code and resource use.** Code should be performant, and it should offload responsibilities to the platform. Code and infrastructure should only be used for their core purpose and only when necessary.|
| :::image type="icon" source="../_images/checkbox-icon.png":::|  [PE: 08](optimize-data-performance.md)|      **Optimize data usage.** All data stores, partitions, and indexes should be optimized for their intended and actual use in the workload.|
| :::image type="icon" source="../_images/checkbox-icon.png":::|  [PE: 09](prioritize-flows-users.md)|      **Prioritize the performance of critical flows and critical users.** The allocation of workload resources and performance optimization efforts should prioritize the critical flows and users.|
| :::image type="icon" source="../_images/checkbox-icon.png":::|  [PE: 10](minimize-operations-effects.md)|      **Minimize the effects of operations tasks.** The performance effects of the software development lifecycle and other routine operations should be known and minimized, such as virus scans, secret rotation, backups, reindexing database, and deployments.|
| :::image type="icon" source="../_images/checkbox-icon.png":::|  [PE: 11](respond-live-performance-issues.md)|      **Respond to live-site performance issues.** Addressing performance issues should be planned with clear lines of communication and responsibilities. Learnings should identify preventive measures to incorporate in the workload and ways to return to normal operations faster in similar incidents.|
| :::image type="icon" source="../_images/checkbox-icon.png":::|  [PE: 12](continuous-performance-optimize.md)|      **Continuously optimize performance.** The focus should be on components whose performance deteriorates over time, such as databases and networking features.|

## Next steps 

> [!div class="nextstepaction"]
> [Tradeoffs for performance efficiency](tradeoffs.md)
