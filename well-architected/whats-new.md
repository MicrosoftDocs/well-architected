---
title: What's new in the Azure Well-Architected Framework
description: Find out what's new in the Azure Well-Architected Framework.
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# What's new in the Azure Well-Architected Framework

## November 2023

The Azure Well-Architected Framework completed a significant content refresh across all five pillars. We're breaking from our standard "What's new" format this month because the changes that launched with [Microsoft Ignite 2023](https://ignite.microsoft.com/) go beyond bullet points.

### The core pillars of architecture have been restructured

All five pillars of the Well-Architected Framework now follow a common structure that consists exclusively of design principles, design review checklists, tradeoffs, recommendation guides, and cloud design patterns.

- **Design principles**. Presents goal-oriented principles that build a foundation for the workload. Each principle includes a set of recommended approaches and the benefits of taking those approaches. The principles for each pillar have changed in terms of content and coverage.

  - [Reliability design principles](./reliability/principles.md)
  - [Security design principles](./security/principles.md)
  - [Cost Optimization design principles](./cost-optimization/principles.md)
  - [Operational Excellence design principles](./operational-excellence/principles.md)
  - [Performance Efficiency design principles](./performance-efficiency/principles.md)

- **Design review checklists**. Lists roughly codified recommendations that drive action. Use the checklists during the design phase of your new workload and to evaluate brownfield workloads.

  - [Design review checklist for Reliability](./reliability/checklist.md)
  - [Design review checklist for Security](./security/checklist.md)
  - [Design review checklist for Cost Optimization](./cost-optimization/checklist.md)
  - [Design review checklist for Operational Excellence](./operational-excellence/checklist.md)
  - [Design review checklist for Performance Efficiency](./performance-efficiency/checklist.md)

- **Tradeoffs**. Describes tradeoffs with other pillars. Many design decisions force a tradeoff. It's vital to understand how achieving the goals of one pillar might make achieving the goals of another pillar more challenging.

  - [Reliability tradeoffs](./reliability/tradeoffs.md)
  - [Security tradeoffs](./security/tradeoffs.md)
  - [Cost Optimization tradeoffs](./cost-optimization/tradeoffs.md)
  - [Operational Excellence tradeoffs](./operational-excellence/tradeoffs.md)
  - [Performance Efficiency tradeoffs](./performance-efficiency/tradeoffs.md)

- **Recommendation guides**. Every design review checklist recommendation is associated with one or more guides. They explain the key strategies to fulfill that recommendation. They also include how Azure can facilitate workload design to help achieve that recommendation. Some of these guides are new, and others are refreshed versions of guides that covered a similar concept.

   The recommendation guides include tradeoffs along with risks.
  - This icon indicates a tradeoff: :::image type="icon" source="./_images/trade-off.svg":::
  - This icon indicates a risk: :::image type="icon" source="./_images/risk.svg":::

- **Cloud design patterns**. Build your design on proven, common architecture patterns. The Azure Architecture Center maintains the [Cloud Design Patterns](/azure/architecture/patterns/) catalog. Each pillar includes descriptions of the cloud design patterns that are relevant to the goals of the pillar and how they support the pillar.

### Well-Architected Framework assessments

The [Well-Architected Review assessment](/assessments/azure-architecture-review/) has been refreshed. Specifically, the "Core Well-Architected Review" option now aligns to the new content structure in the Well-Architected Framework. Every question in every pillar maps to the design review checklist for that pillar. All choices for the questions correlate to the recommendation guides for the related checklist item.

No other assessments were changed as part of this refresh.

### Thematic changes

In addition to the changes in structure and consistency, you should note some thematic changes within the content. See the following key examples of these changes.

- **Workloads are more than technology**. The scope of the Well-Architected Framework is your workload. The principles and the guides provide recommendations for **people and processes** of the workload team along with technical guidance.

- Workloads exist within the context of the organization. The Well-Architected Framework frequently addresses **workload responsibility to organizational expectations**. The Well-Architected Framework calls out the benefits and tradeoffs of organizational influence.

- Prior to this refresh, the guidance was more focused on the infrastructure than on the application running on that infrastructure. Now, every pillar has **developer-centric content**.

- **Specific service configuration has been minimized**. The Well-Architected Framework pillar content is design content, not implementation content. Before the refresh, the Well-Architected Framework interwove Azure service-specific guidance and design guidance. Now, the Well-Architected Framework limits service-specific content to dedicated sections in the recommendation guides. The Well-Architected Framework service guides continue to exist to serve as the primary source for service-centric perspectives.

> [!IMPORTANT]
> As part of this restructuring, many pages have been added, moved, removed, or changed. Redirection is in place where possible, but we're aware that many existing links to the Well-Architected Framework might no longer point to the same content.

### What didn't change?

- The Well-Architected Framework continues to put a **healthy burden on workload teams**. It ensures that workload teams know **what decisions need to be made** and what risks, benefits, and tradeoffs are associated with those decisions.

- The Well-Architected Framework provides recommendations to **help you make informed and justified decisions**. The Well-Architected Framework doesn't know your business requirements or constraints, so it can't make decisions for you.

- The [Well-Architected Framework workloads](./workloads.md) and the [Well-Architected Framework service guides](./service-guides/index.yml) didn't undergo significant changes with this refresh. New workloads and service guides are ongoing additions to the Well-Architected Framework. Keep checking this page for updates.

## October 2023

- Updated service guide: [Azure Well-Architected Framework review - Azure Cosmos DB](./service-guides/cosmos-db/reliability.md)

## September 2023

- New Reliability pillar guide: [Recommendations for using availability zones and regions](./reliability/regions-availability-zones.md)
- Updated service guide: [Azure Well-Architected Framework review - Azure Database for PostgreSQL](service-guides/azure-db-postgresql-well-architected-framework.md)

## August 2023

- New Well-Architected Framework workload: [Azure VMware Solution workloads](./azure-vmware/index.yml)
- Updated service guide: [Azure Well-Architected Framework review - Virtual Machines](./service-guides/virtual-machines-review.md)

## Related resources

- [What's new in Azure Architecture Center](/azure/architecture/changelog)
- [What's new in the Microsoft Cloud Adoption Framework for Azure](/azure/cloud-adoption-framework/get-started/whats-new)