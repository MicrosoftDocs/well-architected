---
title: Carrier-grade workloads
description: Get started with an overview of carrier-grade workloads.
author: mikedell73
ms.author: mikedell73
ms.date: 06/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.industry: telecommunications
products: 
- azure-app-service
- azure-traffic-manager
- cosmos-db
ms.custom:
  - carrier-grade
---

# Carrier-grade workloads

Carrier Grade is a generalized term applied to solutions across a range of safety-critical verticals, including:

- telecommunications
- defense
- power stations
- large-scale manufacturing
- oil refineries

It's synonymous with [Mission Critical](../mission-critical/mission-critical-overview.md) or Business Critical. These industries all have a fundamental requirement to be operational with only minutes or even seconds of downtime per calendar year. Failure to achieve this uptime requirement can result in extensive loss of life, incur significant fines, or contractual penalties.

Carrier-grade industries are precise and specific about what *operational* means for them. *Operational* includes how it's measured, and what targets these industries must meet or exceed. Customers will select their hyper-scale partner based on which one can best support their critical workloads, assuming such support is available.

This section of the [Microsoft Azure Well-Architected Framework](/azure/architecture/framework) strives to address the challenges of building carrier-grade workloads on Azure.

To support such workloads, this section details key service changes and presents a series of carrier-grade design patterns. These changes and design patterns are based on the real-world application so that owners of Azure services and those designing other carrier-grade applications can:

- Understand the special requirements and architectures carrier-grade applications are likely to have, and how those applications will use their services.
- Reuse the same design patterns where applicable.
- Promote alignment around a common set of Azure services, which have been enhanced to support the demands of carrier-grade workloads.

## What is a carrier-grade workload?

