---
title: Carrier-grade workloads on Azure
description: Get started with an overview of carrier-grade workloads.
author: mikedell73
ms.author: prwilk
ms.date: 08/23/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.industry: telecommunication
products: 
- azure
ms.custom:
  - carrier-grade
---

# Carrier-grade workloads on Azure

Mission-critical systems primarily focus on maximizing uptime and they exist in many industries.  Within the telecommunications industry, they're referred to as _carrier-grade systems_.  These systems are developed due to one or more of the following drivers:

- Minimizing loss of life or injury. 
- Meeting regulatory requirements on reliability to avoid paying fines. 
- Optimizing service to customers to minimize churn to competitors. 
- Meeting contractual Service Level Agreements (SLAs) with business or government customers. 

This series of articles applies the [design methodology for mission-critical workloads](/azure/architecture/framework/mission-critical/mission-critical-design-methodology) to inform prescriptive guidance for building and operating a highly reliable, resilient, and available telecommunication workload on Azure. 

>[!NOTE]
> The articles within this series focus on providing additional mission-critical considerations when designing for 99.999% ('5 9s') levels of reliability within the telecommunications industry.

## What is a carrier-grade workload?

The term _workload_ refers to a collection of application resources that support a common business goal or the execution of a common business process, with multiple services, such as APIs and data stores, working together to deliver specific end-to-end functionality.

The term _mission-critical_ refers to a [criticality classification](/azure/cloud-adoption-framework/manage/considerations/criticality) where a significant financial cost (business-critical) or human cost (safety-critical) is associated with unavailability or underperformance.

A _carrier-grade workload_ pivots on both business-critical and safety-critical, where there's a fundamental requirement to be operational with only minutes or even seconds of downtime per calendar year. Failure to achieve this uptime requirement can result in extensive loss of life, incur significant fines, or contractual penalties. 

The *operational* aspect of the workload includes how reliability is measured and the targets that it must meet or exceed. Highly reliable systems typically target 99.999% uptime (commonly referred to as '5 9s') or 0.001% downtime in a year (approximately 5 minutes). Some systems target 99.9999% uptime, or 30 seconds downtime per year, or even higher levels of reliability. This covers all forms and causes of outage – scheduled maintenance, infrastructure failure, human error, software issues and even natural disaster. 

Although the platform used has evolved from dedicated, proprietary hardware through commercial, off-the-sheld hardware to OpenStack or VMware clouds, Telecommunication companies consistently deliver services achieving ≤ 5 minutes of downtime per year, and in many cases, achieve ≤ 30 seconds of downtime due to unscheduled outages.

## What are the common challenges?

Building carrier-grade workloads is a challenge for these main reasons:

### Lift and shift approach

Telecommunication companies have well-architected applications that deliver the expected behavior on their existing infrastructure. However, care should be taken before assuming that porting these applications *as is* to a public cloud infrastructure won't impact their resiliency.

The existing applications make a set of assumptions about their underlying infrastructure, which are unlikely to remain true when moving from on-premises to public cloud. The architect must check that they still hold and adjust infrastructure and application design to accommodate the new reality. The architect should also look for opportunities where the new infrastructure removes limitations that existed on-premises.

For example, upgrade of on-premises systems must happen in place because it's not viable to maintain sufficient hardware to create a new deployment alongside and slowly transition in a safe manner. This upgrade path generates a host of requirements for how upgrades and rollbacks are managed. These requirements lead to complexity and mean that upgrades are infrequent and only permitted in carefully managed maintenance windows.

However, in public cloud, it's reasonable to create a new deployment in parallel with the existing deployment. This process creates the opportunity for major simplifications in the application's operational design and improvements in the user's experience, and expectations.

### Monolithic solutions

Experience across a range of mission-critical industries shows that it isn't realistic to attempt to construct a monolithic solution which will achieve the desired levels of availability.  There are just too many potential sources of failure in a complex system.  Instead, successful solutions are composed of individual  independent and redundant elements.  Each unit has relatively basic availability (typically ~99.9%), but when combined together the total solution achieves the necessary availability goals.  The art of carrier-grade engineering then becomes ensuring that the only dependencies common to the redundant elements are those which are absolutely necessary since they exert the most significant influence on overall availability, often orders of magnitude greater than anything else in the design.

### Only building zonal redundancy

Using [Microsoft Azure Availability Zones](/azure/architecture/high-availability/building-solutions-for-high-availability) is the basic choice for reducing the risk of outage due to hardware failure or localized environmental issues. However, it isn't enough to achieve carrier-grade availability, mainly for these reasons: 

- Availability Zones (AZs) are designed so that the network latency between any two zones in a single region is ≤ 2 ms. AZs can't be widely and geographically dispersed. So, the AZs share a correlated risk of failure due to natural disasters, such as flooding or massive power outages, which could disable multiple AZs within a region.

- Many Azure services are explicitly designed to be zone-redundant so that the applications using them don't need explicit logic to benefit from the availability gain. This redundancy function within the service requires collaboration between the elements in each zone. There's often an unavoidable risk of software failure in one zone that causes correlated failures in other zones. For example, any issue with a secret or certificate used with the zone redundant service could impact all AZs simultaneously.

## What are the key design areas?

When designing a carrier-grade workload, consider the following areas:

|Design area|Description|
|---|---|
|[**Fault tolerance**](carrier-grade-design-area-fault-tolerance.md)|Application design must allow for unavoidable failures so that the application as a whole can continue to operate at some level. The design must minimize points of failure and take a federated approach.|
|[**Data model**](carrier-grade-design-area-data-model.md)|The design must address the consistency, availability, and partition tolerance needs of the database. Carrier Grade availability requires that the application is deployed across multiple regions. This deployment requires careful thought about how the application's data will be managed across those regions.|  
|[**Health modeling**](carrier-grade-design-area-health-modeling.md)|An effective health model provides observability of all components, platform and application, so that issues can be quickly detected and response is ready either through self-healing or other remediations.|
|[**Testing and validation**](carrier-grade-design-area-testing.md)|The design and implementation of the application must be thoroughly tested. In addition, the integration and deployment of the application as a whole solution must be tested.|

  

## Next step

Start by reviewing the design principles for carrier-grade application scenarios.

> [!div class="nextstepaction"]
> [Design principles](carrier-grade-design-principles.md)
