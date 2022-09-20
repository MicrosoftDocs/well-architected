---
title: Principles of the performance efficiency
description: Describes the performance efficiency guiding principles
author: a11smiles
ms.author: robbymillsap
ms.date: 12/08/2021
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories:
  - management-and-governance
products:
  - azure-monitor
ms.custom:
  - overview
---

# Performance efficiency principles
Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. You need to anticipate increases in cloud environments to meet business requirements. These principles are intended to guide you in your overall strategy for improving performance efficiency.

## Design for horizontal scaling

*Horizontal scaling allows for elasticity*. Instances are added (scale-out) or removed (scale-in) in response to changes in load. Scaling out can improve resiliency by building redundancy. 

|Strategy|Benefit|
|---|---|
|Define a capacity model according to the business requirements |Test the limits for **predicted and random spikes and fluctuations** in load to make sure the application can scale. Factor in the SKU service limits and regional limits so that application scales as expected if there's a regional failure.|
|Use PaaS offerings| Take advantage of the built-in capabilities that **automatically trigger scaling** operations instead of investing in manual scaling efforts that often require custom implementations and can be error prone.|
|Choose the right resources and right-size|Determine if the resources can support the anticipated load. Also, justify the cost implications of the choices.|
|Apply strategies in your design early| Accelerate adoption without significant changes. For example, **strive for stateless application** and store state externally in a database or distributed cache. **Use caching** where possible, to minimize the processing load.|

An alternate approach is vertical scaling. However, the application can eventually reach a limit, where you can't scale up anymore. At that point, any further scaling must be horizontal.

## Shift-left on performance testing

*Test early and test often*. 

|Strategy|Benefit|
|---|---|
|Run load and stress tests| Measure the application's performance under **predetermined amounts of load** and also the **maximum load** your application and its infrastructure can withstand.
|Establish performance baselines|Determine the **current efficiency** of the application and its supporting infrastructure. You'll be able to identify bottlenecks early before it worsens with load. Also, this strategy can lead to **strategies for improvements** and determine if the application is meeting the business goals.
|Run the test in the continuous integration (CI) build pipeline.|Detect issues early. Any development effort must go through continuous performance testing to make sure changes to the codebase doesn't negatively affect performance. |

## Continuously monitor for performance in production

- **Have a data-driven approach**&mdash;Base your decisions on the data captured from repeatable processes. Archive data to monitor performance changes  _over time_, not just compared to the last measurement taken.

- **Monitor the health of current workloads**&mdash;In monitoring strategy, consider scalability _and_ resiliency of the infrastructure, application, and dependent services. For scalability, look at the metrics that would allow you to provision resources dynamically and scale with demand. For reliability, look for early warning signs that might require proactive intervention.

[**Identify improvement opportunities with resolution planning**](optimize.md)

Understand the scope of your planned resolution and communicate the changes to all necessary stakeholders. Make code enhancements through a new build. Enhancements to infrastructure may involve many teams. This involved effort may require updated configurations and deprecations in favor of more-appropriate solutions.

[**Understand the challenges of distributed architectures**](design-distributed.md)

Most cloud deployments are based on distributed architectures where components are distributed across various services. Troubleshooting monolithic applications often requires only one or two lenses—the application and the database. With distributed architectures, troubleshooting is complex and challenging because of various factors. For example, capturing telemetry throughout the application—across all services—as possible. Also, the team should be skilled with the necessary expertise to troubleshoot all services in your architecture.




## Next section
Use this checklist to review your application architecture from a performance design standpoint.

> [!div class="nextstepaction"]
> [Design checklist](design-checklist.md)

## Related links

- Performance efficiency impacts the entire architecture spectrum. Bridge gaps in your knowledge of Azure by reviewing the five pillars of the [Microsoft Azure Well-Architected Framework](../index.md).

- To assess your workload using pillars, see the [Microsoft Azure Well-Architected Review](/assessments/?mode=pre-assessment&session=5c2bcc40-1c41-47b1-8729-1fba49dbe408).
