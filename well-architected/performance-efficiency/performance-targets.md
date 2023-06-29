---
title: Best practices for establishing and exposing performance targets
titleSuffix: Azure Design Review Framework
description: Examine a performance efficiency checklist to make sure your workload can scale. Review application design, data management, and implementation guidance.
author: dragon119
ms.author: martinek
ms.date: 12/08/2021
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-efficiency
ms.custom:
  - checklist
---

# Best practices for establishing and exposing performance targets

**Applies to: PE:01**

This guide describes the best practices for establishing and exposing performance targets. Performance targets provide specific performance goals for critical workload flows. They give you a measurable benchmark for evaluating workload performance over time. The benchmark serves as a common reference point for the teams responsible for the application. It creates a numerical foundation for making performance improvements. Without performance targets, teams have unclear expectations. Unclear expectations result in team inefficiency due to a lack of focus. It also causes a lack of accountability for performance issues. 

#### Terminology

|Term|Definition|
|---|---|
|Metrics|Metrics are numerical values that Azure collects at regular intervals and describe aspects of a system at a particular time.|
|Performance targets|Metrics that define performance objectives often expressed as a range.|
|Flow|A sequence of operations in a workload that performs a specific function.|
|User flow|The sequence a user follows to accomplish a task.|
|Workflow|The sequence of steps a workload executes to accomplish a task.|
|Data flow|The movement of data within a workload.|
|Dependency|A component that your workload relies on.|
|Heatlh model|Flow metrics that define healthy and unhealth performance.|


## What Azure gives you

Azure provides the ability to measure the performance of your workload at every layer. Azure Monitor is the primary service that collects, aggregates, and visualizes workload performance metrics.
Azure has several dependency mapping tools. Some are automated. Others gather components and require manual dependency mapping.

- Azure Monitor topology: You can view network dependencies with the topology feature in Azure Monitor. 
- Application map in Azure Application Insights: Application Insights has an application mapping feature where you can view the logical structure of a distributed application. 
- Azure Resource Explorer: Azure Resource Explorer lists every resource in your Azure Active Directory (Azure AD) tenant. It gives you visibility but doesn't indicate dependencies. You must map workload components and dependencies manually.
- Azure Resource Graph: Azure Resource Graph allows you to run queries against the resources in an Azure AD tenant. Resource Graph is accessible in the Azure portal and from the command line. You must map workload components and dependencies manually.


## Your responsibility

##### Performance targets

- Create performance targets for critical workload flows. Critical flows perform essential functions for your organization or business. You should establish performance targets before you deploy and continuously evaluate in operations. Focus on the important flows to improve efficiency and reduce noise. 

##### Critical flows

- Understand each critical flow. Critical flows include user flows, workflows, and data flows. combine multiple subprocesses. One business transaction can require many different technical metrics. You need to break down (decompose) the critical flow into its components and create targets for each component. Example targets include API access time, database latency, network latency. Identify workload dependencies. 

- Define the goal for each critical flow. The goal for a flow should determine the performance targets. A best-in-class product should have more ambitious performance targets than an average product. 

- Define performance target ranges for each critical flow. Performance targets create clear objectives to measure workload performance. The goal is to avoid qualitative assessments of performance like fast or slow. With numerical targets, you can conduct objective assessments of workload performance over time. You should conduct market research, competitive analysis, and employee surveys to generate your performance target ranges. 

##### Health model

- Create a health model. You should use the performance targets to create the health model for each flow, subprocess, and flow components. 

- Expose health targets. The performance targets should be the basis for health models and alerts. Development and operations teams should be able to review and create actionable tasks from the performance targets. You should use dashboards and reports to make the performance targets accessible. In multi-tenant solutions, it’s important to make performance targets available to each tenant.

## Tradeoff

In general, ambitious performance targets require a more expensive solution than less ambitious targets. The recommendation is to focus on critical workload flows. Knowing your critical flows allows you to invest in those flows and less in non-critical flows. Targeted investments of time and technology help optimize costs within a workload. 