A carrier-grade workload requires highly reliable systems for handling emergency (E911) calls and includes the verticals mentioned in [carrier-grade-workloads](#carrier-grade-workloads). Although initially reliant on dedicated, proprietary hardware systems, these verticals have evolved to use commercial bare-metal servers, virtual machines, and most recently, on-premises VMware or OpenStack on-premises clouds.

In each vertical, there are one or more of the following underlying requirements for high availability:

- Minimize loss of life or injury.
- Meet regulatory requirements on reliability to avoid paying fines.
- Optimize service to customers to minimize churn to competitors.
- Meet contractual SLAs with business or government customers.

High availability or highly reliable systems target `0.001%` downtime in a year, which is around five (5) minutes. This downtime covers all forms and causes of outages, including:

- Scheduled maintenance
- Infrastructure failure
- Human error
- Software issues
- Natural disaster

Telecommunication companies consistently deliver services achieving `≤ 5` minutes of downtime per year, and in many cases, achieve `≤ 30` seconds of downtime due to unscheduled outages.

Carrier-grade industries use consistent vocabulary to define exactly how reliability should be measured and how to define reliability objectives.

## What are the common challenges?

[Microsoft Azure Availability Zones](/azure/architecture/high-availability/building-solutions-for-high-availability) do an excellent job of reducing the risk of outage due to hardware failure or localized environmental issues. However, it's a common misconception that the use of availability zones (AZs) is enough to achieve carrier-grade availability.

Building carrier-grade workloads that are highly reliable on the platform remains a challenge for the following reasons:

- AZs are designed so that the network latency between any two zones in a single region is `≤ 2 ms`. AZs can't be widely and geographically dispersed. Since they can't be dispersed, the AZs share a correlated risk of failure due to natural disasters, such as flooding or massive power outages, which could disable multiple AZs within a region.
- Many Azure services are explicitly designed to be zone-redundant so that the applications using them don't need explicit logic to benefit from the availability gain. This redundancy function within the service requires collaboration between the elements in each zone. There's often an unavoidable risk of software failure in one zone that causes correlated failures in other zones. For example, any issue with a secret or certificate used with the zone redundant service will impact all AZs simultaneously.

## Is carrier-grade only about reliability?

While the primary focus of carrier-grade workloads is [Reliability](/azure/architecture/framework/#reliability), other pillars of the Well-Architected Framework are equally important when building and operating a carrier-grade workload on Azure.  

- [Security](/azure/architecture/framework/security/): How a workload mitigates security threats, such as Distributed Denial of Service (DDoS) attacks, will have a significant bearing on overall reliability.

- [Operational Excellence](/azure/architecture/framework/devops/): How a workload is able to effectively respond to operational issues will have a direct impact on application availability.

- [Performance Efficiency](/azure/architecture/framework/scalability/): Availability is more than simple uptime, but rather a consistent level of application service and performance relative to a known healthy state.

Achieving high reliability imposes significant cost tradeoffs, which may not be justifiable for every workload scenario. It's recommended that design decisions be driven by business requirements.

## What are the key design areas?

When building a carrier-grade application or a critical service, consider the following main design points:

- **Fault tolerance and recovery process**&mdash;Application design must allow for unavoidable failures so that the application, as a whole, can continue to operate at some level. The design must minimize points of failure and take a federated approach.
- **Data model**&mdash;Create a quantitative and probabilistic model of various failure modes, which clearly identifies the application's key dependencies. The model must show that the application can achieve the necessary availability given those dependencies meet their own Service Level Objectives (SLOs).

  Carrier Grade availability requires that the application is deployed across multiple regions. This deployment requires careful thought about how
  the application's data will be managed across those regions.  
  
  Databases can provide three key properties for the data they manage for an application, known as *CAP*:

  - Consistency
  - Availability
  - Partition tolerance  

  The CAP theorem states that a database layer can't provide all three of these properties for the same data at the same time. The architect needs to make explicit design decisions about which properties to favor under which conditions, and how to deal with the edge cases.

- **Telemetry**&mdash;High availability requires careful health monitoring to automatically detect and respond to issues within seconds. This monitoring requires built-in telemetry of key dependencies to reliably detect the failure.
- **Testing and support**&mdash;Nothing is ever perfect. The design and implementation of the application must be tested. The integration and deployment of the application as a whole solution must be tested. Testing finds and resolves issues before they become potentially life threatening.

  Testing should include:
  - Chaos testing on production systems to avoid testing bias.
  - A process, which continues for the lifetime of the application, since the deployment environment is complex and multi-layered.

  Highly available systems rely on high-quality support teams, who are able to:

  - Rapidly respond to and resolve issues in the field.
  - Conduct root cause analysis.
  - Discover systematic design flaws.

## Illustrative examples

The guidance provided within the Carrier-grade workload series is based on a solution-oriented approach to illustrate key design considerations and recommendations.

### Reference architecture

The architecture described is the first A4O Carrier Grade reference architecture and applies to an application purely hosted in Azure. The reference architecture is based on a proven real-world application and represents Microsoft's expert opinion on the best way to design and realize an application.

This opinion is based on extensive experience designing, implementing, and supporting carrier-grade applications for the world's largest telecommunications companies.

The architecture is presented from three different viewpoints:

- **Logical**&mdash;The set of generic building blocks and how they relate to each other.
- **Physical**&mdash;How those generic building blocks are realized in the Azure cloud.
- **Operational**&mdash;How the application is deployed and managed.

The reference application follows a pool-based service architecture. The application delivers service to a large population of subscribers and maintains a subscriber database that tracks unique information for each subscriber. The application allows for different types of clients connecting to the server using different protocols, potentially for different classes of server operations that the client requests.

The carrier-grade reference architecture applies where the following scenarios are true:

- Carrier-grade reliability is necessary and justified, since it comes with more costs.
- Application function can be provided from the core cloud. This scenario wouldn't be the case if, for example, latency budgets require function to be delivered at the edge.
- The application isn't required to maintain active session state relating to in-flight message processing if there's a failure.
- Application logic can accept an eventual consistency data replication model with distributed processing pools, as opposed to an application that requires global synchronization of its data with a single point of control.
- There are no regulatory or similar requirements that unnaturally constrain where the application can be deployed. This scenario wouldn't be the case where a regulator requires that data remain within a specific country, but that country only contains a single cloud region.

The overall architecture model can be transferred to the following different application areas, although you may need to adapt the specific data model and method of data access:

- Telecommunication voicemail solution. This application area is the specific use case used to develop this generic reference architecture.
- Banking applications for retail banking.
- Retail applications, such as stock control or transaction tracking.
- Manufacturing and industrial control applications, such as video analysis for QA on a production line.

### Reference implementation: Mobile Voice Mail (MVM)

Mobile Voice Mail is a carrier-grade voicemail solution running in Azure. By the end of 2022, it will serve millions of mobile phone subscribers to one of the world's leading telecommunication giants. Voicemail is a beachhead workload. It's the first core network workload that this industry leader will move into Azure. Success will lead to this telecommunication leader onboarding many more critical workloads into Azure.

### MVM design overview

MVM is architected to achieve `5*9s` of reliability by running as 12 active/active service instances across four regions. Traffic is load-balanced using app-specific protocols combined with the use of a domain name system (DNS) and Asynchronous transfer mode (ATM). Shared data is replicated between regions by [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/#overview) so that each subscriber's configuration and messages can be read and written locally by each service instance, as depicted in the following screenshot:

![Carrier-grade Mobile Voice Mail data replication by Cosmos DB](./images/carrier-grade-cosmosdb.png "Mobile Voice Mail (MVM) data replication using Cosmos DB")

Subscriber data and message metadata are held in Cosmos DB and replicated across all regions. The messages are written to blob storage and replicated into just two regions for cost efficiency. If any service instance or its local dependencies fail, traffic is transparently routed to another service instance. Even if individual service instances are relatively unreliable, the overall system remains highly available as long as failures are independent.

The 12 service instances (SIs) are managed as interchangeable, limited lifetime entities. The service instances are auto-created based on declarative configuration. Any configuration or software changes are validated in the production network using test traffic before slowly adding them to the service pool using weighting. Old instances are slowly removed from the pool using weighting adjustments and decommissioned once inactive.

### MVM telemetry

MVM tracks and reports the number of successful and failed subscriber actions (VM deposit/collection). Those statistics are held in the application's own statistics and monitoring service instance. From there, the application can track the failure rate of user actions as a percentage of total operations attempted.

All outages are tracked. Both the downtime performance measurement (DPM) and Outage Frequency Measurement (OFM) are normalized by the number of impacted subscribers. The DPM and OFM are continuously maintained and reported for a range of time periods up to and including yearly. MVM also tracks the total failure rate to allow monitoring for low-level background problems.

For diagnostic purposes, the application will also define and track similar Key Performance Indicators (KPIs) for the critical services that it's dependent on.

## Next step

Start by reviewing the design methodology for carrier-grade application scenarios.

> [!div class="nextstepaction"]
> [Design methodology](carrier-grade-design-methodology.md)