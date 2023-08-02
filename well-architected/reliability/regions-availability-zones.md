---
title: Design your solution to use availability zones and regions
description: A guide for understanding when to deploy workloads across availability zones or regions.
author: johndowns
ms.author: jodowns
ms.date: 08/03/2023
ms.topic: conceptual
ms.custom:
  - guide
categories:
  - management-and-governance
---

# Design your solution to use availability zones and regions

When you design a solution for Azure, you need to decide whether you deploy across multiple availability zones in a region, or whether you deploy into multiple regions. These decisions affect your solution's reliability, cost, and performance, and your team's ability to operate the solution. This guide provides information about the key business requirements that influence your decision, the approaches you can consider, the tradeoffs involved in each approach, and the impact of each approach on the core pillars of the Well-Architected Framework.

Your choice of how you use regions and availability zones affects several of the pillars of the Well-Architected Framework:

- **Reliability:** Your choice of deployment approach can help you to mitigate different types of risks. In general, by spreading your workload across a more geographically distributed area, you can achieve higher resiliency.
- **Cost Optimization:** Some architectural approaches require deploying more resources, which often incurs a resource cost. Other approaches involve sending data across geographically separated availability zones or regions, which might incur network traffic charges. It's also important to consider the ongoing cost of managing your resources, which is usually higher when you have a more complex architecture.
- **Performance Efficiency:** Occasionally, workloads can be highly sensitive to network latency. In these workloads, it's important to physically locate the components close together to minimize the latency when they communicate, which typically means deploying into a single availability zone. However, most workloads aren't highly latency sensitive, so this concern doesn't apply.
- **Operational Excellence:** A complex architecture takes more effort to deploy, configure, and manage. Additionally, for a highly available solution you might need to plan how you'll fail over to a secondary set of resources. Failover and failback can be complex, especially when manual steps are required.

However you design your solution, the **Security** pillar continues to apply. Usually, decisions about whether and how you use availability zones and regions doesn't change your security posture. Azure applies the same security rigor to every region and availability zone.

