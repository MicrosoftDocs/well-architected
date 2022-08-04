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

## What are the common challenges?

Building and designing sustainable workloads on Microsoft Azure can be challenging for these main reasons:

- Understanding if your workloads are in alignment with sustainability targets. It requires assessments of current workloads to determine if they are designed in a sustainable way.
- Designing workloads that are natively environmentally friendly and optimized.
- Measuring and tracking the emissions of your workloads.

## Is sustainability only about performance and cost?

While [performance efficiency](/azure/architecture/framework/scalability/) and [cost optimization](/azure/architecture/framework/cost/) are areas of strong focus for designing sustainable workloads, the other pillars of the Well-Architected Framework are equally important when building long-term sustainable workloads on Azure.

- [Security](/azure/architecture/framework/security/): how the security appliances in a workload is optimized and designed to auto-scale will have an impact on the environment.
- [Reliability](/azure/architecture/framework/resiliency/): designing reliable workloads that meet sustainability guidelines from the Green Software Foundation can greatly reduce the workloads' carbon and electricity footprint.
- [Operational Excellence](/azure/architecture/framework/devops/): how a workload is able to effectively respond to operational issues can ultimately reduce carbon emissions.

## What are the key design areas?

Sustainable guidance within this series is composed of architectural considerations and recommendations oriented around these key design areas.

Decisions made in one design area can impact or influence decisions across the entire design. The focus is ultimately on building a sustainable solution to minimize the footprint and impact on the environment.

- **Compute**&mdash;
- **Operations**&mdash;
- **Deployment &amp; DevOps**&mdash;
- **Infrastructure Platform**&mdash;
- **Networking**&mdash;
- **Storage**&mdash;
- **Application Design**&mdash;
- **Security**&mdash;

We recommend that readers familiarize themselves with these design areas, reviewing provided considerations and recommendations to better understand the consequences of encompassed decisions.

## Next step