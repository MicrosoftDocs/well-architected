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

When you architect a solution for Azure, you need to decide whether you deploy across multiple availability zones in a region, or whether you deploy into multiple regions. These decisions affect your solution's reliability, cost, and performance, and your team's ability to operate the solution. This guide provides information about the approaches you can consider, the tradeoffs involved in each approach, and the impact of each approach on the core pillars of the Well-Architected Framework.

> [!TIP]
> For many production customer workloads, a single-region, multi-zone deployment provides the best balance of tradeoffs. You can consider other workload approaches when you need the specific benefits that those approaches bring, but it's important to be aware of the tradeoffs involved.

## Regions and availability zones

Azure's global footprint includes over 60 announced regions. A *region* is a geographic perimeter that contains a set of data centers.

Many Azure regions provide *availability zones*, which are separated groups of data centers. Within a region, each availability zone is close enough together to have very low latency connections to other availability zones, but they're far enough apart to ensure that they have independent power, cooling, and networking infrastructure. Availability zones are designed so that if one zone has an outage, then regional services, capacity, and high availability are supported by the remaining zones.

![Diagram showing data centers, availability zones and regions](/azure/reliability/media/availability-zones.png)

There are two ways to use availability zones within a solution:
- **Zonal** resources are confined to a specific availability zone.
- **Zone-redundant** resources are ... TODO

For more information, see [What are Azure regions and availability zones?](/azure/reliability/availability-zones-overview)

## How to select a deployment approach

Most solutions can be designed in many different ways. Each approach has advantages and disadvantages. You can only make an informed decision about which deployment approach best fits your needs if you understand all of the following elements:

- Your business and workload requirements.
- The deployment approaches that are available, and how they work.
- The tradeoffs involved in each approach.

Your choice of how you use regions and availability zones affects several of the pillars of the Well-Architected Framework:

- **Reliability:** Your choice of deployment approach can help you to mitigate different types of risks. In general, by spreading your workload across a more geographically distributed area, you can achieve higher resiliency.
- **Cost Optimization:** Some architectural approaches require deploying more resources, which often incurs a resource cost. Other approaches involve sending data across geographically separated availability zones or regions, which might incur network traffic charges. It's also important to consider the ongoing cost of managing your resources, which is usually higher when you have a more complex architecture.
- **Performance Efficiency:** Occasionally, workloads can be highly sensitive to network latency. In these workloads, it's important to physically locate the components close together to minimize the latency when they communicate, which typically means deploying into a single availability zone. However, most workloads aren't highly latency sensitive, so this concern doesn't apply.
- **Operational Excellence:** A complex architecture takes more effort to deploy and manage. Additionally, for a highly available solution you might need to plan how you'll fail over to a secondary set of resources. Failover and failback can be complex, especially when manual steps are required.

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

## Deployment approaches

Suppose you're thinking about deploying a new solution, which includes an application that writes data to some sort of storage.

<!-- TODO diagram -->

> [!NOTE]
> This example isn't specific to any particular Azure services. Instead, it's intended as a simple example to illustrate the fundamental concepts.

There are multiple ways that you can deploy this solution, which each provide a different set of benefits and costs. At a high level, you can consider a *non-zonal*, *multi-zone*, or *multi-region* deployment. Here's a summary of the options and how they affect your architectural considerations:

| Architectural Concern | Non-Zonal Approach | Zonal Approach | Zone-Redundant Approach | Multi-Region Approach |
|-|-|-|-|
| Reliability | Low | Depends on Approach | TODO | High or Very High |
| Cost Optimization | Low | Depends on Approach | TODO | High |
| Performance Efficiency | Acceptable or Low | Depends on Approach | TODO | Depends on Approach |
| Operational Excellence | High | Depends on Approach | TODO | High |

### Non-zonal deployments

If you don't use multiple availability zones or regions, then your data is likely to be stored within a single data center. In the unlikely event of a data center outage, your solution might be unavailable and your data could be lost.

<!-- TODO diagram -->

The single-region deployment model has the following effects on your architectural concerns:

| Architectural Concern | Impact |
|-|-|
| Reliability | **Low reliability.** Services are subject to outages if a data center fails. Application can be built to be resilient to other types of failures. |
| Cost Optimization | **Lowest cost.** You only need to have a single instance of each resource, and you don't incur any inter-zone or inter-region bandwidth costs. |
| Performance Efficiency | *For most workloads:* **Acceptable performance.** This approach is likely to provide satisfactory performance.<br /><br />*For highly latency-sensitive workloads:* **Low performance.** Components aren't guaranteed to be located in the same availablity zone, so highly latency-sensitive components might see lower performance. |
| Operational Excellence | **High operational efficiency.** You only have a single instance of each resource that needs to be managed. |

