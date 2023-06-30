---
title: Principles of the performance efficiency
description: Describes the performance efficiency guiding principles
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/08/2022
ms.topic: conceptual
categories:
  - management-and-governance
ms.custom:
  - overview
---

# Performance efficiency principles
Performance efficiency is the ability of your workload to adjust to changes in demands placed on it by users in an efficient manner. These principles are intended to guide you in your overall strategy for improving performance efficiency.

## Design for horizontal scaling

*Horizontal scaling allows for elasticity*. Instances are added (scale-out) or removed (scale-in) in response to changes in load. Scaling out can improve resiliency by building redundancy. Scaling in can help reduce costs by shutting down excess capacity.

|Approach|Benefit|
|---|---|
|Define a capacity model according to the business requirements |Test the limits for **predicted and random spikes and fluctuations** in load to make sure the application can scale. Factor in the SKU service limits and regional limits so that application scales as expected if there's a regional failure.|
|Use PaaS offerings| Take advantage of the built-in capabilities that **automatically trigger scaling** operations instead of investing in manual scaling efforts that often require custom implementations and can be error prone.|
|Choose the right resources and right-size|Determine if the resources can support the anticipated load. Also, justify the cost implications of the choices.|
|Apply strategies in your design early| Accelerate adoption without significant changes. For example, **strive for stateless application** and store state externally in a database or distributed cache. **Use caching** where possible, to minimize the processing load.|

An alternate approach is vertical scaling (scale up). However, you eventually may reach a limit where there isn't a larger system, and you can't scale up anymore. At that point, any further scaling must be horizontal. So it's good practice to employ a scale-out architecture early on.

## Shift-left on performance testing

*Test early and test often to catch issues early*. 

|Approach|Benefit|
|---|---|
|Run load and stress tests| Measure the application's performance under **predetermined amounts of load** and also the **maximum load** your application and its infrastructure can withstand.
|Establish performance baselines|Determine the **current efficiency** of the application and its supporting infrastructure. You'll be able to identify bottlenecks early before it worsens with load. Also, this strategy can lead to **strategies for improvements** and determine if the application is meeting the business goals.
|Run the test in the continuous integration (CI) build pipeline.|Detect issues early. Any development effort must go through continuous performance testing to make sure changes to the codebase doesn't negatively affect performance. |

## Continuously monitor for performance in production
*Observe the system holistically to evaluate the overall health of the solution*. Capture the test results not only in dev/test environment but also in production. Monitoring and logging in production can help identify bottlenecks and opportunities for improvement.

|Approach|Benefit|
|---|---|
|Monitor the health of the entire solution|Know about the  scalability _and_ resiliency of the infrastructure, application, and dependent services. Gather and review key performance counters regularly.|
|Capture data from repeatable processes| Evaluate the metrics over time that would allow for autoscaling with demand. For reliability, look for early warning signs that might require proactive intervention.|
|Reevaluate the needs of the workload continuously|Identify improvement opportunities with resolution planning. This  effort may require updated configurations and deprecations in favor of more-appropriate solutions.|


## Next section
Use this checklist to review your application architecture from a performance design standpoint.

> [!div class="nextstepaction"]
> [Design checklist](design-efficiency.md)

## Related links

- Performance efficiency impacts the entire architecture spectrum and is interrelated with other pillars of the [Microsoft Azure Well-Architected Framework](../pillars.md).

- Assess your workload using the [Microsoft Azure Well-Architected Review tool](/assessments/?mode=pre-assessment&session=5c2bcc40-1c41-47b1-8729-1fba49dbe408).
