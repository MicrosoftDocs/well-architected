---
title: What's new in Azure Well-Architected Framework
description: What's new in the Azure Well-Architected Framework.
author: ckittel
ms.author: chkittel
ms.date: 09/22/2023
ms.topic: conceptual
---

# What's new in Azure Well-Architected Framework

## November 2023

> [!NOTE]
> The Azure Well-Architected Framework launched a significant content refresh across all five pillars. We're breaking from our standard "What's new" format for this month, because the changes launched with [Microsoft Ignite 2023](https://ignite.microsoft.com/) saw a significant shift that goes beyond bullet points.

### The five core pillars of architecture - restructuring

All pillars of the Azure Well-Architected Framework were significantly restructured and expanded upon. They follow a common structure consisting exclusively of:

- **Design principles**. Presents goal-oriented principles that build a foundation for the workload. Each principle includes a set of recommended approaches and benefits of following those approaches. The principles for each pillar have changed in terms of words and coverage.

  - [Reliability design principles](./reliability/principles.md)
  - [Security design principles](./security/principles.md)
  - [Cost Optimization design principles](./cost-optimization/principles.md)
  - [Operational Excellence design principles](./operational-excellence/principles.md)
  - [Performance Efficiency design principles](./performance-efficiency/principles.md)

- ** Design review checklist**. Helps drive action through roughly codified recommendations. Use the checklist during the design phase of your new workload and also to evaluate brownfield workloads. 

  - [Design review checklist for Reliability](./reliability/checklist.md)
  - [Design review checklist for Security](./security/checklist.md)
  - [Design review checklist for Cost Optimization](./cost-optimization/checklist.md)
  - [Design review checklist for Operational Excellence](./operational-excellence/checklist.md)
  - [Design review checklist for Performance Efficiency](./performance-efficiency/checklist.md)

- **Tradeoffs**. Describes tradeoffs with other pillars. Many design decisions will force a tradeoff. It's vital to understand how achieving goals of a pillar might make goals in another pillar more challenging. 

  - [Reliability tradeoffs](./reliability/tradeoffs.md)
  - [Security tradeoffs](./security/tradeoffs.md)
  - [Cost Optimization tradeoffs](./cost-optimization/tradeoffs.md)
  - [Operational Excellence tradeoffs](./operational-excellence/tradeoffs.md)
  - [Performance Efficiency tradeoffs](./performance-efficiency/tradeoffs.md)

  In addition to the preceding set of articles, you'll also see them across the various recommendation guides, along with risks. Tradeoffs are noted with this icon :::image type="content" source="./_images/trade-off.svg" alt-text="Tradeoff icon"::: and risks are noted with this icon :::image type="content" source="./_images/risk.svg" alt-text="Risk icon":::.

-  **Recommendation guides**. Every design review checklist recommendation is associated with one or more guides. They explain the key strategies for fulfilling that recommendation. It also includes how Azure can facilitate workload design to help achieve that recommendation. Some of these guides are new and others are refreshed versions of guides that covered a similar concept.

- **Cloud design patterns**. Build your design on proven, common architecture patterns. The Azure Architecture Center maintains the [Cloud Design Patterns](/azure/architecture/patterns/) catalog. Each pillar now has a mapping of which cloud design patterns are relevant to the goals of the pillar and how they support the pillar.

### Azure Well-Architected Framework assessments

The [Azure Well-Architected Review assessment](/assessments/azure-architecture-review/) has been refreshed. Specifically, the "Core Well-Architected Review" has been aligned to the new content structure in WAF. Every question in every pillar maps to the design review checklist for that pillar. All choices for the questions are tied to the recommendation guides for that related checklist item. 

No other assessments were changed as part of this refresh.

### Thematic changes

Besides the structure and consistency changes noted above, it's important to also note that the Azure Well-Architected Framework saw some "thematic" changes within the content. Some key examples of these follow.

- **Workloads are more than technology**. The scope of WAF is your workload. Starting with the principles and all through the guides, recommendations are provided for **people and processes** of the workload team along with technical guidance. 

- Workloads exist within the context of the organization. WAF frequently addresses **workload responsibility to organizational expectations**. WAF calls out benefits and tradeoffs of organizational influence through platform and enabling teams.

- Prior to this refresh, guidance was more focused on _infrastructure_ than the application running on that infrastructure. Now every pillar has **developer-centric content** .

- **Specific service configuration has been minimized.** WAF pillar content is design content, not implementation. Prior to the refresh, WAF interwove service-specific guidance and design guidance which confused the focus. Now WAF limits Azure service-specific content to dedicated sections in recommendation guides. WAF service guides continue to exist to serve as the primary source for service-centric perspectives.

> [!IMPORTANT]
> As part of this significant restructuring, a lot of pages have been added, moved, removed, and changed. Redirection is in place where possible, but we're aware that many existing links into the Azure Well-Architected Framework may no longer point to the same content it used to.

### What did not change?

- WAF continues to put a **healthy burden on workload teams**. It strives to make sure that workload teams know **what decisions need to be made**, and the risks, benefits, and tradeoffs associated with those decisions.

- WAF has recommendations to **help you make informed and justified decisions**. WAF doesn't know your business requirements or constraints, so it can't make decisions for you.

- [WAF workloads](./workloads.md) and [WAF service guides](./service-guides/index.yml) did not experience significant changes with this refresh. New workloads and service guides are ongoing additions to WAF. Keep checking this page for updates. 

<!-- TODO: Some of these links are broken and some articles haven't shown up yet. -->
## October 2023

- Updated service guide: [Azure Well-Architected Framework review - Azure Cosmos DB](./service-guides/cosmos-db/reliability.md)

## September 2023

- New Reliability pillar guide: [Recommendations for using availability zones and regions](./reliability/regions-availability-zones.md)
- Updated service guide: [Azure Well-Architected Framework review - Azure Database for PostgreSQL](./services/data/azure-db-postgresql/azure-db-postgresql-well-architected-framework.md)

## August 2023

- New WAF workload: [Azure VMware Solution workloads](./azure-vmware/index.yml)
- Updated Reliability pillar guide: [Design reliable Azure applications](./resiliency/app-design.md)
- Updated Reliability pillar guide: [Target functional and nonfunctional requirements](./resiliency/design-requirements.md)
- Updated service guide: [Azure Well-Architected Framework review - Virtual Machines](./service-guides/virtual-machines-review.md)

## Related resources

- [What's new in Azure Architecture Center](/azure/architecture/changelog)
- [What's new in the Microsoft Cloud Adoption Framework for Azure](/azure/cloud-adoption-framework/get-started/whats-new)
