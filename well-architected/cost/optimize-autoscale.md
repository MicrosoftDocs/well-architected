---
title: Autoscale instances
description: Learn about autoscaling strategies to lower the cost in virtual machine instances or stateless applications in Azure.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 04/18/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - article
  - internal-intro
products:
  - azure
  - azure-cost-management
---

# Autoscale instances

In Azure, it's easier to grow a service with little to no downtime compared to downscaling a service, which usually requires deprovisioning or downtime. In general, opt for scale-out instead of scale up.

For certain applications, capacity requirements might change over time. Autoscaling policies allow for less error-prone operations and cost savings through robust automation.

## Virtual machine instances

For autoscaling, consider the choice of instance size. The size can significantly change the cost of your workload.

> :::image type="icon" source="./images/i-best-practices.png"::: Choose smaller instances where workload is highly variable and scale out to get the desired level of performance, rather than up. This approach lets you make your cost calculations and estimates granular.

![Diagram shows wastage by over-sizing resources as a chart.](../_images/over-sizing.png)

## Stateless applications

You can use many Azure services to improve the application's ability to scale dynamically, even if the application isn't originally designed to scale dynamically.

For example, many ASP.NET stateful web applications can be made stateless. Then you can autoscale them, which results in a cost benefit. You store state using [Azure Redis Cache](/azure/azure-cache-for-redis/cache-aspnet-session-state-provider), or Azure Cosmos DB as a back-end session state store through a [session state provider](https://github.com/aspnet/AspNetSessionState).
