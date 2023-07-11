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

## Key decision points

To make an informed decision about which approach works for your solution, consider the following factors:

### Risk tolerance

Different organizations have different risk appetites, and risk tolerance is often different for each workload. Some workloads are so important that it's worth even mitigating risks that are unlikely to occur, like major natural disasters occurring across a wide geographic area. Other workloads are less critical.

The following table lists some common risks that should be considered in a cloud environment:

| Risk | Example | Frequency |
|-|-|-|
| Node/host/instance/rack outage | Issue with hard disk or networking equipment; host reboot | Fairly common. Any resiliency strategy should account for these risks. |
| Data center outage | Power, cooling, or network failure in a data center; natural disaster in one part of a metro area | Infrequent |
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

Deciding on the best architecture for your requirements means that you need to consider tradeoffs and make informed decisions
The major tradeoffs involved in this decision are:
- Reliability: Different degree of reliaiblity based on risks that are being mitigated.
- Performance: Only really a concern for highly latency-sensitive workloads. Most workloads don't fit into this category.
- Operational complexity: Management and failover
- Cost: Some architectural approaches require deploying more resources, which incurs a resource cost. Others involve sending data across availability zones or regions, which incurs bandwidth costs.

--

## Options and tradeoffs

Single region, not zone redundant:
- RE: lower
- PE: high
- OC: easy
- CE: cheapest

Single region, zone redundant:
- RE: high
- PE: usually fine, but latency-sensitive workloads might have isses
- OC: easy
- CE: usually has some cost

Single region, zonal across multiple zones:
Note only makes sense for very specific use cases
 - RE: high (if configured correctly)
 - PE: co-located resources mean latency can be very low
 - OC: high, need to plan failover
 - CE: higher cost

This is also called metro DR

Single region, zone redundant, with backup across regions:
 - RE: very high
 - PE: usually fine, but latency-sensitive workloads might have isses
 - OC: zone outages easy; region outages require manual redeployment
 - CE: minimal cost compared to ZR


 Multi region
 - RE: very high (if configured correctly)
 - PE: depends on workload. Can support high-perf workloads though
 - OC: difficult
 - CE: high

--

When would zone redundancy not be advisable?
 - Mission-critical solutions with low risk tolerance.
 - Perf: low latency requirements. Consider zonal deployment.
 - When you deploy across multiple regions for other reasons (e.g. you're deploying to multiple regions to support a globally distributed user base)
