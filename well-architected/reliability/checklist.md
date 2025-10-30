---
title: Design review checklist for Reliability
description: Use this checklist for Reliability to identify the best infrastructure and application design for your workload.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: concept-article
ms.update-cycle: 1095-days
---

# Design review checklist for Reliability

This checklist presents a set of recommendations for you to use to evaluate the reliability, resiliency, and failure recovery strategies in your architecture design. To ensure reliability, identify the best infrastructure and application design for your workload. Make these decisions based on your business requirements that are mapped to availability and recoverability target metrics.

To implement a reliable design, thoroughly consider decision points in your design and be aware of how those decisions affect your workload. This checklist and the accompanying guides provide resources to help you make those decisions. Make workload reliability a central consideration throughout the workload design, development, and operation lifecycle.

## Checklist

Approach your design with a focus on reliability to help ensure that you design a workload that's resilient, manageable, and repeatable. If you don't include reliability practices and consider the tradeoffs, your design is potentially at risk. Carefully consider all the points covered in the checklist to instill confidence in your system's success.

|&nbsp; |Code  |Recommendation  |
|-|-|-|
| &#9744; |[RE:01](simplify.md)     |  **Focus your workload design on simplicity and efficiency.** Use a practical approach to avoid unnecessary complexity while meeting your business goals and requirements. |
| &#9744; |[RE:02](identify-flows.md)    |  **Identify and rate user and system flows.** Use a criticality scale based on your business requirements to prioritize the flows. |
| &#9744; |[RE:03](failure-mode-analysis.md)     |  **Use failure mode analysis (FMA) to identify potential failures in your workload.** Identify dependencies and failure points, and develop mitigation strategies for those failures.|
| &#9744; |[RE:04](metrics.md)     |   **Define reliability and recovery targets** for your workload. Use the targets to inform your design and as the foundation of your health model.|
| &#9744; |[RE:05](redundancy.md)   | **Add redundancy at different levels, especially for critical flows**, to help meet your reliability targets. Consider redundant infrastructure components such as compute and network, and multiple instances of your solution. |
| &#9744; |[RE:06](scaling.md)    | **Implement a timely and reliable scaling strategy at the application, data, and infrastructure levels**. Base the scaling strategy on actual or predicted usage patterns and minimize manual intervention.|
| &#9744; |[RE:07](self-preservation.md)        |  **Strengthen the resiliency of your workload by implementing self-preservation and self-healing measures**. Use built-in features and well-established cloud patterns to help your workload remain functional during and recover from incidents.   |
| &#9744; |[RE:08](testing-strategy.md)      |  **Test for resiliency and availability scenarios by applying the principles of chaos engineering**. Ensure that your graceful degradation implementation and scaling strategies are effective by performing active malfunction and simulated load testing.       |
| &#9744; |[RE:09](disaster-recovery.md)     |  **Implement structured, tested, and documented business continuity and disaster recovery (BCDR) plans** that align with the recovery targets. Plans must cover all components and the system as a whole.       |
| &#9744; |[RE:10](monitoring-alerting-strategy.md)     |  **Measure and model the solution's health signals**. Continuously capture uptime and other reliability data from across the workload and also from individual components and key flows.       |

## Next steps

We recommend that you review the Reliability tradeoffs to explore other concepts.

> [!div class="nextstepaction"]
> [Reliability tradeoffs](tradeoffs.md)