> [!TIP]
> For many production workloads, a [zone-redundant (spread) deployment](#deployment-approach-3-zone-redundant-spread-deployments) provides the best balance of tradeoffs. This approach can be extended with [asynchronous data backup to another region](#zone-redundant-spread-deployments-with-backup-across-regions).
>
> Consider other workload approaches when you need the specific benefits that those approaches bring, but it's important to be aware of the tradeoffs involved.

## Definitions

| Term | Definition |
|-|-|
| Region | A geographic perimeter that contains a set of data centers. |
| Data center | A facility that contains servers, networking equipment, and other hardware to support Azure resources and workloads. |
| Availability zone | A separated group of data centers within a region. Each availability zone is independent from the others. [Many regions support availability zones.][azure-regions-with-availability-zone-support] |
| Paired regions | [Some Azure regions][azure-region-pairs] are connected to another defined region to enable specific types of multi-region solutions. [Newer Azure regions aren't paired.][regions-with-availability-zones-and-no-region-pair] |
| Region architecture | The specific configuration of the Azure region, including the number of availability zones, and whether the region is paired with another region. |
| Non-zonal | A deployment model where a resource is deployed without reference to an availability zone. In a region that supports availability zones, the resource might be deployed anywhere within the region. |
| Zonal (pinned) | A deployment model where a resource is deployed into a specific availability zone. |
| Zone redundancy (spread) | A deployment model where a resource is deployed across multiple availability zones, and Microsoft manages data synchronization, traffic distribution, and failover in the event of a zone outage. |
| Multi-region | A deployment model where resources are deployed into multiple Azure regions. |
| Asynchronous replication | A data replication approach where data is written to one place. At a later time, the changes are replicated to another place. |
| Synchronous replication | A data replication approach where data is written to multiple places, which must each acknowledge completion of the write operation before the overall write is considered to be completed. |

## Your responsibility

### Understand Azure regions and availability zones

Azure's has a large global footprint. An Azure *region* is a geographic perimeter that contains a set of data centers.

Azure regions have a variety of configurations, which are also called *region architectures*.

Many Azure regions provide *availability zones*, which are separated groups of data centers. Within a region, each availability zone is close enough together to have very low-latency connections to other availability zones, but they're far enough apart to ensure that they have independent power, cooling, and networking infrastructure. Availability zones are designed so that if one zone has an outage, then regional services, capacity, and high availability are supported by the remaining zones.

The following diagram shows several example Azure regions. Regions 1 and 2 support availability zones.

:::image type="content" border="false" source="../_images/regions-availability-zones/regions-availability-zones.png" alt-text="Diagram showing data centers, availability zones and regions":::

If you deploy into an [Azure region that contains availability zones][azure-regions-with-availability-zone-support], then you can use multiple availability zones together. By using multiple avalability zones, you can keep separate copies of your application and data within separate physical data centers in a wide metropolitan area.

There are two ways to use availability zones within a solution:
- **Zonal** resources are pinned to a specific availability zone. You can combine multiple zonal deployments across different zones to achieve high reliability requirements. You're responsible for managing data replication and distributing requests across zones. If an outage occurs in a single availability zone, you're responsible for failover to another availability zone.
- **Zone-redundant** resources are spread across multiple availability zones. Microsoft manages spreading requests across zones, and the replication of data across zones, for you. If an outage occurs in a single availability zone, Microsoft manages failover automatically.

Different Azure services support one or both of these approaches. In general, PaaS services typically support zone-redundant deployments, and IaaS services typically support zonal deployments. For more information about how Azure services work with availability zones, see [Azure services with availability zone support][azure-services-with-availability-zone-support].

Many regions also have a [*paired region*][azure-region-pairs]. Paired regions support certain types of multi-region deployment approaches. Some newer regions have [multiple availability zones and don't have a paired region][regions-with-availability-zones-and-no-region-pair]. You can still deploy multi-region solutions into these regions, but the approaches you use might be different.

For more information about how Azure uses regions and availability zones, see [What are Azure regions and availability zones?][availability-zones-overview]

### Identify key business and workload requirements

To make an informed decision about how to use availability zones and regions in your solution, you need to understand your requirements. These requirements should be driven by discussions between solution designers and business stakeholders.

##### Risk tolerance

Different organizations have different risk appetites. Even within an organization, risk tolerance is often different for each workload. Most workloads don't need extreme high availability. But some workloads are so important that it's worth even mitigating risks that are unlikely to occur, like major natural disasters occurring across a wide geographic area.

The following table lists a few of the common risks that should be considered in a cloud environment:

| Risk | Example | Likelihood |
|-|-|-|
| Hardware outage | <ul><li>Issue with hard disk or networking equipment</li><li>Host reboots</li></ul> | High. Any resiliency strategy should account for these risks. |
| Data center outage | <ul><li>Power, cooling, or network failure across an entire data center</li><li>Natural disaster in one part of a metro area</li></ul> | Medium |
| Region outage | <ul><li>Major natural disaster that affects a wide geographical area</li></ul> | Low |

It would be ideal to mitigate every possible risk for every workload, but it's not practical or cost effective to do so. It's important to have an open discussion with business stakeholders so that you can make an informed decision about the risks you should mitigate.

> [!TIP]
> Generally, it's only worth mitigating the low-likelihood risks when you have mission-critical workloads. For example, banks, governments, and healthcare workloads often need to remain operational in all situations. For other workloads, the organization's risk tolerance usually is higher.

##### Resiliency requirements

It's important that you understand your resiliency requirements for your workload, including the recovery time objective (RTO) and recovery point objective (RPO). These objectives help you to decide which alternatives to rule out. If you don't have clear requirements, you can't make an informed decision about which approach to follow. For more information, see [Target functional and nonfunctional requirements](design-requirements.md). <!-- Update this to RE:01 when staged -->

##### Service level agreements

You should understand your solution's expected uptime service level agreement (SLA). For example, you might have a business requirement for your solution to meet 99.9% uptime.

Azure provides service level agreements (SLAs) for each service. An SLA indicates the level of uptime you should expect from the service, and the conditions you need to meet to achieve that expected SLA.

Your architectural decisions affect your solution's [composite SLA][composite-slas]. In general, the more redundancy you build into your solution, the higher your SLA is likely to be. Many Azure services provide higher SLAs when you deploy services in a zone-redundant or multi-region configuration. Review the SLAs for each of the Azure services you use to ensure that you understand how to maximize the resiliency and SLA of the service.

##### User location

If your users are geographically dispersed, it might make sense to deploy your workload across multiple regions. If your users are in one area, a single-region deployment can simplify your operational requirements and reduce your costs.

##### Budget

If you operate under a constrained budget, it's important to consider the costs involved in deploying redundant workload components. These costs can include additional resource charges, networking costs, and operational costs to manage more resources and a more complex environment. 

##### Complexity

It's a good practice to avoid unnecessary complexity in your solution architecture. The more complexity you introduce, the harder it is to reason about your architecture. Complex architectures are harder to operate, harder to secure, and are often less performant.

## How Azure facilitates your decisions

By providing regions and availability zones, Azure enables you to select a deployment approach that fits your needs. There are many different approaches that you can choose from, each of which provides benefits and has costs.

Suppose you're thinking about deploying a new solution, which includes an application that writes data to some sort of storage.

:::image type="content" border="false" source="../_images/regions-availability-zones/application.png" alt-text="Diagram showing a user connecting to an application, which connects to storage.":::

> [!NOTE]
> This example isn't specific to any particular Azure services. Instead, it's intended as a simple example to illustrate the fundamental concepts.

There are multiple ways that you can deploy this solution, which each provide a different set of benefits and costs. At a high level, you can consider a *non-zonal*, *zonal (pinned)*, *zone-redundant (spread)*, or *multi-region* deployment. The following table summarizes some of the approaches you can use and how they affect your architecture:

| Architectural Pillar | [Non-Zonal](#deployment-approach-1-non-zonal-deployments) | [Zonal (Pinned)](#deployment-approach-2-zonal-pinned-deployments) | [Zone-Redundant (Spread)](#deployment-approach-3-zone-redundant-spread-deployments) | [Multi-Region](#deployment-approach-4-multi-region-deployments) |
|-|-|-|-|
| Reliability | Low Reliability | Depends on Approach | High or Very High Reliability | High or Very High Reliability |
| Cost Optimization | Low Cost | Depends on Approach | Moderate Cost | High Cost |
| Performance Efficiency | Acceptable Performance (for most workloads) | High Performance | Acceptable Performance (for most workloads) | Depends on Approach |
| Operational Excellence | Low Operational Requirements | High Operational Requirements | Low Operational Requirements | High Operational Requirements |
| **Architectural Concern** | | | | |
| Compliance with Data Residency | High | High | High | [Depends on region][azure-region-pairs] |
| Regional Applicability | All regions | [Regions with availability zones][azure-regions-with-availability-zone-support] | [Regions with availability zones][azure-regions-with-availability-zone-support] | [Depends on region][regions-with-availability-zones-and-no-region-pair] |

The list of approaches provided in this article isn't exhaustive. You might choose to combine multiple approaches together, or use different approaches with different parts of your solution.

## Deployment approach 1: Non-zonal deployments

If you don't specify multiple availability zones or regions when you deploy your resources, then Azure doesn't make any guarantees about whether the resources are deployed into a single data center or split across multiple data centers in the region.

:::image type="content" border="false" source="../_images/regions-availability-zones/non-zonal.png" alt-text="Diagram showing the solution deployed into a single data center, within a single availability zone.":::

From a reliability perspective, if any part of the region experiences an outage then there's a chance that your workload might be impacted. If this happens, your solution might be unavailable, or your data could be lost.

For highly latency-sensitive workloads, this approach might also result in lower performance. Your workload components might not be colocated in the same data center, and so you might observe some latency for intra-region traffic. However, this isn't a concern for most workloads.

The single-region deployment model has the following effects on your architectural concerns:

| Architectural Pillar | Impact |
|-|-|
| Reliability | **Low reliability.** Services are subject to outages if a data center fails. However, an application can be built to be resilient to other types of failures. |
| Cost Optimization | **Lowest cost.** You only need to have a single instance of each resource, and you don't incur any inter-zone or inter-region bandwidth costs. |
| Performance Efficiency | *For most workloads:* **Acceptable performance.** This approach is likely to provide satisfactory performance.<br /><br />*For highly latency-sensitive workloads:* **Low performance.** Components aren't guaranteed to be located in the same availability zone, so highly latency-sensitive components might see lower performance. |
| Operational Excellence | **High operational efficiency.** You only have a single instance of each resource that needs to be managed. |
| **Architectural Concern** | |
| Compliance with Data Residency | **High.** When you deploy a solution that uses this approach, data is stored in the Azure region you select. |
| Regional Applicability | **High.** This approach can be used in any Azure region. |

### Non-zonal deployments with backup across regions

You can extend a non-zonal deployment by performing regular backups of your data to a secondary region. This approach adds an extra layer of protection to mitigate against an outage in your primary region.

:::image type="content" border="false" source="../_images/regions-availability-zones/non-zonal-backup-across-regions.png" alt-text="Diagram showing the solution deployed into a single data center, with backups going to another region.":::

When you implement this approach, consider your recovery time objective (RTO) and recovery point objective (RPO) carefully:

- **Recovery time:** If a regional outage does occur, you might need to rebuild your solution in another Azure region, which impacts your recovery time. Consider deploying your infrastructure as code so that you can quickly redeploy into another region during a major disaster. Ensure that your deployment tools and processes are just as resilient as your applications, so that you can use them to redeploy your solution even if you have an outage.
- **Recovery point:** Your backup frequency determines the amount of data loss you might experience (your recovery point). You can typically control the frequency of backups so that you can meet your RPO.

Adding cross-region backups to a single-region deployment model has the following effects on your architectural concerns:

| Architectural Pillar | Impact |
|-|-|
| Reliability | **Moderate reliability.**  Services are subject to outages if a data center fails. You can restore from a backup into another region if required, but recovery processes can be complex and it can take time to manually restore into the other region. |
| Cost Optimization | **Low cost.** Typically, adding a backup to another region only adds a small amount of extra cost compared to deploying a non-zonal resource. |
| Performance Efficiency | *For most workloads:* **Acceptable performance.** This approach is likely to provide satisfactory performance.<br /><br />*For highly latency-sensitive workloads:* **Low performance.** Components aren't guaranteed to be located in the same availability zone, so highly latency-sensitive components might see lower performance. |
| Operational Excellence | *During any outage within a region:* **Low operational efficiency.** Failover is the your responsibility and might require manual operations and redeployment. |
| **Architectural Concern** | |
| Compliance with Data Residency | **Depends on region selection.** Data is primarily stored in the Azure region you specify. However, you need to select another region to store your backups, so it's important that you select a region that's compatible with your data residency requirements. |
| Regional Applicability | **High.** This approach can be used in any Azure region. |

## Deployment approach 2: Zonal (pinned) deployments

In a *zonal* deployment, you specify that your resources should be deployed to a specific availability zone. Sometimes, this is called a *zone-pinned* deployment.

:::image type="content" border="false" source="../_images/regions-availability-zones/zonal-single-zone.png" alt-text="Diagram showing the solution deployed into a specific availability zone by using a zonal deployment approach.":::

A zonal approach reduces the latency in communicating between your components. However, by itself, it doesn't increase the resiliency of your solution. To increase your resiliency, you need to deploy multiple instances of your components into multiple availability zones.

:::image type="content" border="false" source="../_images/regions-availability-zones/zonal-multiple-zone.png" alt-text="Diagram showing the solution deployed into multiple availability zones by using a zonal (pinned) deployment approach.":::

When you use a zonal deployment model, you take on many responsibilities:

- You need to deploy resources to each availability zone, and configure and manage those resources individually.
- You need to decide how to replicate data between the availability zones, and then configure and manage the replication.
- You're responsible for distributing the requests to the correct resources, such as by using a load balancer. You need to decide whether to use an active/passive or an active/active request distribution model.
- In the event of an outage of an availability zone, you need to handle the failover to send traffic to resources in another availability zone.

> [!NOTE]
> An active/passive deployment across multiple availability zones is sometimes called [*Metro DR*][metro-dr].

A zonal deployment model has the following effects on your architectural concerns:

| Architectural Pillar | Impact |
|-|-|
| Reliability | *When deployed in a single availability zone:* **Low reliability.** A zonal deployment itself doesn't provide any resiliency to an outage in a data center or availability zone. You must deploy redundant resources across multiple availability zones to achieve high resiliency. <br /><br /> *When deployed in multiple availability zones:* **High reliability.** Services can be made resilient to an outage of a data center or availability zone. |
| Cost Optimization | *When deployed in a single availability zone:* **Low cost.** A single-zone deployment only requires a single instance of each resource. <br /><br /> *When deployed in multiple availability zones:* **High cost.** You deploy multiple instances of the resources, each of which are billed separately. You also need to pay for inter-zone traffic for data replication. |
| Performance Efficiency | **High performance.** Latency can be very low when the components that serve a request are located in the same availability zone. |
| Operational Excellence | **Low operational efficiency.** You have multiple instances of your service to configure and manage. Data must be replicated between availability zones. During an availability zone outage, failover is your responsibility. |
| **Architectural Concern** | |
| Compliance with Data Residency | **High.** When you deploy a solution that uses this approach, data is stored in the Azure region you select. |
| Regional Applicability | **Regions with availability zones.** This approach is available in any region that supports [availability zones][azure-regions-with-availability-zone-support]. |

This approach is typically used when you deploy virtual machine-based workloads. For a complete list of services that support zonal deployments, see [Availability zone service and regional support][azure-services-with-availability-zone-support].

## Deployment approach 3: Zone-redundant (spread) deployments

In this approach, your application tier is spread across multiple availability zones. When requests arrive, a load balancer built into the service (which itself spans availability zones) spreads the requests across the instances in each availability zone. If an availability zone has an outage, the load balancer moves the traffic to instances in the healthy availability zones.

Your storage tier is also spread across multiple availability zones. Copies of your application's data are distributed across multiple availability zones by using *synchronous replication*. When the application makes a change to the data, the storage service writes the change to multiple availability zones before it considers the transaction to be completed. That way, each availability zone always has an up-to-date copy of the data. If an availability zone has an outage, another availability zone can be used to access the same data.

:::image type="content" border="false" source="../_images/regions-availability-zones/zone-redundant.png" alt-text="Diagram showing the solution deployed into multiple availability zones by using a zone-redundant deployment approach.":::

A zone-redundant approach increases your solution's resiliency to issues like data center outages. But because data is replicated synchronously, your application has to wait for the data to be written across multiple separate places that might be in different parts of a metropolitan area. For most applications, the latency involved in inter-zone communication is negligible. However, for some highly latency-sensitive workloads, synchronous replication across availability zones might affect the application's performance.

A zone-redundant deployment model has the following effects on your architectural concerns:

| Architectural Pillar | Impact |
|-|-|
| Reliability | **High reliability.** Services are resilient to an outage of a data center or availability zone. Data is synchronously replicated across availability zones and with no delay. |
| Cost Optimization | **Moderate cost.** Depending on the services you use, you might see some costs for higher service tiers to enable zone redundancy, or some inter-zone networking costs. |
| Performance Efficiency | *For most workloads:* **Acceptable performance.** This approach is likely to provide satisfactory performance.<br /><br />*For highly latency-sensitive workloads:* **Low performance.** Some components might be sensitive to latency due to inter-zone traffic. |
| Operational Excellence | **High operational efficiency.** You typically need to only manage a single logical instance of each resource. For most services, during an availability zone outage, failover is Microsoft's responsibility and happens automatically. |
| **Architectural Concern** | |
| Compliance with Data Residency | **High.** When you deploy a solution that uses this approach, data is stored in the Azure region you select. |
| Regional Applicability | **Regions with availability zones.** This approach is available in any region that supports [availability zones][azure-regions-with-availability-zone-support]. |

This approach is possible with many Azure services, including virtual machine scale sets, App Service, Azure Functions, AKS, Azure Storage, Azure SQL, Service Bus, and many others. For a complete list of services that support zone redundancy, see [Availability zone service and regional support][azure-services-with-availability-zone-support].

### Zone-redundant (spread) deployments with backup across regions

You can extend a zone-redundant deployment by performing regular backups of your data to a secondary region. This approach gives you the benefits of a zone-redundant approach, with an added layer of protection to mitigate the extremely unlikely event of a full region outage.

:::image type="content" border="false" source="../_images/regions-availability-zones/zone-redundant-backup-across-regions.png" alt-text="Diagram showing the solution deployed into multiple availability zones by using a zone-redundant deployment approach, with backups going to another region.":::

When you implement this approach, it's important to consider your recovery time objective (RTO) and recovery point objective (RPO) carefully:

- **Recovery time:** If a regional outage does occur, you might need to rebuild your solution in another Azure region, which impacts your recovery time. Consider deploying your infrastructure as code so that you can quickly redeploy into another region during a major disaster. Ensure that your deployment tools and processes are just as resilient as your applications, so that you can use them to redeploy your solution even if you have an outage.
- **Recovery point:** Your backup frequency determines the amount of data loss you might experience (your recovery point). You can typically control the frequency of backups so that you can meet your RPO.

> [!TIP]
> This approach often gives a good balance of all of the architectural concerns. If you aren't sure which approach to select, start with this type of deployment.

Adding cross-region backups to a zone redundant deployment model has the following effects on your architectural concerns:

| Architectural Pillar | Impact |
|-|-|
| Reliability | **Very high reliability.** Services are resilient to an outage of a data center or availability zone. For most services, data is replicated across zones automatically and with no delay. Data is backed up asynchronously to a geographically separated region to mitigate the minimal risk of a full region outage. |
| Cost Optimization | **Moderate cost.** Typically, adding a backup to another region only adds a small amount of extra cost compared to implementing zone redundancy. |
| Performance Efficiency | *For most workloads:* **Acceptable performance.** This approach is likely to provide satisfactory performance.<br /><br />*For highly latency-sensitive workloads:* **Low performance.** Some components might be sensitive to latency due to inter-zone traffic. |
| Operational Excellence | *During an availability zone outage:* **High operational efficiency.** Failover is Microsoft's responsibility and happens automatically. <br /><br /> *During a regional outage:* **Low operational efficiency.** Failover is your responsibility and might require manual operations and redeployment. |
| **Architectural Concern** | |
| Compliance with Data Residency | **Depends on region selection.** Data is primarily stored in the Azure region you specify. However, you need to select another region to store your backups, so it's important that you select a region that's compatible with your data residency requirements. |
| Regional Applicability | **Regions with availability zones.** This approach is available in any region that supports [availability zones][azure-regions-with-availability-zone-support]. |

## Deployment approach 4: Multi-region deployments

You can use multiple Azure regions together to distribute your solution across a wide geographical area. You can use a multi-region approach to improve your solution's reliability or to support geographically distributed users. If data residency is an important concern for your solution, carefully consider which regions you use to ensure that your data is only stored in locations that fit your requirements.

### Active and passive regions

Multi-region architectures are complex, and there are many different ways you can design a multi-region solution. For some workloads, it makes sense to have multiple regions actively processing requests simultaneously (active/active deployments). For other workloads, it's better to designate one *primary region*, and use one or more *secondary regions* for failover purposes (active/passive deployments). This section focuses on the second scenario, where one region is active and another is passive. For information about active/active multi-region solutions, see the [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp) and the [Geode pattern](/azure/architecture/patterns/geodes).

### Data replication

Communicating across regions is much slower than communicating within a region. In general, the greater the geographic distance between two regions, the higher the network latency is. See [Azure network round-trip latency statistics][round-trip-latency] for the expected network latency when connecting between two regions. Cross-region network latency can significantly affect how your solution is designed, because you need to carefully consider whether how latency affects data replication and other transactions. For many solutions, a cross-region architecture requires *asynchronous* replication in order to minimize the effect of cross-region traffic on your application's performance.

##### Asynchronous data replication

Asynchronous replication across regions means that your application doesn't wait for all regions to acknowledge a change. After the change is commited in the primary region, the application considers the transaction to be completed. At some point later, the change is replicated to the secondary regions. This approach means that inter-region connection latency doesn't directly affect the application performance. However, because of the delay in replication, a region-wide outage might result in some data loss. This data loss is because a region might have an outage after a write was completed but before the change could be replicated.

:::image type="content" border="false" source="../_images/regions-availability-zones/multi-region-asynchronous.png" alt-text="Diagram showing the solution deployed into multiple regions, with data replication happening asynchronously.":::

A multi-region deployment model with asynchronous data replication has the following effects on your architectural concerns:

| Architectural Pillar | Impact |
|-|-|
| Reliability | **High reliability.** Solution is resilient to an outage of a data center, availability zone, or an entire region. Data is replicated but might not be synchronous, so some data loss is possible in a failover scenario.  |
| Cost Optimization | **High cost.** Separate resources must be deployed in each region, and each resource incurs cost to deploy and maintain. Data replication across regions might also incur significant cost. |
| Performance Efficiency | **High performance.** Appliction requests don't require cross-region traffic, so traffic is typically low latency. |
| Operational Excellence | **Low operational efficiency.** You need to deploy and manage resources across multiple regions. You're also responsible for failover between regions during a regional outage. |
| **Architectural Concern** | |
| Compliance with Data Residency | **Depends on region selection.** This approach requires you to select multiple regions for your workload to run in. It's important that you select regions that are compatible with your data residency requirements. |
| Regional Applicability | [Many Azure regions][azure-region-pairs] are paired. Some Azure services use paired regions to replicate data automatically. If you run your workload in a [region without a pair][regions-with-availability-zones-and-no-region-pair], you might need to use a [different approach to replicate your data](#region-architectures). |

##### Synchronous data replication

A synchronous multi-region solution means that your application has to wait for write operations to be completed in each Azure region before the transaction is completed. The latency involved in waiting for write operations depends on the distance between the regions. For many workloads, inter-region latency can make synchronous replication too slow to be useful.

:::image type="content" border="false" source="../_images/regions-availability-zones/multi-region-synchronous.png" alt-text="Diagram showing the solution deployed into multiple regions, with data replication happening synchronously.":::

A multi-region deployment model with synchronous data replication has the following effects on your architectural concerns:

| Architectural Pillar | Impact |
|-|-|
| Reliability | **Very high reliability.** Solution is resilient to an outage of a data center, availability zone, or an entire region. Data is always in sync across regions, so even if a complete region loss occurs, your data is available and complete in another region. |
| Cost Optimization | **High cost.** Separate resources must be deployed in each region, and each resource incurs cost to deploy and maintain. Data replication across regions might also incur significant cost. |
| Performance Efficiency | **Low performance.** Appliction requests require cross-region traffic. Depending on the distance between the regions, synchronous replication might add significant latency to the request. |
| Operational Excellence | **Low operational efficiency.** You need to deploy and manage resources across multiple regions. You're also responsible for failover between regions during a regional outage. |
| **Architectural Concern** | |
| Compliance with Data Residency | **Depends on region selection.** This approach requires you to select multiple regions for your workload to run in. It's important that you select regions that are compatible with your data residency requirements. |
| Regional Applicability | [Many Azure regions][azure-region-pairs] are paired. Some Azure services use paired regions to replicate data automatically. If you run your workload in a [region without a pair][regions-with-availability-zones-and-no-region-pair], you might need to use a [different approach to replicate your data](#region-architectures). |

### Region architectures

When you design a multi-region solution, consider whether the Azure regions you plan to use are paired or not.

You can build a multi-region solution even when the regions aren't paired. However, the approaches you use to achieve a multi-region architecture might be different. For example, in Azure Storage, you can use geo-redundant storage (GRS) with paired regions. If GRS isn't available, consider using features like Azure Storage [object replication](/azure/storage/blobs/object-replication-overview), or design your application to write to multiple regions.

### Combine multi-zone and multi-region approaches

You can also combine multi-zone and multi-region approaches. For example, you might deploy zone-redundant components into each region and also configure replication between the regions. This type of approach can be very complex to configure and it can be costly, but for some solutions it provides a very high degree of reliability.

## Example use cases

This section includes describes some common use cases, and the key requirements that each workload typically needs to consider. Based on the requirements and the approaches described in this article, we provide a suggested deployment approach for the workload.

### Line of business application for an enterprise

Contoso is a large manufacturing company. They are implementing a line of business application to manage aspects of their financial processes.

**Business requirements:** The information that the system manages is difficult to replace, so it's important that data is persisted reliably. Their architects have explained that their recovery point objective (RPO) is zero. Contoso's employees will use this system throughout their workday, so high performance is important so that their team members aren't kept waiting. Cost is also a concern, because the finance team has to pay for this solution themselves.

**Suggested approach:** [Zone-redundant deployment](#deployment-approach-3-zone-redundant-spread-deployments), or [Zone-redundant deployment with backup across regions](#zone-redundant-spread-deployments-with-backup-across-regions).

### Internal application

Fourth Coffee is a small business. They're developing a new internal application for their employees to use to submit timesheets.

**Business requirements:** For this workload, cost efficiency is a primary concern. Fourth Coffee has evaluated the business impact of downtime and has decided the application doesn't need to prioritize resiliency or performance. They accept the risk that an outage in an Azure availability zone or region might mean that their application is temporarily unavailable.

**Suggested approach:** [Non-zonal deployment](#deployment-approach-1-non-zonal-deployments).

### Legacy application migration

Fabrikam is migrating a legacy application from an on-premises data center into Azure, using a virtual machine-based (IaaS) approach. The application wasn't designed for a cloud environment, and communication between the application tier and the database is very [chatty](/azure/architecture/antipatterns/chatty-io/).

**Business requirements:** Fabrikam emphasize the importance of performance for this application. Resiliency is also important, though, and they need the application to continue to work even if an Azure data center has an outage.

**Suggested approach:** [Zonal (pinned) deployment (metro DR)](#deployment-approach-2-zonal-pinned-deployments).

### Healthcare application

Lamna Healthcare Company is implementing a new electronic health record system in Azure.

**Business requirements:** Because of the nature of the data that this solution stores, data residency is critically important. Lamna Healthcare Company operates under a strict regulatory framework that mandates that data must remain in a specific location.

**Suggested approach:** Lamna might consider a [Zone-redundant (spread) deployment](#deployment-approach-3-zone-redundant-spread-deployments), or [Zone-redundant (spread) deployment with backup across regions](#zone-redundant-spread-deployments-with-backup-across-regions). They could also consider a [Multi-region deployment](#deployment-approach-4-multi-region-deployments) if there are multiple regions that fit their data residency requirements.

### Banking system

Woodgrove Bank runs their core banking operations from a large solution deployed to Azure.

**Business requirements:** This is a mission-critical system. Any outages can cause major financial impact for customers. As a result, Woodgrove Bank has very low risk tolerance. They need the highest level of reliability possible, and they need to mitigate the risk of any failures that are possible to mitigate.

**Suggested approach:** [Multi-region deployment](#deployment-approach-4-multi-region-deployments). It's important that they select regions that fit their data residency requirements.

### Software as a service (SaaS)

Proseware, Inc. builds software used by companies across the world. Their user base is widely geographically distributed.

**Business requirements:** Proseware needs to enable their customers to choose a deployment regions close to them. Enabling customer region choice is important for both latency and for end customers’ data sovereignty requirements.

**Suggested approach:** [Multi-region deployment](#deployment-approach-4-multi-region-deployments).

## Related links

Review some of the reference architectures and example scenarios for multi-zone and multi-region solutions:
- [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant)
- [Highly available multi-region web application](/azure/architecture/web-apps/app-service/architectures/multi-region)
- [Multi-region N-tier application](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
- [Multi-tier web application built for HA/DR](/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)

<!-- Links -->

[availability-zones-overview]: </azure/reliability/availability-zones-overview>
[azure-regions-with-availability-zone-support]: </azure/reliability/availability-zones-service-support#azure-regions-with-availability-zone-support>
[azure-services-with-availability-zone-support]: </azure/reliability/availability-zones-service-support#azure-services-with-availability-zone-support>
[azure-region-pairs]: </azure/reliability/cross-region-replication-azure>
[regions-with-availability-zones-and-no-region-pair]: </azure/reliability/cross-region-replication-azure#regions-with-availability-zones-and-no-region-pair>
[metro-dr]: </azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery>
[round-trip-latency]: </azure/networking/azure-network-latency>
[composite-slas]: <business-metrics.md#composite-slas>
