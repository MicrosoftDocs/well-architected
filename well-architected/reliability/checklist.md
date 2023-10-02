---
title: Design review checklist for Reliability
description: Use this checklist for Reliability to identify the best infrastructure and application design for your workload.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---
# Design review checklist for Reliability

This checklist presents a set of recommendations for you to use to evaluate the reliability, resiliency, and failure recovery strategies in your architecture design. To ensure reliability, identify the best infrastructure and application design for your workload. Make these decisions based on your business requirements that are mapped to availability and recoverability target metrics.

To implement a reliable design, thoroughly consider decision points in your design and be aware of how those decisions affect your workload. This checklist and the accompanying guides provide resources to help you make those decisions. Make workload reliability a central consideration throughout the workload design, development, and operation lifecycle.

## Checklist

Approach your design with a focus on reliability to help ensure that you design a workload that's resilient, manageable, and repeatable. If you don't include reliability practices and consider the tradeoffs, your design is potentially at risk. Carefully consider all the points covered in the checklist to instill confidence in your system's success.

|&nbsp;|Code  |Recommendation  |
|-|-|-|
| &#9744; |[RE:&nbsp;01](identify-flows.md)    |  **Identify the user and system flows** of your workload. **Prioritize importance by using a criticality scale** that's based on your business requirements.       |
| &#9744; |[RE:&nbsp;02](failure-mode-analysis.md)     |  **Use failure mode analysis (FMA) to identify and prioritize potential failures in your solution components**. Perform FMA to help you assess the risk and effect of each failure mode. Determine how the workload responds and recovers.       |
| &#9744; |[RE:&nbsp;03](metrics.md)     |   **Define reliability and recovery targets** for the components, the flows, and the overall solution. Visualize the targets to **negotiate, gain consensus, set expectations, and drive actions** to achieve the ideal state. Use the defined targets to build the health model. The health model defines what healthy, degraded, and unhealthy states look like.      |
| &#9744; |[RE:&nbsp;04](redundancy.md) <br> [RE:&nbsp;04](highly-available-multi-region-design.md) <br> [RE:&nbsp;04](regions-availability-zones.md)    | **Add redundancy at different levels, especially for critical flows**. Apply redundancy to the compute, data, network, and other infrastructure tiers in accordance with the identified reliability targets.        |
| &#9744; |[RE:&nbsp;05](partition-data.md) <br> [RE:&nbsp;05](scaling.md)    | **Implement a timely and reliable scaling strategy at the application, data, and infrastructure levels**.        |
| &#9744; |[RE:&nbsp;06](background-jobs.md) <br> [RE:&nbsp;06](self-preservation.md) <br> [RE:&nbsp;06](handle-transient-faults.md)  |  **Strengthen the resiliency and recoverability of your workload by implementing self-preservation and self-healing measures**. Build capabilities into the solution by using infrastructure-based reliability patterns and software-based design patterns to handle component failures and transient errors. Build capabilities into the system to detect solution component failures and automatically initiate corrective action while the workload continues to operate at full or reduced functionality.       |
| &#9744; |[RE:&nbsp;07](testing-strategy.md)      |  **Test for resiliency and availability scenarios by applying the principles of chaos engineering in your test and production environments**. Use testing to ensure that your graceful degradation implementation and scaling strategies are effective by performing active malfunction and simulated load testing.       |
| &#9744; |[RE:&nbsp;08](disaster-recovery.md)     |  **Implement structured, tested, and documented business continuity and disaster recovery (BCDR) plans that align with the recovery targets**. Plans must cover all components and the system as a whole.       |
| &#9744; |[RE:&nbsp;09](monitoring-alerting-strategy.md)     |  **Measure and publish the solution's health indicators**. Continuously capture uptime and other reliability data from across the workload and also from individual components and key flows.       |
| &#9744; |[RE:&nbsp;10](simplify.md)     |  **Design your workload to align with business objectives and avoid unnecessary complexity or overhead**. Use a practical and balanced approach to make design decisions that deliver the desired results. Contain your design to the necessities to reduce inefficiencies and potential problems.       |

## Next steps

> [!div class="nextstepaction"]
> [Tradeoffs for Reliability](tradeoffs.md)
