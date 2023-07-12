---
title: Architect for regions and availability zones
description: A guide for understanding when to deploy workloads across availability zones or regions.
author: johndowns
ms.author: jodowns
ms.date: 07/12/2023
ms.topic: conceptual
ms.custom:
  - guide
categories:
  - management-and-governance
---

# Architect for regions and availability zones

When you architect a solution for Azure, you need to decide which region to use, whether you deploy across multiple availabilty zones in a region, and whether you deploy into multiple regions. These decisions affect your solution's reliability, cost, performance, and operational efficiency. This guide provides TODO.

## Key requirements

To make an informed decision about which approach works for your solution, you need to understand your requirements. These requirements should be driven by discussions between solution designers and business stakeholders.

### Risk tolerance

Different organizations have different risk appetites, and risk tolerance is often different for each workload. Some workloads are so important that it's worth even mitigating risks that are unlikely to occur, like major natural disasters occurring across a wide geographic area. Other workloads are less critical.

The following table lists some common risks that should be considered in a cloud environment:

| Risk | Example | Frequency |
|-|-|-|
| Hardware outage | <ul><li>Issue with hard disk or networking equipment</li><li>Host reboots</li></ul> | Fairly common. Any resiliency strategy should account for these risks. |
| Data center outage | <ul><li>Power, cooling, or network failure across an entire data center</li><li>Natural disaster in one part of a metro area</li></ul> | Infrequent |
| Region outage | Major natural disaster that affects a wide geographical area | Very infrequent |

<!-- TODO should we also include service outages? e.g. global service outage for something like Azure Front Door or Entra ID. Need to look to mission-critical guidance for this level of risk mitigation -->

While every organization would ideally like to mitigate every possible risk, it's not practical or cost effective to do so. It's important to have an open discussion with business stakeholders so that you can make an informed decision about the risks you should mitigate.

> [!TIP]
> Generally, only customers with mission-critical workloads choose to mitigate risks with low likelihood. For example, banks, governments, and healthcare workloads typically have a high degree of importance and need to remain operational in all situations. For other workloads, the organization's risk tolerance usually is higher.

### Resiliency requirements

It's important that you understand your resiliency requirements for your workload, including the RTO and RPO. If you don't have clear requirements, you can't make an informed decision about which approach to follow.

### User location

If your users are geographically dispersed, it might make sense to deploy your workload across multiple regions. If your users are in one area, a single-region deployment can simplify your operational requirements and reduce your costs.

### Budget

If you operate under a constrained budget, it's important to consider the costs involved in redundant workload components. These costs can include additional resource charges, networking costs, and operational costs to manage more resources and a more complex environment. 

### Complexity

It's a good practice to avoid unnecessary complexity in your solution architecture. The more complexity you introduce, the harder it is to reason about your architecture. Complex architectures are harder to operate, harder to secure, and are often less performant.

## Tradeoffs to consider

Deciding on the best deployment architecture for your requirements means that you need to consider tradeoffs. Four of the pillars of the well-architected framework are 

- **Reliability:** Your choice of deployment architecture mitigates different types of risks. In general, the more geographically distributed a workload is, the more resilient it can be.
- **Cost Optimization:** Some architectural approaches require deploying more resources, which incurs a resource cost. Others involve sending data across geographically separated availability zones or regions, which incurs bandwidth costs.
- **Performance Efficiency:** Some workloads are highly latency-sensitive,  Only really a concern for highly latency-sensitive workloads. Most workloads don't fit into this category.
- **Operational Efficiency:** Management and failover

--

## Options and tradeoffs

### Single region, no zone redundancy

<!-- TODO description -->

<!-- TODO diagram -->

| Architectural Concern | Impact |
|-|-|
| Reliability | **Low reliability.** Services are subject to outages if a data center fails. Application can be built to be resilient to other types of failures. |
| Cost Optimization | **Lowest cost.** Likely to only have a single instance of each resource, and no inter-zone or inter-region bandwidth costs. |
| Performance Efficiency | **Depends on the workload.** Components aren't guaranteed to be located in the same availablity zone, so highly latency-sensitive components might see lower performance. |
| Operational Efficiency | **Easy to operate.** Likely to only have a single instance of each resource that needs to be managed. |

### Single region, zone redundant services

<!-- TODO description -->

<!-- TODO diagram -->

| Architectural Concern | Impact |
|-|-|
| Reliability | **High reliability.** Risks  |
| Cost Optimization | TODO |
| Performance Efficiency | TODO |
| Operational Efficiency | TODO |

- RE: high
- PE: usually fine, but latency-sensitive workloads might have isses
- OC: easy
- CE: usually has some cost

### Single region, zonal across multiple zones

<!-- TODO description -->

<!-- TODO diagram -->

| Architectural Concern | Impact |
|-|-|
| Reliability | TODO |
| Cost Optimization | TODO |
| Performance Efficiency | TODO |
| Operational Efficiency | TODO |

Note only makes sense for very specific use cases
 - RE: high (if configured correctly)
 - PE: co-located resources mean latency can be very low
 - OC: high, need to plan failover
 - CE: higher cost

This is also called metro DR

### Single region, zone redundant services, with backup across regions

<!-- TODO description -->

<!-- TODO diagram -->

| Architectural Concern | Impact |
|-|-|
| Reliability | TODO |
| Cost Optimization | TODO |
| Performance Efficiency | TODO |
| Operational Efficiency | TODO |

 - RE: very high
 - PE: usually fine, but latency-sensitive workloads might have isses
 - OC: zone outages easy; region outages require manual redeployment
 - CE: minimal cost compared to ZR

### Multi-region

<!-- TODO description -->

<!-- TODO diagram -->

| Architectural Concern | Impact |
|-|-|
| Reliability | TODO |
| Cost Optimization | TODO |
| Performance Efficiency | TODO |
| Operational Efficiency | TODO |

 - RE: very high (if configured correctly)
 - PE: depends on workload. Can support high-perf workloads though
 - OC: difficult
 - CE: high

## Example workloads

<!-- Considering adding a set of examples, with basic requirements, and an approach they can consider -->

- Intranet for a small business - cost is the primary factor, and business impact of downtime is very low. Single-region deployment, no use of zones.
- Line of business app for an enterprise - cost is a big factor; resiliency is important. Zone redundant.
- Legacy application that's extremely chatty - performance matters a lot; resiliency is important. Multi-zone using metro DR approach.
- Public sector application - data residency. Zone redundant.
- Bank - resiliency and very low risk tolerance. Zone redundant and multi-region.
- eCommerce app - geographically distributed user base. Multi-region.
- SaaS - geographically distributed user base. Multi-region.

<!-- TODO

When would zone redundancy not be advisable?
 - Mission-critical solutions with low risk tolerance.
 - Perf: low latency requirements. Consider zonal deployment.
 - When you deploy across multiple regions for other reasons (e.g. you're deploying to multiple regions to support a globally distributed user base)

-->