### Multi-zone deployments

If you deploy into an [Azure region that contains availability zones](/azure/reliability/availability-zones-service-support#azure-regions-with-availability-zone-support), then you can use multiple availability zones together to keep separate copies of your application and data within separate physical data centers in a wide metropolitan area.

Specific Azure resources support one or both of these approaches. For more information about how Azure services work with availability zones, see [Azure services with availability zone support](/azure/reliability/availability-zones-service-support#azure-services-with-availability-zone-support).

#### Zone redundant deployment approach

In this approach, your compute tier is deployed across multiple availability zones. When requests arrive, a load balancer (which itself spans availability zones) sends them to instances in any availability zone. If an availability zone has an outage, the load balancer moves the traffic to instances in the healthy availability zones.

Your storage tier is also deployed across multiple availability zones. Copies of your application's data are distributed across multiple availability zones by using *synchronous replication*. When the application makes a change to the data, the storage service writes the change to multiple availability zones before it considers the transaction to be completed. That way, each availability zone always has an up-to-date copy of the data. If an availabilty zone has an outage, another availability zone can be used to access the same data.

<!-- TODO diagram -->

A zone-redundant approach increases your solution's resiliency to issues like data center outages. But because data is replicated synchronously, your application has to wait for the data to be written across multiple separate places that might be in different parts of a metropolitan area. For most applications, the latency involved in inter-zone communication is negligible. However, for some highly latency-sensitive workloads, synchronous replication might affect the application's performance.

A zone-redundant deployment model has the following effects on your architectural concerns:

| Architectural Concern | Impact |
|-|-|
| Reliability | **High reliability.** Services are resilient to an outage of a data center or availability zone. Data is synchronously replicated across availability zones and with no delay. |
| Cost Optimization | **Moderate cost.** Depending on the services you use, you might see some costs for higher service tiers to enable zone redundancy, or some inter-zone networking costs. |
| Performance Efficiency | *For most workloads:* **Acceptable performance.** This approach is likely to provide satisfactory performance.<br /><br />*For highly latency-sensitive workloads:* **Low performance.** Some components might be sensitive to latency due to inter-zone traffic. |
| Operational Excellence | **High operation efficiency.** You typically need to only manage a single instance of each resource. For most services, during an availability zone outage, failover is Microsoft's responsibility and happens automatically. |

This approach is possible with many Azure services, including virtual machine scale sets, App Service, Azure Functions, AKS, Azure Storage, Azure SQL, Service Bus, and many others. For a complete list of services that support zone redundancy, see [Availability zone service and regional support](/azure/reliability/availability-zones-service-support).

#### Zonal deployment approach

This approach also uses multiple availability zones within a metropolitan area. However, instead of spreading instances or data across availability zones, you specify that an instance should be deployed to a specific availability zone. This is called a *zonal* deployment, or sometimes *zone-pinned* deployment.

A zonal approach reduces the latency in communicating between your components. However, by itself, it doesn't provide any redundancy or resiliency. To create a resilient solution, you need to deploy multiple instances of your components into multiple availability zones. You're responsible for replicating data between the availability zones. In the event of an outage of an availability zone, you need to handle the failover procedures. When you deploy zonally across multiple availability zones, this approach is sometimes called *Metro DR*.

<!-- TODO diagram -->

A zonal deployment model has the following effects on your architectural concerns:

| Architectural Concern | Impact |
|-|-|
| Reliability | *When deployed in a single availability zone:* **Low reliability.** A zonal deployment itself doesn't provide any resiliency to an outage in a data center or availability zone. You must deploy redundant resources across multiple availabilty zones to achieve high resiliency. <br /><br /> *When deployed in multiple availability zones:* **High reliability.** Services can be made resilient to an outage of a data center or availability zone. |
| Cost Optimization | *When deployed in a single availability zone:* **Low cost.** TODO. <br /><br /> *When deployed in multiple availability zones:* **High cost.** You deploy multiple instances of the resources, each of which are billed separately. You also need to pay for inter-zone traffic for data replication. |
| Performance Efficiency | **High performance.** Latency can be very low when the components that serve a request are located in the same availability zone. |
| Operational Excellence | **Low operational efficiency.** You have multiple instances of your service to configure and manage. Data must be replicated between availability zones. During an availability zone outage, failover is your responsibility. |

This approach is typically used when you deploy virtual machine-based workloads. For a complete list of services that support zonal deployments, see [Availability zone service and regional support](/azure/reliability/availability-zones-service-support).

### Multi-region deployments

You can use multiple Azure regions together to distribute your solution across a wide geographical area. You can use a multi-region approach to improve your solution's reliability or to support geographically distributed users. However, if data residency is an important concern for your solution, consider whether a multi-region approach will meet your requirements.

Communicating across regions is much slower than communicating within a region. In general, the longer the distance between two regions, the higher the network latency is. Cross-region network latency can significantly affect how your solution is architected, because you need to carefully consider whether how latency affects data replication and other transactions. For many solutions, a cross-region architecture requires *asynchronous* replication in order to minimize the effect of cross-region traffic on your applications.

#### Asynchronous data replication

TODO

<!-- TODO diagram -->

A multi-region deployment model with asynchronous data replication has the following effects on your architectural concerns:

| Architectural Concern | Impact |
|-|-|
| Reliability | **High reliability.** Solution is resilient to an outage of a data center, availability zone, or an entire region. Data is replicated but might not be synchronous, so some data loss is possible in a failover scenario.  |
| Cost Optimization | **High cost.** Separate resources must be deployed in each region, and each resource incurs cost to deploy and maintain. Data replication across regions might also incur significant cost. |
| Performance Efficiency | **High performance.** Appliction requests don't require cross-region traffic, so traffic is typically low latency. |
| Operational Excellence | **Low operational efficiency.** Resources across two regions must be operated and maintained. Customer is responsible for failover between regions during a regional outage. |

#### Synchronous data replication

TODO

<!-- TODO diagram -->

A multi-region deployment model with synchronous data replication has the following effects on your architectural concerns:

| Architectural Concern | Impact |
|-|-|
| Reliability | **Very high reliability.** Solution is resilient to an outage of a data center, availability zone, or an entire region. Data is always in sync across regions, so even if a complete region loss occurs, your data is available and complete in another region. |
| Cost Optimization | **High cost.** Separate resources must be deployed in each region, and each resource incurs cost to deploy and maintain. Data replication across regions might also incur significant cost. |
| Performance Efficiency | **Low performance.** Appliction requests require cross-region traffic. Depending on the distance between the regions, synchronous replication might add significant latency to the request. |
| Operational Excellence | **Low operational efficiency.** Resources across two regions must be operated and maintained. Customer is responsible for failover between regions during a regional outage. |

### Hybrid approaches

#### Zone-redundant and cross-region

TODO

#### Zone-redundant with backup across regions

TODO

| Architectural Concern | Impact |
|-|-|
| Reliability | **Very high reliability.** Services are resilient to an outage of a data center or availability zone. For most services, data is replicated across zones automatically and with no delay. Data is backed up asynchronously to a geographically separated region to mitigate the minimal risk of a full region outage. |
| Cost Optimization | **Moderate cost.** TODO minimal extra cost on top of ZR |
| Performance Efficiency | TODO |
| Operational Excellence | **Moderate operational burden.** During an availability zone outage, failover is Microsoft's responsibility and happens automatically. During a regional outage, failover is the customer's responsibility and might require manual operations and redeployment. |

<!-- 
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

In the preceding example, you need to decide how you'll trade off between reliability and performance, and select the appropriate option for your needs. However, reliability and performance are only two of the tradeoffs you need to consider.



To integrate somewhere:

- **How zone redundancy works:** For many services, Azure runs multiple instances or stores multiple copies of the data. Zone redundancy means that these are distributed across availability zones (i.e. DCs) that are in the same metro area but separated. So you don't necessarily provision more resources, you're just spreading them across physical locations. This helps with resiliency. The data transfer to keep instances in sync can have a cost impact but not as high as multi-region. Might increase latency of communication between components, but this doesn't matter for most workloads.


This is also called metro DR

-->

## Example workloads

- Intranet for a small business - cost is the primary factor, and business impact of downtime is very low. Single-region deployment, no use of zones.
- Line of business app for an enterprise - cost is a big factor; resiliency is important. Zone redundant.
- Legacy application that's extremely chatty - performance matters a lot; resiliency is important. Multi-zone using metro DR approach.
- Public sector application - data residency. Zone redundant.
- Bank/healthcare - resiliency and very low risk tolerance. Zone redundant and multi-region.
- SaaS - geographically distributed user base. Multi-region.
