---
title: Target functional and nonfunctional requirements
description: Learn about reliability targets for availability, recovery, and nonfunctionality, which involve application and data platforms, networking, and connectivity.
author: martinekuan
ms.author: martinek
ms.date: 08/03/2023
ms.topic: conceptual
---

# Target functional and nonfunctional requirements

Target functional and nonfunctional requirements include *availability targets* and *recovery targets*. These requirements allow you to measure the uptime and downtime of your workloads. Having clearly defined targets is crucial so that you have a goal to work and measure against. There are many other requirements that you should consider that improve reliability requirements and meet business expectations.

*Resiliency* means the ability to recover from failures. *Availability* means to run in a healthy state without significant downtime. Building resiliency and availability into your apps begins with gathering requirements. For example, how much downtime is acceptable and how much does potential downtime cost your business?

## Key points

- Determine the acceptable level of uptime for your workloads.
- Determine how long workloads can be unavailable and how much data is acceptable to lose during a disaster.
- Consider application and data platform requirements to improve resiliency and availability.
- Ensure connection availability and improve reliability with Azure services.
- Assess overall application health of workloads.

## Availability targets

A service-level agreement (SLA) is an availability target that represents a commitment around performance and availability of the application. Understanding the SLA of individual components within the system is essential to define reliability targets. Knowing the SLA of dependencies also provides a justification for extra spending when making the dependencies highly available and with proper support contracts. Availability targets for any dependencies used by the application should be understood and ideally align with application targets should also be considered.

Understanding your availability expectations is vital to reviewing overall operations for the application. If you want to achieve an application service-level objective (SLO) of 99.999%, the level of inherent operational action required by the application is going to be far greater than if an SLO of 99.9% is the goal.

Monitoring and measuring application availability is vital to qualifying overall application health and progress towards defined targets. Make sure you measure and monitor key targets such as:

- Mean Time Between Failures (MTBF). The average time between failures of a particular component.
- Mean Time To Recover (MTTR). The average time it takes to restore a component after a failure.

### Considerations for availability targets

Keep the following questions in mind.

#### Are SLAs/SLOs/SLIs for all applied dependencies understood?

Availability targets for any dependencies used by the application should be understood and ideally align with application targets. Make sure SLAs/SLOs/service level indicators (SLIs) for all applied dependencies are understood.

#### Has a composite SLA been calculated for the application and key scenarios using Azure SLAs?

A composite SLA captures the end-to-end SLA across all application components and dependencies. It's calculated using the individual SLAs of Azure services hosting application components. A composite SLA provides an important indicator of designed availability in relation to customer expectations and targets. Make sure that the composite SLA of all components and dependencies on the critical paths are understood. For more information, see [Composite SLAs](./business-metrics.md#composite-slas).

> [!NOTE]
> If you have contractual commitments to an SLA for your Azure solution, allowances on top of the Azure composite SLA must be made to accommodate outages caused by code-level issues and deployments. This fact is often overlooked. You might directly put the composite SLA forward to your customers.

#### Are availability targets considered while the system runs in disaster recovery mode?

Availability targets might or might not be applied when the system runs in disaster recovery mode. It depends on the application. If targets must also apply in a failure state, an *N+1 model* should be used to achieve greater availability and resiliency. In this scenario, N is the capacity needed to deliver required availability. There's also a cost implication. More resilient infrastructure is usually more expensive.

#### What are the consequences if availability targets aren't satisfied?

Are there any penalties, such as financial charges, associated with failing to meet SLA commitments? Other measures can be used to prevent penalties, but that also brings extra cost to operate the infrastructure. This fact has to be factored in and evaluated. The consequences if availability targets aren't satisfied should be fully understood. This approach also informs when to initiate a failover case.

## Recovery targets

Recovery targets identify how long the workload can be unavailable and how much data is acceptable to lose during a disaster. Define target reports for the application and key scenarios. Target reports needed are:

