---
title: Well-Architected checklist for Reliability
description: Review a checklist for Reliability in application design. Considerations include uptime (availability), high resiliency, low latency, and cost.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---
# Recommendations for Reliability

This checklist presents a set of recommendations as a way for you to gauge reliability, resiliency, and failure recovery strategies in your architecture design. The goal of reliability is to ensure that you have identified the best infrastructure and application design for your workload based on business requirements that are mapped to availability and recoverability target metrics. Arriving at a reliable design requires a thorough consideration of many decision points and a holistic understanding of the impacts of the decisions you make throughout your design process, as detailed in this checklist and accompanying guides. Moreover, make workload reliability concerns a central consideration throughout the workload design, development, and ops lifecycle.

Approaching your design with a focus on reliability will help you ensure that you are designing a workload that is resilient, manageable, and repeatable. If you haven't checked the boxes and considered the tradeoffs, the design could be at risk. Careful consideration of all the points covered in the checklist will give you confidence in success.

|Code  |Recommendation  |
|---------|---------|
|RE: 01     |  **Identify user and system flows** of the workload and **prioritize using a criticality scale** that's derived from the business requirements.       |
|RE: 02     |  **Use Failure Mode Analysis (FMA) to identify and prioritize potential failures in your solution components.** FMA helps you assess the risk and impact of each failure mode and plan how the workload would respond and recover.       |
|RE: 03     |   **Define reliability and recovery targets** for the components, flows, and the overall solution. Visualize the targets to **negotiate, gain consensus, set expectations, and drive actions** to achieve the ideal state. Use the defined targets to build the health model. The health model defines what healthy, degraded, and unhealthy states look like.      |
|RE: 04     | **Add redundancy at different levels, especially for critical flows.** Apply redundancy to the compute, data, network and other infrastructure tiers in accordance with the identified reliability targets.        |
|RE: 05     | **Implement a timely & reliable scaling strategy at application, data, and infrastructure level.**        |
|RE: 06     |  **Strengthen the resiliency and recoverability of your workload by implementing self-preservation and self-healing measures.** Build capabilities in the solution using infrastructure-based reliability patterns and software-based design patterns to handle component failures and transient errors. And build capabilities in the system to detect solution component failures and automatically initiate corrective action while the workload continues to operate at full or reduced functionality.       |
|RE: 07      |  **Test for resiliency and availability scenarios by applying the principles of chaos engineering in your test and production environments.** Your testing should prove your graceful degradation implementation and scaling strategies through active malfunction and simulated load testing.       |
|RE: 08     |  **Have structured, tested, and documented business continuity and disaster recovery (BCDR) plans aligned with the negotiated recovery targets.** Plans must cover all components as well as the system as a whole.       |
|RE: 09     |  **Measure and publish the solution's health indicators.** Continuously capture uptime and other reliability data, from across the workload and more granularly from individual components and key flows.       |
|RE: 10     |  **Design your workload to align with business objectives and avoid unnecessary complexity or overhead.** Design decisions should be based on a practical and balanced approach that can deliver the desired results effectively, with a smaller surface area to reduce inefficiencies and potential problems.       |

