---
title: Sustainability workloads
description: Overview of sustainability workloads on Azure that are highly reliable.
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

# Sustainable workloads

## What is a sustainable workload?

The term _workload_ refers to a collection of application resources that support a common business goal or the execution of a common business process, with multiple services, such as APIs and data stores, working together to deliver specific end-to-end functionality.

With _sustainability_, we refer to the environmental impact of our workloads.

A _sustainable workload_ therefore describes the practice of designing solutions that maximize utilization while minimizing waste, ultimately reducing the footprint on the environment.

### Measure and track carbon impact

According to the Green Software Foundation, to which Microsoft’s view is aligned on green software, the measurement for the carbon impact of an application is Software Carbon Intensity (SCI), and is calculated as follows:
```SCI = ((E*I)+M) per R```

Where:

- E	= energy consumed by software (kWh)
- I	= carbon emitted per kWh of energy, gCO2/kWh
- M	= carbon emitted through the hardware that software is running on
- R	= Functional Unit, which is how the application is scaling: per user, per service, etc.

Therefore, you should take into account not just the application infrastructure, but also the user devices and how the application scales, because it might have a larger impact than thought.

Review the main [design principles for sustainable workloads on Azure](sustainability-design-principles.md) to learn how you can build and optimize your solutions in alignment with the Green Software Foundation.

## What are the common challenges?

> TODO. Common challenges customers experience when designing sustainable workloads.

- Measuring and tracking carbon impact
- Designing workloads that are natively environmentally friendly and optimized

### Reducing emissions

In order to reduce carbon emissions, we have three possible solutions:

- Carbon neutralization – compensating carbon emissions
- Carbon avoidance – not emitting carbon in the first place
- Carbon removal – subtract carbon from the atmosphere

Carbon removal is referred to as “the only way to bring our atmospheric levels back to normal and undo the damage already done.” The goal of green software is to avoid emitting unnecessary emissions in the first place.

## Is sustainability only about performance and cost?

<!-- TBD: Remove this H2? -->

While performance efficiency and cost optimization are areas of strong focus for designing sustainable workloads, the other pillars of the Well-Architected Framework are equally important when building long-term sustainable workloads on Azure.

- [Security](/azure/architecture/framework/security/): TODO
- [Reliability](/azure/architecture/framework/resiliency/): TODO
- [Operational Excellence](/azure/architecture/framework/devops/): TODO

## What are the key design areas?

Sustainable guidance within this series is composed of architectural considerations and recommendations oriented around these key design areas.

> TODO: If possible, insert graphic.

Decisions made in one design area can impact or influence decisions across the entire design. The focus is ultimately on building a sustainable solution to minimize the footprint and impact on the environment.

> TBD: Decide whether the design areas are the principles from the green software foundation, or our WAF Pillars.

## Illustrative examples

The guidance provided within this series is based on a solution-oriented approach to illustrate key design considerations and recommendations for sustainability. There are several reference implementations available as part of an open source project on GitHub. These implementations can be used as a basis for further solution development.

> TODO: Insert link to Sustainability repository

- [A proxy architecture to score sustainability](#) (TODO: Link to AAC article)

[Implementation: Sustainability Reference Architecture](#) (TODO: Link to specific implementation on GitHub).

> TODO: Information from the reference architecture doc.

## Shared responsibility model for carbon

As mentioned earlier, an application’s impact on carbon emission isn't solely limited to the cloud infrastructure but will span several technologies and points of usage.
The first step of the cloud efficiency (cost + carbon) paradigm is optimizing the cost of the application. Refer to the cost optimization pillar for more info.

We're already familiar with all the cost management guidelines.
Optimizing carbon emissions means to make your applications and architectures carbon aware, score them against a benchmark or proxy to measure any improvement or status change, and take any available actions to reduce emissions according to the situation and workload.

Optimizing your workload for energy means being aware of their energy requirements and have the software perform the same features with less electricity consumption.
Once you have an optimized and streamlined application, it’s time to work on its carbon and energy impact.

The cloud provider is responsible for all the datacenter portion of the app: Microsoft has a sustainability goal and most of its Azure datacenters are driving towards a zero-emission goal (**more info or link here).
But putting an application in the cloud doesn't mean that automatically it's sustainable. Let’s pick an example:

- An application that is heavy on carbon is using X resources on Azure
- 10 highly optimized and efficient applications can run on the same X resources on Azure.

This means that even if we're using resources that are carbon optimized, we shouldn't waste them and in principle, try to use them in the most efficient possible way. Just as resiliency, sustainability is a shared responsibility goal that needs to combine the efforts of both the Azure cloud provider and the customer or partner that is designing and implementing the application.

## Cloud efficiency process overview

### Optimize workload to reduce direct factors

Reduce the impact of compute by right sizing resources, introduce dynamic autoscaling to allocate and deallocate resources when needed, modernize your application components towards PaaS and serverless technologies. Consider running batch workloads during quiet hours, and upgrade frameworks to improve efficiency.

### Reduce indirect factors

Scope 3 emissions account for a large carbon impact through application delivery, reduce network bandwidth through compression, consider using a Content Delivery Network (CDN), and reducing asset sizes.

### Continuously reevaluate and improve

Schedule regular reviews of the Emissions Impact Dashboard and your SCI score, set measures and scorecards, document success and forecast future impact

## Next step