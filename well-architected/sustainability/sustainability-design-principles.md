---
title: Design principles for sustainability workloads on Azure
description: Understand the design principles for building a sustainable application on Microsoft Azure.
author: Zimmergren
ms.author: tozimmergren
ms.date: 10/12/2022
ms.topic: conceptual
categories:
  - management-and-governance
  - web
ms.custom:
  - sustainability
  - ignite-2022
---

# Design principles of a sustainable workload

The sustainability design methodology provides a framework to record, report, and reduce or optimize the environmental impact of your workloads.

To achieve an increase in carbon efficiency, consider how your workload, directly and indirectly, can reduce carbon emissions through:

- Using less physical and virtual resources
- Using less energy
- Using energy and resources more intelligently
- Supporting older devices

It's important to effectively record, report, and reduce carbon emissions through actionable insights.

- Gain transparency into your current carbon impact
- Estimate savings
- Take action to accelerate progress

These critical design principles for sustainability resonate and extend the quality pillars of the Azure Well-Architected Framework—[Reliability](../reliability/), [Security](../security/), [Cost Optimization](../cost-optimization/), [Operational Excellence](../operational-excellence/), and [Performance Efficiency](../performance-efficiency/).

## Principles of green software

Microsoft is actively working toward sustainability targets, and empowers every organization to help reduce emissions and improve our environmental health. The Azure Well-Architected Framework workload for sustainability aligns with the [Green Software Principles](https://learn.greensoftware.foundation/practitioner/introduction/) from the [Green Software Foundation](https://greensoftware.foundation).

The principles of green software are the starting point to understand the [SCI model](sustainability-design-methodology.md#measure-and-track-carbon-impact) and how this will be included in our framework.

## Carbon efficiency

**Principle:** Emit the least amount of carbon possible.

The application or software must emit the least amount of carbon possible. A carbon efficient cloud application is one that is optimized, and the starting point is the cost – streamlining the application infrastructure and cost will ensure that no unnecessary resources are wasted in the cloud to run the software. But this isn't enough, as you might have cost-optimized your application but still waste tons of resources that emit carbon for no reason.

- Read more about the [Carbon Efficiency principle](https://learn.greensoftware.foundation/practitioner/carbon-efficiency) from the Green Software Foundation.

## Energy efficiency

**Principle:** Use the least amount of energy possible.

The goal of this principle is that you build applications that are energy-efficient. This is a common pattern for mobile applications, since they must rely on a battery powered device and are optimizing its consumption. It's less common, however, for desktop or web applications, since until now, developers have never been asked to optimize the electricity consumption of their software.

- Read more about the [Energy Efficiency principle](https://learn.greensoftware.foundation/practitioner/energy-efficiency) from the Green Software Foundation

## Carbon awareness

**Principle:** Do more when the electricity is cleaner and do less when the electricity is dirtier.

We need to make the application aware of how much carbon it's emitting. This way, we can react to specific conditions of energy supply using demand shifting and demand shaping techniques:

|Technique|Description|
|---|---|
Demand shifting|Demand shifting means moving the workloads and resources to regions or data centers, or a time in the data center where the energy supply is high and the demand is lower and can be met by renewable energy. Delaying running apps to a time when there's less demand should result in lower carbon intensity.
|Demand shaping|Demand shaping means changing the application's behavior and appearance to match the energy supply in real-time. A good practice is to build an eco-version of the app and keep it as a benchmark for demand shaping and carbon optimization.|

- Read more about the [Carbon Awareness principle](https://learn.greensoftware.foundation/practitioner/carbon-awareness) from the Green Software Foundation.

## Hardware efficiency

**Principle:** Use the least amount of embodied carbon possible.

Embodied carbon is the carbon that was emitted to build a device. Therefore, a sustainable application will make sure older devices are supported and will maximize the efficiency of each device.  The goal is to build hardware-efficient applications.

Consider the tradeoff that older devices can have power inefficiencies, and may not always be suitable.

- Read more about the [Hardware Efficiency principle](https://learn.greensoftware.foundation/practitioner/hardware-efficiency) from the Green Software Foundation.

## Measuring sustainability

**Principle:** What you can't measure, you can't improve.

Measuring carbon emissions of a cloud application is a complex task, as it involves the whole ecosystem of the software: from the cloud infrastructure (where we have the emissions dashboards to help us out), to the network path that is crossed, to the edge technology and user devices. With the SCI, we aren't targeting a discrete measurement of carbon emissions, but a score that will change over time and with our optimization techniques.

- Read more about the [Measurement](https://learn.greensoftware.foundation/practitioner/measurement) from the Green Software Foundation.

## Climate commitments

**Principle:** Understand the exact mechanism of reduction.

Many corporations and groups have made commitments to the climate. They actively work toward new sustainability goals with a primary objective to remove, reduce and prevent carbon emissions.

There are several options for reducing the carbon footprint of any organization or entity. However, and aligned with the goal of the Green Software Foundation, our main direction should always be to avoid emitting carbon in the first place. This is what we call Abatement, or Carbon Elimination.

Once we've pursued this goal, there will still be emissions that can't be avoided. All the remaining carbon reduction methodologies will help us do so, offsetting (either compensating or neutralizing carbon).

Your company's strategy can be a mix of all the possible methodologies and, depending on the final result, can reach a Net Zero target when carbon emissions are eliminated where possible and the residual emissions compensated.

The [SCI equation](sustainability-design-methodology.md#measure-and-track-carbon-impact) aims to eliminate emissions, which should always be the primary goal of a sustainable workload, and the score can only be reduced with abatement.

- Read more about the [Climate Commitments](https://learn.greensoftware.foundation/practitioner/climate-commitments) from the Green Software Foundation.

## Next steps

Review the considerations for application design.

> [!div class="nextstepaction"]
> [Application design](sustainability-application-design.md)
