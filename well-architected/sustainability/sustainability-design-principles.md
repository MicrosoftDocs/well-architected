---
title: Design principles for sustainability workloads on Azure
description: Understand the design principles for building a sustainable application on Microsoft Azure.
author: Zimmergren
ms.author: tozimmergren
ms.date: 07/22/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories:
  - management-and-governance
  - web
products: Azure
ms.custom:
  - sustainability
---

# Design principles of a sustainable workload

The sustainability design methodology provides a framework to record, report and reduce or optimize the environmental impact of your workloads.

To achieve an increase in carbon efficiency, consider how your workload directly, and indirectly, can reduce carbon emissions through:

- Using less physical and virtual resources
- Using less energy
- Using energy and resources more intelligently
- Supporting older devices

It's important to effectively record, report, and reduce carbon emissions through actionable insights.

- Gain transparency into your current carbon impact
- Estimate savings
- Take action to accelerate progress

These critical design principles for sustainability resonate and extend the quality pillars of the Azure Well-Architected Framework&mdash;[Reliability](/azure/architecture/framework/#reliability), [Security](/azure/architecture/framework/security/), [Cost Optimization](/azure/architecture/framework/cost/), [Operational Excellence](/azure/architecture/framework/devops/), and [Performance Efficiency](/azure/architecture/framework/scalability/).

## Principles of green software

Microsoft is actively working toward sustainability targets, and empowers every organization to help reduce emissions and improve our environmental health. The Azure Well-Architected Framework workload for sustainability aligns with the [Principles of Green Software Engineering](https://principles.green/) from the [Green Software Foundation](https://greensoftware.foundation/projects/principles-of-green-software-engineering).

The principles of green software are the starting point to understand the [SCI model](sustainability-design-methodology.md#measure-and-track-carbon-impact) and how this will be included in our framework.

## Carbon efficiency

The application or software must emit the least amount of carbon possible. A carbon efficient cloud application is one that is optimized, and the starting point is the cost – streamlining the application infrastructure and cost will ensure that no unnecessary resources are wasted in the cloud to run the software. But this isn't enough, as you might have cost-optimized your application but still waste tons of resources that emit carbon for no reason.

## Energy efficiency

The goal of this principle is that you build applications that are energy-efficient. This is a common pattern for mobile applications, since they must rely on a battery powered device and are optimizing its consumption. It's less common, however, for desktop or web applications, since until now, developers have never been asked to optimize the electricity consumption of their software.

## Carbon awareness

We need to make the application aware of how much carbon it’s emitting. This way, we can react to specific conditions of energy supply, using demand shifting and demand shaping techniques:

- Demand shifting means moving the workloads and resources to regions or data centers where the energy supply is higher (and incidentally the cost is lower)
- Demand shaping means changing the application’s behavior and appearance to match the energy supply in real-time. A good practice is to build an eco-version of the app and keep it as benchmark for demand shaping but also for carbon optimization.

## Hardware efficiency

Embodied carbon is the carbon that was emitted to build a device. Therefore, a sustainable application will make sure older devices are supported and will maximize the efficiency of each device.  The goal is to build hardware-efficient applications.

Consider the tradeoff that older devices can have power inefficiencies, and may not always be suitable.

## Measuring sustainability

Measuring carbon emissions of a cloud application is a complex task, as it involves the whole ecosystem of the software: from the cloud infrastructure (where we have the emissions dashboards to help us out), to the network path that is crossed, to the edge technology and user devices. With the SCI, we aren't targeting a discrete measurement of carbon emissions, but a score that will change over time and with our optimization techniques.

## Next steps

Review the considerations for application design.

> [!div class="nextstepaction"]
> [Application design](sustainability-application-design.md)