- Recovery Time Objective (RTO). The maximum acceptable time an application is unavailable after an incident.
- Recovery Point Objective (RPO). The maximum duration of data loss that is acceptable during a disaster.

Recovery targets are nonfunctional requirements of a system and should be dictated by business requirements. Recovery targets should be defined in accordance to the required RTO and RPO targets for the workloads.

## Meet application platform requirements

Azure application platform services offer resiliency features to support application reliability. They might only apply for a certain SKU and configuration or deployment. For example, they might apply if an SLA is dependent on the number of instances deployed or a certain feature enabled.

We recommend that you review the SLA for services used. For example, the Service Bus Premium SKU provides predictable latency and throughput to mitigate noisy neighbor scenarios. It also lets you automatically scale and replicate metadata to another Service Bus instance for failover purposes.

For more information, see [Service Bus Premium and Standard messaging tiers](/azure/service-bus-messaging/service-bus-premium-messaging).

### Use availability zones

Many Azure regions provide *availability zones*, which are separated groups of data centers. Within a region, each availability zone is close enough together to have very low-latency connections to other availability zones, but they're far enough apart to ensure that they have independent power, cooling, and networking infrastructure. Availability zones are designed so that if one zone has an outage, then regional services, capacity, and high availability are supported by the remaining zones.

