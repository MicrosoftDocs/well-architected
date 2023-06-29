---
title: Cost impact of Azure regions
description: Explore cost strategies for selecting Azure regions. Examine tradeoffs, compliance and regulatory issues, and traffic costs across billing zones and regions.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 04/06/2023
ms.topic: conceptual
ms.custom:
  - article
  - internal-intro
---

# Azure regions

The cost of an Azure service can vary between locations based on demand and local infrastructure costs. Consider the following geographical areas when choosing the location of your resources to estimate costs.

|Terminology|Description|
|---|---|
|Azure region|A set of data centers deployed within a latency-defined perimeter and connected through a dedicated regional low-latency network. |
|Availability zone |A unique physical location within a region with independent power, network, and cooling to be tolerant to data center failures through redundancy and logical isolation of services.|
|Billing zone|A geographic collection of regions that is used for billing.|
|Location|A region or a zone within a region. Azure has data centers all over the world and each data center resides in a location. |
|Landing zone|The ultimate location of your cloud solution or the landing zone. The landing zone typically consists of logical containers like a subscription and resource group, in which your cloud infrastructure components exist. |

See the [Azure global infrastructure](https://azure.microsoft.com/global-infrastructure) for a complete list of Azure geographies, regions, and locations.

To see product availability by region, see [Products available by region](https://azure.microsoft.com/global-infrastructure/services/).

## Tradeoff

- Locating resources in a cheaper region shouldn't negate the cost of network ingress and egress or by degraded application performance because of increased latency.
- Hosting an application in a single region can cost less than an application that's hosted across regions because of replication costs or the need for extra nodes.

## Compliance and regulatory
Azure offers differentiated cloud regions for specific security and compliance requirements.

**Does your solution need specific levels of security and compliance?**
 
If your solution needs to follow certain government regulations, the cost is higher. Otherwise you can meet less rigid compliance, through [Azure Policy](/azure/governance/policy/overview), which is free.

Certain Azure regions are built specifically for high compliance and security needs. For example, with [Azure Government (USA)](/azure/azure-government/) you're given an isolated instance of Azure. [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/) has data centers that meet privacy certifications. These specialized regions have higher cost.

Regulatory requirements can dictate restrictions on data residency. These requirements can change your data replication options for resiliency and redundancy.

## Traffic across billing zones and regions
Cross-regional traffic and cross-zonal traffic incur more costs.

**Is the application critical enough to have the footprint of the resources cross zones and,or cross regions?**

Bandwidth refers to data moving in and out of Azure data centers. Inbound data transfers, or data transfers going into Azure data centers, are free for most services. For outbound data transfers, the billing zone determines the data transfer pricing. For more information, see [Bandwidth Pricing Details](https://azure.microsoft.com/pricing/details/bandwidth/?cdn=disable).

Suppose you want to build a cost-effective solution by provisioning resources in locations that offer the lowest prices. The dependent resources and their users are located in different parts of the world. In this case, data transfers between locations add cost if there are meters tracking the volume of data moving across locations. The cost of transferring the data could offset any savings from choosing the cheapest location.
- The extra cross-regional and cross-zone costs don't apply to global services, such as Azure Active Directory.
- Not all Azure services support zones and not all regions in Azure support zones.

> ![Task](./images/i-best-practices.png) Before choosing a location, consider how important is the application to justify the cost of having resources cross zones and/or cross regions. For non-mission critical applications such as, developer or test, consider keeping the solution and its dependencies in a single region or single zone to leverage the advantages of choosing the lower-cost region.
