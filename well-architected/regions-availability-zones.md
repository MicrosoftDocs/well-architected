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

When you architect a solution for Azure, you need to decide which region to use, whether you deploy across multiple availabilty zones in a region, and whether you deploy into multiple regions. These decisions affect your solution's reliability, cost, performance, and operational efficiency. This guide provides information about the approaches you can consider, the tradeoffs involved in each approach, and the impact of each approach on the core pillars of the Well-Architected Framework.

> [!TIP]
> For many production customer workloads, a [single-region, zone-redundant deployment](#single-region-zone-redundant-services) provides the best balance of tradeoffs. You can consider other workload approaches when you need the specific benefits that those approaches bring, but it's important to be aware of the tradeoffs involved.

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

*should we also include service outages? e.g. global service outage for something like Azure Front Door or Entra ID. Need to look to mission-critical guidance for this level of risk mitigation*

While every organization would ideally like to mitigate every possible risk, it's not practical or cost effective to do so. It's important to have an open discussion with business stakeholders so that you can make an informed decision about the risks you should mitigate.

> [!TIP]
> Generally, only customers with mission-critical workloads choose to mitigate risks with low likelihood. For example, banks, governments, and healthcare workloads typically have a high degree of importance and need to remain operational in all situations. For other workloads, the organization's risk tolerance usually is higher.

### Resiliency requirements

It's important that you understand your resiliency requirements for your workload, including the RTO and RPO. If you don't have clear requirements, you can't make an informed decision about which approach to follow.

Link to WAF guidance about these requirements

### User location

If your users are geographically dispersed, it might make sense to deploy your workload across multiple regions. If your users are in one area, a single-region deployment can simplify your operational requirements and reduce your costs.

### Budget

If you operate under a constrained budget, it's important to consider the costs involved in redundant workload components. These costs can include additional resource charges, networking costs, and operational costs to manage more resources and a more complex environment. 

### Complexity

It's a good practice to avoid unnecessary complexity in your solution architecture. The more complexity you introduce, the harder it is to reason about your architecture. Complex architectures are harder to operate, harder to secure, and are often less performant.

## Key tradeoffs

Deciding on the best deployment architecture for your requirements means that you need to consider tradeoffs. Four of the pillars of the well-architected framework are 

- **Reliability:** Your choice of deployment architecture mitigates different types of risks. In general, the more geographically distributed a workload is, the more resilient it can be.
- **Cost Optimization:** Some architectural approaches require deploying more resources, which incurs a resource cost. Others involve sending data across geographically separated availability zones or regions, which incurs bandwidth costs.
- **Performance Efficiency:** Some workloads are highly latency-sensitive, TODO  Only really a concern for highly latency-sensitive workloads. Most workloads don't fit into this category.
- **Operational Efficiency:** Management and failover



To integrate somewhere:

- **Replication:** If you deploy across regions, you need to replicate data between them. Synchronous replication means you take a perf hit on every transaction, but then everything is consistent. Async replication means there's a chance data loss occurs if an outage happens before data is replicated.
- **How zone redundancy works:** For many services, Azure runs multiple instances or stores multiple copies of the data. Zone redundancy means that these are distributed across availability zones (i.e. DCs) that are in the same metro area but separated. So you don't necessarily provision more resources, you're just spreading them across physical locations. This helps with resiliency. The data transfer to keep instances in sync can have a cost impact but not as high as multi-region. Might increase latency of communication between components, but this doesn't matter for most workloads.

## Approaches

### Single region, no zone redundancy

Description

Diagram

| Architectural Concern | Impact |
|-|-|
| Reliability | **Low reliability.** Services are subject to outages if a data center fails. Application can be built to be resilient to other types of failures. |
| Cost Optimization | **Lowest cost.** Likely to only have a single instance of each resource, and no inter-zone or inter-region bandwidth costs. |
| Performance Efficiency | <ul><li>**For most workloads:** TODO.</li><li>**For highly latency-sensitive workloads:** Components aren't guaranteed to be located in the same availablity zone, so highly latency-sensitive components might see lower performance.</li></ul>|
| Operational Efficiency | **Low operational burden.** You only have a single instance of each resource that needs to be managed. |

### Single region, zone redundant services

Description

Diagram

| Architectural Concern | Impact |
|-|-|
| Reliability | **High reliability.** Services are resilient to an outage of a data center or availability zone. For most services, data is replicated across zones automatically and with no delay. |
| Cost Optimization | **Moderate cost.** Depending on the services you use, you might see some costs for higher service tiers to enable zone redundancy, or some inter-zone networking costs. |
| Performance Efficiency | <ul><li>**For most workloads:** TODO.</li><li>**For highly latency-sensitive workloads:** Some components might be sensitive to latency due to inter-zone data replication.</li></ul> |
| Operational Efficiency | **Low operation burden.** You only have a single instance of each resource that needs to be managed. During an availability zone outage, failover is Microsoft's responsibility and happens automatically. |

### Single region, zonal across multiple zones

Description

Diagram

| Architectural Concern | Impact |
|-|-|
| Reliability | **High reliability.** If services are distributed across availability zones, they are resilient to an outage of a data center or availability zone. |
| Cost Optimization | **High cost.** |
| Performance Efficiency | **For most workloads:** TODO.<br /><br />**For highly latency-sensitive workloads:** Latency can be very low when components are located in the same availability zone. |
| Operational Efficiency | **High operational burden.** Data must be replicated between service instances. During an outage, failover is the customer's responsibility. |

This is also called metro DR

### Single region, zone redundant services, with backup across regions

Description

Diagram

| Architectural Concern | Impact |
|-|-|
| Reliability | **Very high reliability.** Services are resilient to an outage of a data center or availability zone. For most services, data is replicated across zones automatically and with no delay. Data is replicated asynchronously to a geographically separated region for even higher resiliency. |
| Cost Optimization | **Moderate cost.** TODO minimal cost compared to ZR |
| Performance Efficiency | TODO |
| Operational Efficiency | **Moderate operational burden.** During an availability zone outage, failover is Microsoft's responsibility and happens automatically. During a regional outage, failover is the customer's responsibility and might require manual operations and redeployment. |

### Multi-region

Description

Diagram

| Architectural Concern | Impact |
|-|-|
| Reliability | **Very high reliability.** Solution is resilient to an outage of a data center, availability zone, or an entire region. Data is replicated but might not be synchronous, so some data loss is possible in a failover scenario.  |
| Cost Optimization | **High cost.** Separate resources must be deployed in each region, and each resource incurs cost to deploy and maintain. Data replication across regions might also incur significant cost. |
| Performance Efficiency | TODO |
| Operational Efficiency | **High operational burden.** Resources across two regions must be operated and maintained. Customer is responsible for failover between regions during a regional outage. |

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