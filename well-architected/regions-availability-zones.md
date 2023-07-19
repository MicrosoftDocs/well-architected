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

When you architect a solution for Azure, you need to decide which region to use, whether you deploy across multiple availability zones in a region, and whether you deploy into multiple regions. These decisions affect your solution's reliability, cost, performance, and operational excellence. This guide provides information about the approaches you can consider, the tradeoffs involved in each approach, and the impact of each approach on the core pillars of the Well-Architected Framework.

> [!TIP]
> For many production customer workloads, a [single-region, zone-redundant deployment](#single-region-zone-redundant-services) provides the best balance of tradeoffs. You can consider other workload approaches when you need the specific benefits that those approaches bring, but it's important to be aware of the tradeoffs involved.

## Regions and availability zones

Azure's global footprint includes over 60 announced regions. A *region* is a geographic perimeter that contains a set of data centers.

Many Azure regions provide *availability zones*, which are separated groups of data centers. Within a region, each availability zone is close enough together to have very low latency connections to other availability zones, while being far enough apart to ensure that they have independent power, cooling, and networking infrastructure. Availability zones are designed so that if one zone is affected, regional services, capacity, and high availability are supported by the remaining zones.

![Diagram showing data centers, availability zones and regions](/azure/reliability/media/availability-zones.png)

For more information, see [What are Azure regions and availability zones?](/azure/reliability/availability-zones-overview)

## Understand tradeoffs

Most solutions can be designed in many different ways. Each approach has advantages and disadvantages. You can only make an informed decision about which deployment approach best fits your needs if you understand all of the following elements:

- Your business and workload requirements.
- The deployment approaches and how they work.
- The tradeoffs involved in each approach.

To understand why you need to make tradeoffs, consider how data replication works. Suppose you're thinking about deploying a new solution with an application that writes data to some sort of storage. If you want to achieve high resiliency, you might choose to write the data to multiple places. Select each tab to learn about some of the options, and the tradeoffs involved.

> [!NOTE]
> The examples below aren't specific to any storage service in Azure. They're intended as simple examples to illustrate the basic concepts.

#### [Single-zone](#tab/single-zone)

If you use a service that isn't zone-aware, then your data is likely to be stored within a single data center. However, if that data center has an outage, your data might be unavailable or lost.

#### [Multi-zone](#tab/multi-zone)

When you use a zone-redundant storage service, copies of your application's data are distributed across multiple availability zones by using *synchronous replication*. When the data changes, the write operation happens synchronously to multiple copies of the data simultaneously.

This approach increases your solution's resiliency to issues like data center outages.

Because data is replicated synchronously, your application has to wait for the data to be written across multiple separate places that might be in different parts of a metropolitan area. For most applications, the latency involved in inter-zone communication is negligible. However, for some highly latency-sensitive workloads, synchronous replication might affect the application's performance.

#### [Multi-region synchronous](#tab/multi-region-synchronous)

When you use a geo-redundant storage service, multiple copies of the data are stored in separate Azure regions.

Your data can remain available even if an entire Azure region is offline.

A synchronous multi-region solution means that your application has to wait for write operations to be completed in each Azure region before the transaction is completed. The latency involved in waiting for write operations depends on the distance between the regions. See [Azure network round-trip latency statistics](/azure/networking/azure-network-latency) for the expected network latency when connecting between two regions.

For many workloads, inter-region latency can make synchronous replication too slow.

#### [Multi-region asynchronous](#tab/multi-region-asynchronous)

When you use a geo-redundant storage service, multiple copies of the data are stored in separate Azure regions.

Your data can remain available even if an entire Azure region is offline.

Asynchronous replication across regions means that your application doesn't wait for all regions to acknowledge a change. Once the change is commited in one region, the application considers the transaction to be completed. At some point later, the change is replicated between the two regions. This approach means that inter-region connection latency doesn't affect the application performance. However, because of the delay in replication, a region wide outage might result in some data loss. This data loss is because a region might have an outage after a write was completed but before the change could be replicated.

---

In the preceding example, you need to decide how you'll trade off between reliability and performance, and select the appropriate option for your needs. However, reliability and performance are only two of the tradeoffs you need to consider. Your choice of how you use regions and availability zones affects many of the pillars of the Well-Architected Framework:

- **Reliability:** Your choice of deployment approach can help you to mitigate different types of risks. In general, the more geographically distributed a workload is, the more resilient it can be.
- **Cost Optimization:** Some architectural approaches require deploying more resources, which often incurs a resource cost. Other approaches involve sending data across geographically separated availability zones or regions, which might incur network traffic charges. It's also important to consider the ongoing cost of managing your resources, which is usually higher when you have a more complex architecture.
- **Performance Efficiency:** Occasionally, workloads can be highly sensitive to network latency. In these workloads, it's important to physically locate the components close together to minimize the latency when they communicate, which typically means deploying into a single availability zone. However, most workloads aren't highly latency sensitive, so this concern doesn't apply.
- **Operational Excellence:** A complex architecture takes more effort to deploy and manage. Additionally, for a highly available solution you might need to plan how you'll fail over to a secondary set of resources. Failover and failback can be complex, especially when manual steps are required.

<!-- TODO -->

To integrate somewhere:

- **How zone redundancy works:** For many services, Azure runs multiple instances or stores multiple copies of the data. Zone redundancy means that these are distributed across availability zones (i.e. DCs) that are in the same metro area but separated. So you don't necessarily provision more resources, you're just spreading them across physical locations. This helps with resiliency. The data transfer to keep instances in sync can have a cost impact but not as high as multi-region. Might increase latency of communication between components, but this doesn't matter for most workloads.


## Key business and workload requirements

To make an informed decision about which approach works for your solution, you need to understand your requirements. These requirements should be driven by discussions between solution designers and business stakeholders.

### Risk tolerance

Different organizations have different risk appetites. Even within an organization, risk tolerance is often different for each workload. Most workloads don't need extreme high availability. But some workloads are so important that it's worth even mitigating risks that are unlikely to occur, like major natural disasters occurring across a wide geographic area.

The following table lists some common risks that should be considered in a cloud environment:

| Risk | Example | Likelihood |
|-|-|-|
| Hardware outage | <ul><li>Issue with hard disk or networking equipment</li><li>Host reboots</li></ul> | Common. Any resiliency strategy should account for these risks. |
| Data center outage | <ul><li>Power, cooling, or network failure across an entire data center</li><li>Natural disaster in one part of a metro area</li></ul> | Infrequent |
| Region outage | <ul><li>Major natural disaster that affects a wide geographical area</li></ul> | Very infrequent |

*should we also include service outages? e.g. global service outage for something like Azure Front Door or Entra ID. Need to look to mission-critical guidance for this level of risk mitigation* <!-- TODO -->

Every organization would ideally like to mitigate every possible risk, but it's not practical or cost effective to do so. It's important to have an open discussion with business stakeholders so that you can make an informed decision about the risks you should mitigate.

> [!TIP]
> Generally, only customers with mission-critical workloads choose to mitigate risks with low likelihood. For example, banks, governments, and healthcare workloads often need to remain operational in all situations. For other workloads, the organization's risk tolerance usually is higher.

### Resiliency requirements

It's important that you understand your resiliency requirements for your workload, including the recovery time objective (RTO) and recovery point objective (RPO). These objectives help you to decide which alternatives to rule out. If you don't have clear requirements, you can't make an informed decision about which approach to follow.

TODO Link to WAF guidance about these requirements

### User location

If your users are geographically dispersed, it might make sense to deploy your workload across multiple regions. If your users are in one area, a single-region deployment can simplify your operational requirements and reduce your costs.

### Budget

If you operate under a constrained budget, it's important to consider the costs involved in redundant workload components. These costs can include additional resource charges, networking costs, and operational costs to manage more resources and a more complex environment. 

### Complexity

It's a good practice to avoid unnecessary complexity in your solution architecture. The more complexity you introduce, the harder it is to reason about your architecture. Complex architectures are harder to operate, harder to secure, and are often less performant.

## Common deployment approaches

### Single region, no zone redundancy

The simplest deployment approach is to deploy a solution into a single region, and not to enable any availability zone-related features, such as zone redundancy or zonal deployments. While this approach is the simplest to deploy and manage, it also 

Diagram

| Architectural Concern | Impact |
|-|-|
| Reliability | **Low reliability.** Services are subject to outages if a data center fails. Application can be built to be resilient to other types of failures. |
| Cost Optimization | **Lowest cost.** Likely to only have a single instance of each resource, and no inter-zone or inter-region bandwidth costs. |
| Performance Efficiency | **For most workloads:** TODO.<br /><br />**For highly latency-sensitive workloads:** Components aren't guaranteed to be located in the same availablity zone, so highly latency-sensitive components might see lower performance. |
| Operational Excellence | **Low operational burden.** You only have a single instance of each resource that needs to be managed. |

### Single region, zone redundant services

Description

To understand which services support zone redundant deployments, see [Availability zone service and regional support](/azure/reliability/availability-zones-service-support).

Diagram

Examples of services that handle this easily - AppGW, VNets, Azure Firewall, App Service, Cosmos DB, Service Bus

| Architectural Concern | Impact |
|-|-|
| Reliability | **High reliability.** Services are resilient to an outage of a data center or availability zone. For most services, data is replicated across zones automatically and with no delay. |
| Cost Optimization | **Moderate cost.** Depending on the services you use, you might see some costs for higher service tiers to enable zone redundancy, or some inter-zone networking costs. |
| Performance Efficiency | **For most workloads:** TODO.<br /><br />**For highly latency-sensitive workloads:** Some components might be sensitive to latency due to inter-zone data replication. |
| Operational Excellence | **Low operation burden.** You only have a single instance of each resource that needs to be managed. During an availability zone outage, failover is Microsoft's responsibility and happens automatically. |

### Single region, zonal across multiple zones

Description

To understand which services support zonal deployments, see [Availability zone service and regional support](/azure/reliability/availability-zones-service-support).

Diagram

Examples of services that can be deployed zonally - VMs, load balancers

| Architectural Concern | Impact |
|-|-|
| Reliability | **High reliability.** If services are distributed across availability zones, they are resilient to an outage of a data center or availability zone. |
| Cost Optimization | **High cost.** |
| Performance Efficiency | **For most workloads:** TODO.<br /><br />**For highly latency-sensitive workloads:** Latency can be very low when components are located in the same availability zone. |
| Operational Excellence | **High operational burden.** Data must be replicated between service instances. During an outage, failover is the customer's responsibility. |

This is also called metro DR

### Single region, zone redundant services, with backup across regions

Description

Diagram

| Architectural Concern | Impact |
|-|-|
| Reliability | **Very high reliability.** Services are resilient to an outage of a data center or availability zone. For most services, data is replicated across zones automatically and with no delay. Data is replicated asynchronously to a geographically separated region for even higher resiliency. |
| Cost Optimization | **Moderate cost.** TODO minimal cost compared to ZR |
| Performance Efficiency | TODO |
| Operational Excellence | **Moderate operational burden.** During an availability zone outage, failover is Microsoft's responsibility and happens automatically. During a regional outage, failover is the customer's responsibility and might require manual operations and redeployment. |

### Multi-region

Description

Diagram

| Architectural Concern | Impact |
|-|-|
| Reliability | **Very high reliability.** Solution is resilient to an outage of a data center, availability zone, or an entire region. Data is replicated but might not be synchronous, so some data loss is possible in a failover scenario.  |
| Cost Optimization | **High cost.** Separate resources must be deployed in each region, and each resource incurs cost to deploy and maintain. Data replication across regions might also incur significant cost. |
| Performance Efficiency | TODO |
| Operational Excellence | **High operational burden.** Resources across two regions must be operated and maintained. Customer is responsible for failover between regions during a regional outage. |

## Example workloads

- Intranet for a small business - cost is the primary factor, and business impact of downtime is very low. Single-region deployment, no use of zones.
- Line of business app for an enterprise - cost is a big factor; resiliency is important. Zone redundant.
- Legacy application that's extremely chatty - performance matters a lot; resiliency is important. Multi-zone using metro DR approach.
- Public sector application - data residency. Zone redundant.
- Bank/healthcare - resiliency and very low risk tolerance. Zone redundant and multi-region.
- SaaS - geographically distributed user base. Multi-region.
