---
title: How to use the Azure Well-Architected Framework documentation
description: Learn about the Azure Well-Architected Framework, the five pillars of architectural excellence, and how the documentation is organized.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/15/2023
ms.topic: conceptual
---

# What is the Azure Well-Architected Framework?

The Azure Well-Architected Framework is a design framework that can improve the quality of a workload by helping it to:

- Be resilient, available, and recoverable.
- Be as secure as you need it to be.
- Deliver a sufficient return on investment.
- Support responsible development and operations.
- Accomplish its purpose within acceptable timeframes.

The framework is founded on the five pillars of architectural excellence, which are mapped to those goals. They are: [**Reliability**](./reliability/index.yml), [**Security**](./security/index.yml), [**Cost Optimization**](./cost-optimization/index.yml), [**Operational Excellence**](./operational-excellence/index.yml), and [**Performance Efficiency**](./performance-efficiency/index.yml).

Each pillar provides recommended practices, risk considerations, and tradeoffs. The design decisions must be balanced across all pillars, given the business requirements. The technical and actionable guidance is broad enough for all workloads and applies to a specific scenario. This guidance is centered on Azure.

Workload architecture isn't the same as its implementation. The Well-Architected Framework can set you up for success through architectural design, but the implementation choices depend on the business requirements and constraints of your organization.

## Audience

The Well-Architected Framework applies to teams that are responsible for improving workloads and addressing cross-cutting concerns.  

The Well-Architected Framework provides valuable insights and recommendations for anyone involved in the lifecycle of a workload. Regardless of your role in a workload team, whether architect, developer, operator, or business stakeholder, if you have the authority to make decisions within the scope of a workload, you can benefit from this framework.

This guidance is beneficial regardless of the scale of your organization. Whether you're part of a large enterprise, a small business, or an independent software vendor, you can move a step closer to optimal design. The framework caters to a wide range of organizational structures and sizes, ensuring that all workload users can effectively use its benefits.

If you're seeking guidance for improving a portfolio of workloads through centralized controls, this content might not fully apply. We recommend that you refer to [Cloud Adoption Framework](/azure/cloud-adoption-framework/). If you have no vested interest in designing workloads on Azure, this content isn't relevant for you.

## Goals

The primary objective of the Well-Architected Framework is to set you up for success when you deploy your workload on Azure.

- **Successful implementation**: A well-architected design leads to successful implementation. Given the breadth and depth of coverage in concepts, you're well-equipped to make informed decisions.

- **Confidence in success**: Proven assessments, seen on numerous workloads deployed on Azure, back the tenets of the framework.

- **Understand tradeoffs and risks**: The framework helps you understand that adopting the recommendations might require making choices against the other pillars. It highlights the tradeoffs and also the potential risks that you might want to address in the short term.

- **Optimize over time**: The framework is designed for iterative use and as a tool for continuous improvement. Measure the maturity of your workload against the guidance. Treat that evaluation as a moving score that evolves with your workload, ensuring that the design remains efficient and effective in meeting your business objectives.

## Layers of the Well-Architected Framework

The Well-Architected Framework is structured in a layered approach: pillars, workload, and service guides.

### Pillars

The foundation of this framework lies in the pillars. If you don't have a comprehensive understanding of these pillars, the subsequent layers—the workload layer and service guides—might not be fully comprehensible.

At the pillar level, start your journey with the **Design principles**, each of which has a specific goal. Within each principle, follow the approaches to craft your design strategy. These approaches aren't optional and must be taken into account.

:::image source="_images/principles.png" alt-text="Screenshot that shows some design principles of the Well-Architected Framework.":::

Next, move on to the **Checklist**, which is always your starting point for evaluation. Each item on the checklist is accompanied by one or more **Recommendation guides** that describe key strategies and how Azure helps you attain the recommendation.

:::image  source="_images/checklists.png" alt-text="Screenshot that shows a checklist of the Well-Architected Framework.":::

:::image source="_images/guide.png" alt-text="Screenshot that shows recommendation guides for the Well-Architected Framework.":::

Be sure to cover **cloud design patterns**. They're mapped to the pillars they directly support.

Each architectural decision entails a series of considerations. These **tradeoffs** represent recognized and accepted compromises that balance the various aspects of the framework. Tradeoffs are noted with this icon :::image type="icon" source="./_images/trade-off.svg"::: and risks are noted with this icon :::image type="icon" source="./_images/risk.svg":::.

For more information, see [About the Well-Architected Framework pillars](./pillars.md).

### Workload

The workload layer represents how the pillars apply to a specific class of workload. During the initial design phase, workload architecture is segmented based on utility, and each segment represents the prioritized or design areas. These design areas are specific to the workload class and serve as focal points for optimization. The Well-Architected Framework includes several workloads. Find one that closely matches your business requirements.

Begin with **Get started** to understand the solution context. As a refresher, read the **Design principles** to understand how the workload adopts the pillar guidance. Then, dive deep into **Design areas** that focus on the technical decision points with recommendations that follow. Workload guidance also includes an assessment that helps you evaluate your readiness in production.

For more information, see [About the Well-Architected Framework workloads](./workloads.md).

### Service guides

Service guides are instrumental in decision-making related to the individual Azure components that reside within the workload. They offer the core features and capabilities of each service that are necessary to attain a well-architected state. It's important to note that these guides aren't configuration guides. Also, they aren't a compiled list of all features and capabilities. The intent is to highlight the utility of the features through Well-Architected pillar perspectives.

For more information, see [the available guides](./service-guides/index.yml).
 
### Assessment

Microsoft Azure Well-Architected Review is offered at no charge. It's a collection of questionnaires tied to the pillar checklists to evaluate your design choices. Track your score through iterative runs to identify possible areas for enhancement.

For more information, see [Azure Well-Architected Review tool](/assessments/?id=azure-architecture-review&mode=pre-assessment).

## Related links

Here are some resources to get started with using the Well-Architected Framework documentation:

- [Azure Well-Architected Framework](/azure/well-architected/)
- [Introducing the Azure Well-Architected Framework](https://azure.microsoft.com/blog/introducing-the-microsoft-azure-wellarchitected-framework/)
- [Training for the Well-Architected Framework](/training/modules/azure-well-architected-introduction/)
- [Azure Well-Architected](https://azure.microsoft.com/solutions/cloud-enablement/well-architected/)
