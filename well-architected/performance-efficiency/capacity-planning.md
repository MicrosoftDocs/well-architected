---
title: Recommendations for capacity planning
description: Learn best practices for capacity planning.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for capacity planning: PE 02

This guide describes the best practices for capacity planning. You should perform formal capacity planning at key points in the lifecycle of the workload. Capacity planning enables you to anticipate short and long-term demand and manage workload performance effectively. By analyzing historical usage patterns and growth trends, you can identify potential bottlenecks, scalability challenges, or performance constraints.

**Definitions**

|  Term|                                Definition|
|-|-|
 |  Capacity planning |                  The process of predicting the resources a workload needs to meet its performance targets.|
|  Trend analysis                      |Historical data analysis to forecast future demand.|
| Functional requirements|             The features and capabilities a workload must have to fulfill its intended purpose.|
|  Technical requirements|              The code and infrastructure needed to meet the functional requirements.|

## Key design strategy

**Plan predictively.** Capacity planning is a predictive exercise. The goal is to help ensure a workload performance efficiently when demand grows and when it shrinks. Capacity planning should accommodate short-term and long-term changes due to known events. Periodic capacity planning helps realign predictions to new data.

For new workloads, capacity planning should take place before design. For existing workloads, capacity planning should take place before predicted changes in usage patterns. Changes in usage patterns can change resource needs and alter workload performance. Usage patterns can grow or shrink. They can be organic (more or less users) or inorganic (event or security incident). You need to conduct capacity planning before usage changes, including the following examples:

-   Design (prediction)
-   Regular spikes (8am sign-on rush)
-   Launch (prediction validation)
-   Business model change
-   Acquisition or merger
-   Marketing push
-   Seasonal change
-   Feature launch
-   Periodically

**Identify reachable limits.** Capacity planning should find hard limits in the workload and evaluate if those are in reach. These limits usually apply to infrastructure (compute, memory, storage, network), application (concurrent database connections, response times, availability), service (requests per second), and scaling. When capacity planning identifies reachable limits, you need to modify the workload before the limit becomes a performance issue. Performance baseline, continuous monitoring, and testing are essential to validating the limits and the fix.

**Understand functional requirements.** Workload requirements include functional and technical requirements. You should meet with stakeholders to ensure you understand the functional requirements of the workload. The technical requirements should support the functional requirements. You need to understand the workload architecture so you can identify the correct components to modify.

**Understand resource limitations.** Resources in your workload have performance limitations. Performance limitations apply to services and SKUs within each service. You need to understand the limitations of the resources in your workload and should factor those limitations into your design decisions. For example, you should know if resources limitations require changing SKUs or changing resources altogether.

**Conduct trend analysis.** Trend analysis uses historical data to identify trends. These trends can be increases or decreases in usage over time (organic). For example, if workload demand has increased by 10% over the past year, then you might forecast a continuation of this trend or a slowdown depending on the suspected causes for growth. Statistical analysis can help identify trends in historical data and help correlate causes and outcomes. For example, linear regression is a common and accessible statistical method that uses historical to make prediction of future trends. All data has outliers and data anomalies that are hard to explain. In linear regression models, the adjusted R square value helps interpret variance and provides a numerical evaluation of predication reliability. You can do this type of analysis easily in Excel.

Trend analysis can also identify the effects of one-time events that cause rapid shifts in traffic up or down (inorganic). For example, data could show a pattern of 5% growth in usage after feature releases. If you have four major releases a year, you should plan for 5% growth each time. You should use data and stakeholder input to inform how you predict the trend will continue (1 year, 3 years).

**Test conclusions.** Capacity planning is an iterative process. You should test the conclusions of your capacity planning and adjust the estimates accordingly.

## Azure facilitation

Azure Monitor provides tools to establish performance targets, conduct trend analysis, and perform capacity planning.

## Tradeoff

Misjudged capacity planning could lead to over provisioning or under provisioning resources. Over provisioning could increase the attack surface and cost of the workload unnecessarily. Under provisioning would degrade performance and reliability.
