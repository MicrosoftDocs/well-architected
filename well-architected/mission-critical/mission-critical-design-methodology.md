---
title: Design methodology of mission-critical workloads
description: A design methodology to guide readers through the architectural process of building a mature mission-critical application on Microsoft Azure.
author: calcof
ms.author: calcof
ms.date: 02/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories:
  - management-and-governance
  - web
products: Azure
ms.custom:
  - mission-critical
---

# Design methodology

This design methodology provides easy to follow guidance surrounding the critical design decisions required to produce a target [AlwaysOn](https://github.com/azure/alwayson) architecture.

> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [What is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload).

## Architecture pattern

### Components
Most cloud applications consist of multiple application services — web front ends, databases, business processes, reporting and analysis, and so on. To achieve scalability and reliability, each of those services should run on multiple instances.

## Critical design path

At the heart of an AlwaysOn target architecture definition lies a critical design path, comprised of 5 foundational [design principles](./alwayson-design-principles.md) and 8 [fundamental design areas](./alwayson-design-areas.md) with heavily interrelated and dependent design decisions.

Ultimately, the impact of decisions made within each area will reverberate across other design areas and design decisions. Readers are strongly advised to familiarize themselves with these 8 critical design areas, reviewing provided considerations and recommendations to better understand the consequences of encompassed decisions, which may later produce trade-offs or unforeseen consequences within related areas. For example, to define a target architecture it is critical to determine how best to monitor application health across key components. In this instance, the reader should review the Health Modelling design area, using the outlined recommendations to help drive decisions.

## Design for business requirements

Not all mission-critical applications have the same requirements, and as a result the review considerations and design recommendations provided by this design methodology may yield different design decisions and trade-offs, which is to be expected.

### Reliability tiers

Reliability is a subjective concept and for an application to be appropriately reliable it must reflect the business requirements surrounding it. For example, a mission-critical application with a 99.999% availability Service Level Objective (SLO) requires a much higher level of reliability and operational rigour than another application with an SLO of 99.9%. However, there are obvious financial and opportunity cost implications for introducing greater reliability, and such trade-offs should be carefully considered.

The AlwaysOn design methodology leverages several reliability tiers orientated around an availability SLO to introduce further specificity to design recommendations. These tiers are therefore intended as a reference to help readers better navigate requisite design decisions to achieve required levels of reliability.

|Reliability Tier (Availability SLO)|Permitted Downtime (Week)|Permitted Downtime (Month)|Permitted Downtime (Year)|
|--|--|--|--|
|99.9%|10 minutes, 4 seconds|43 minutes, 49 seconds|8 hours, 45 minutes, 56 seconds|
|99.95%|5 minutes, 2 seconds|21 minutes, 54 seconds|4 hours, 22 minutes, 58 seconds|
|99.99%|1 minutes|4 minutes 22 seconds|52 minutes, 35 seconds|
|99.999%|6 seconds|26 seconds|5 minutes, 15 seconds|
|99.9999%|<1 second|2 seconds|31 seconds|

> It is important to note that AlwaysOn and this design methodology consider availability to be more than simple uptime, but rather a consistent level of application service relative to a known healthy application state which is captured by a codified health model.

The pursuit of a particular reliability tier ultimately has a significant bearing on the critical design path and encompassed design decisions, resulting in a different target architecture.

The image below demonstrates how the different reliability tiers and underlying business requirements influence the target architecture for the foundational reference implementation, particularly concerning the number of regional deployments and utilized global technologies.

![AlwaysOn Reliability Tiers](./images/alwayson-reliability-tiers.png "AlwaysOn Reliability Tiers")

![AlwaysOn SLO Availability Targets](./images/alwayson-slo.gif "AlwaysOn SLO Availability Targets")

### Opportunity cost

There is ultimately an opportunity cost associated with achieving an always on application design since it requires significant engineering investment in fundamental reliability concepts, such as fully embracing infrastructure as code, deployment automation, and chaos engineering. This comes at a cost in terms of time/effort which could be invested elsewhere to deliver new application functionality and features.

Furthermore, maximizing reliability with an AlwaysOn application design can also have a significant bearing on financial costs, primarily through the duplication of resources and the distribution of resources across regions to achieve high availability. To avoid excess costs, it is highly recommended that **AlwaysOn solutions not be over-engineered/over-optimized/over-provisioned beyond relevant [business requirements](/azure/architecture/guide/design-principles/build-for-business)**.

## Synthetic application construction

In parallel to design activities, it is highly recommended that a synthetic AlwaysOn application environment be established using the [foundational-online](https://github.com/Azure/AlwaysOn-Foundational-Online) and [foundational-connected](https://github.com/Azure/AlwaysOn-Foundational-Connected) reference implementations. 

This provides hands-on opportunities to validate design decisions by replicating the target architecture, allowing for design uncertainty to be swiftly assessed. If applied correctly with representative requirement coverage, most problematic issues likely to hinder progress can be uncovered and subsequently addressed.

## Target architecture evolution

Application architectures established using this design methodology must continue to [evolve in alignment with Azure platform roadmaps](/azure/architecture/guide/design-principles/design-for-evolution) to support optimized sustainability.

|Design area|About|
|---|---|
|Application design| Cloud application design patterns that allow for scaling, and error handling. 	|
|Hosting infrastructure| Hosting environment choices, application dependencies, frameworks, and libraries.	|
|Networking| Network topology considerations at an application level, considering requisite connectivity and redundant traffic management.|
|Data storage|Choices in data store technologies by evaluating the volume, velocity, variety, and veracity characteristics.|
|Monitoring|Observability considerations through raw monitoring logs and metrics to determine the overall health.	|
|Deployment| Strategies for CI/CD pipelines and automation considerations.	|
|Testing| Scenarios, such as unit, build, static, security, integration, regression, UX, performance, capacity and failure injection (chaos). |
|Security|Mitigation of attack vectors through Microsoft Zero Trust model.|
|Operations|Processes related to deployment, key management, patching and updates.|

## Next steps

- Review the AlwaysOn [Design Principles](./alwayson-design-principles.md) for mission-critical application scenarios.
