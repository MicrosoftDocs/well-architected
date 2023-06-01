---
title: Consumption and fixed cost models
description: Learn how to compare and contrast the consumption-based and fixed-price cost models for Azure services.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 03/28/2023
ms.topic: conceptual
ms.custom:
  - article
  - internal-intro
products:
  - azure
  - azure-cost-management
---

# Consumption and fixed cost models

The common pricing options for Azure services are:

- Consumption-based price - You're charged for only what you use. This model is also known as the pay-as-you-go rate.
- Fixed price - You provision resources and are charged for those instances whether you use them or not.

A common way to estimate cost is by considering workloads on a peak throughput. With consistently high utilization, consumption-based pricing can be less efficient for estimating baseline costs when compared to the equivalent provisioned pricing. Platform as a service (PaaS) and serverless technologies can help you understand the economy cutoff point for consumption-based pricing.

See the difference between cost models based on fixed, static services provisioning, and more variable costs based on autoscaling serverless technologies.

![Diagram that shows the abstraction choices and their impacts on system costs.](../_images/choice-of-abstraction.png)

Start with a fixed minimum-level of performance and then use architectural patterns, such as [queue based load leveling](/azure/architecture/patterns/queue-based-load-leveling) and autoscaling services. With this approach, the peaks are smoothed out into a more consistent flow of compute and data. This approach should temporarily extend your burst performance when the service is under sustained load. If cost is an important factor but you need to maintain service availability under burst workloads, use the [throttling pattern](/azure/architecture/patterns/throttling) to maintain service quality under load.

Compare and contrast the options and understand how to provision workloads that can potentially switch between the two models. The model is a tradeoff between scalability and predictability. Ideally in the architecture, you blend the two aspects.