If you deploy into an [Azure region that contains availability zones](/azure/reliability/availability-zones-service-support#azure-regions-with-availability-zone-support), then you can use multiple availability zones together. By using multiple avalability zones, you can keep separate copies of your application and data within separate physical data centers in a wide metropolitan area.

There are two ways to use availability zones within a solution:

- **Zonal resources** are pinned to a specific availability zone. You can combine multiple zonal deployments across different zones to achieve high reliability requirements. You're responsible for managing data replication and distributing requests across zones. If an outage occurs in a single availability zone, you're responsible for failover to another availability zone.
- **Zone-redundant resources** are spread across multiple availability zones. Microsoft manages spreading requests across zones, and the replication of data across zones, for you. If an outage occurs in a single availability zone, Microsoft manages failover automatically.

Different Azure services support one or both of these approaches. In general, PaaS services typically support zone-redundant deployments, and IaaS services typically support zonal deployments. For more information about how Azure services work with availability zones, see [Azure services with availability zone support](/azure/reliability/availability-zones-service-support#azure-services-with-availability-zone-support).

There are performance and cost considerations where applications are extremely *chatty* across zones given the physical separation and inter-zone bandwidth charges.

For more information about designing for availability zones and other ways to achieve resiliency in Azure, see [Building solutions for high availability using availability zones](/azure/architecture/high-availability/building-solutions-for-high-availability) and [Business continuity with data resiliency](https://azurecomcdn.azureedge.net/cvt-27012b3bd03d67c9fa81a9e2f53f7d081c94f3a68c13cdeb7958edf43b7771e8/mediahandler/files/resourcefiles/azure-resiliency-infographic/Azure_resiliency_infographic.pdf).

### Use multiple Azure regions

If you have a mission-critical solution, you might consider deploying it across multiple Azure regions. However, if you don't explicitly need a multi-region design, then it's cheaper and less complex to use availability zones instead. For most workloads, availability zones provide the best balance of tradeoffs between resiliency, performance, cost, and complexity. Multi-region deployment is an extra measure that you can consider if the SLAs given by a single-region multi-zone configuration are insufficient, or if your users are geographically spread out and it makes sense to have instances of your workload near your user bases.

The ability to respond to disaster scenarios for overall compute platform availability and application reliability depends on the use of multiple regions or other deployment locations.

Many regions also have a [*paired region*](/azure/reliability/cross-region-replication-azure). Paired regions support certain types of multi-region deployment approaches, such as native replication features like geo-redundant storage (GRS) asynchronous replication. Some newer regions have [multiple availability zones and don't have a paired region](/azure/reliability/cross-region-replication-azure#regions-with-availability-zones-and-no-region-pair). You can still deploy multi-region solutions into these regions, but the approaches you use might be different.

If you plan maintenance, updates to a region are performed sequentially. For more information, see [Cross-region replication in Azure](/azure/best-practices-availability-paired-regions).

### Considerations for availability

There are several considerations for availability.

#### Is the application hosted across two or more application platform nodes?

To ensure application platform reliability, it's vital that the application is hosted across at least two nodes. This approach ensures that there are no single points of failure. Ideally an N+1 model should be applied for compute availability where N is the number of instances required to support application availability and performance requirements.

> [!NOTE]
> Higher SLAs provided for virtual machines and associated related platform services require at least two replica nodes deployed to either an availability set or across two or more availability zones. For more information, see [SLA for Virtual Machines](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services).

#### How is the client traffic routed to the application for region, availability zone, or network outage?

If there's a major outage, client traffic should be routable to application deployments that remain available across other regions or availability zones. This situation is ultimately where cross-premises connectivity and global load balancing should be used, depending on whether the application is internal or external facing. Services such as Azure Front Door, Azure Traffic Manager, or third-party content delivery networks can route traffic across regions based on application health discovered by using health probes. For more information, see [Traffic Manager endpoint monitoring](/azure/traffic-manager/traffic-manager-monitoring).

## Meet data platform requirements

Data and storage services should be running in a highly available configuration or SKU. Azure data platform services offer reliability features to support application reliability. They might only be applicable at a certain SKU. Examples are Azure SQL Database Business Critical SKUs and Azure Storage Zone Redundant Storage (ZRS) with three synchronous replicas spread across availability zones.

### Data consistency

Data types should be categorized by data consistency requirements. Data consistency requirements, such as strong or eventual consistency, should be understood for all data types and used to inform data grouping and categorization. Consistency requirements should inform what data replication and synchronization strategies can be considered to meet application reliability targets.

CAP theorem proves that it's impossible for a distributed data store to simultaneously provide more than two guarantees across the following measures:

- *Consistency*. Every read receives the most recent write or an error.
- *Availability*. Every request receives a nonerror response, without the guarantee that it contains the most recent write.
- *Partition tolerance*. A system continues to operate despite an arbitrary number of transactions being dropped or delayed by the network between nodes.

Determining which of these guarantees are most important in the context of application requirements is critical.

### Replication and redundancy

Replicating data across availability zones or regions supports application availability objectives to limit the effect of failure scenarios. The ability to restore data from a backup is essential when recovering from data corruption situations and failure scenarios. To ensure sufficient redundancy and availability during availability zone and regional failure scenarios, backups should be stored across availability zones or regions.

Define and test a data restore process to ensure a consistent application state. Regular testing of the data restore process promotes operational excellence and confidence in the ability to recover data in alignment with defined recovery objectives for the application.

Consider how your application traffic is routed to data sources when there's a region, availability zone, or network outage. Understanding the method used to route application traffic to data sources if there's a major failure event is critical to identify whether failover processes meet recovery objectives. Many Azure data platform services offer native reliability capabilities to handle major failures, such as Azure Cosmos DB Automatic Failover and Azure SQL Database active geo-replication.

> [!NOTE]
> Some capabilities, such as Azure Storage read-access geo-redundant storage and Azure SQL DB active geo-replication, require application-side failover to alternate endpoints in some failure scenarios. Application logic should be developed to handle these scenarios.

## Networking and connectivity requirements

Consider these guidelines to ensure connection availability and improve reliability with Azure services.

### Connectivity

- Use a global load balancer to distribute traffic and failover across regions.

  Azure Front Door, Azure Traffic Manager, or third-party content delivery network services can be used to direct inbound requests to external-facing application endpoints deployed across multiple regions. Traffic Manager is a DNS-based load balancer, so failover must wait for DNS propagation to occur. A sufficiently low time-to-live (TTL) value should be used for DNS records, though not all ISPs honor this setting. For application scenarios that require transparent failover, Azure Front Door should be used. For more information, see [Disaster Recovery using Azure Traffic Manager](/azure/networking/disaster-recovery-dns-traffic-manager) and [Routing architecture overview](/azure/frontdoor/front-door-routing-architecture).

- For cross-premises connectivity, by using Azure ExpressRoute or VPN, ensure that there are redundant connections from different locations.

  At least two redundant connections should be established across two or more Azure regions and peering locations to ensure there are no single points of failure. An active/active load-shared configuration provides path diversity and promotes availability of network connection paths. For more information, see [Cross-network connectivity](/azure/expressroute/cross-network-connectivity).

- Simulate a failure path to ensure that connectivity is available over alternative paths.

  The failure of a connection path onto other connection paths should be tested to validate connectivity and operational effectiveness. Using site-to-site VPN connectivity as a backup path for ExpressRoute provides an extra layer of network resiliency for cross-premises connectivity. For more information, see [Using site-to-site VPN as a backup for ExpressRoute private peering](/azure/expressroute/use-s2s-vpn-as-backup-for-expressroute-privatepeering).

- Eliminate all single points of failure from the data path, both on-premises and hosted on Azure.

  Single-instance Network Virtual Appliances (NVAs) introduce significant connectivity risk, whether deployed in Azure or within an on-premises datacenter. For more information, see [Deploy highly available network virtual appliances](/azure/architecture/reference-architectures/dmz/nva-ha).

### Zone-aware services

- Use ExpressRoute or VPN zone-redundant virtual network gateways.

  Zone-redundant virtual network gateways distribute gateway instances across availability zones to improve reliability and ensure availability during failure scenarios that affect a datacenter in a region. For more information, see [About zone-redundant virtual network gateway in Azure availability zones](/azure/vpn-gateway/about-zone-redundant-vnet-gateways).

- If used, deploy Azure Application Gateway v2 in a zone-redundant configuration.

  Azure Application Gateway v2 can be deployed in a zone-redundant configuration to deploy gateway instances across zones for improved reliability and availability during failure scenarios that affect a datacenter in a region. For more information, see [Scaling Application Gateway v2 and WAF v2](/azure/application-gateway/application-gateway-autoscaling-zone-redundant).

- Use Azure Load Balancer Standard to load-balance traffic across availability zones.

  Azure Load Balancer Standard is zone-aware to distribute traffic across availability zones. It can also be configured in a zone-redundant configuration to improve reliability and ensure availability during failure scenarios that affect a datacenter within a region. For more information, see [Load Balancer and availability zones](/azure/load-balancer/load-balancer-standard-availability-zones).

- Configure health probes for Azure Load Balancer and Azure Application Gateways.

  Health probes allow Azure Load Balancer to assess the health of backend endpoints to prevent traffic from being sent to unhealthy instances. For more information, see [Azure Load Balancer health probes](/azure/load-balancer/load-balancer-custom-probe-overview).

- Assess critical application dependencies with health probes.

  Custom health probes should be used to assess overall application health including downstream components and dependent services, such as APIs and datastores. In this approach, traffic isn't sent to backend instances that can't successfully process requests due to dependency failures. For more information, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).

## Related links

- To understand business metrics to design resilient Azure applications, see [Using business metrics to design resilient Azure applications](./business-metrics.md).
- For information on availability zones, see [Build solutions for high availability using availability zones](/azure/architecture/high-availability/building-solutions-for-high-availability).
- For information on health probes, see [Azure Load Balancer health probes](/azure/load-balancer/load-balancer-custom-probe-overview) and [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).
- To learn about connectivity risk, see [Deploy highly available network virtual appliances](/azure/architecture/reference-architectures/dmz/nva-ha).

## Next steps

> [!div class="nextstepaction"]
> [Design reliable Azure applications](./app-design.md)

Go back to the main article: [Design for reliability](design-checklist.md)
