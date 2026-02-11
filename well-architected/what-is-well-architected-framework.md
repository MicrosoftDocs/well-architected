---
title: How to use the Azure Well-Architected Framework documentation
description: Learn about the Azure Well-Architected Framework, the five pillars of architectural excellence, and how to use the guidance to drive improvements in your workload.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 07/14/2025
ms.topic: concept-article
ms.update-cycle: 1095-days
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

For information about the role and duties of an architect, see [Architect's fundamentals](./architect-role/fundamentals.md) and [Architect's checklist](./architect-role/checklist.md).

## Goals

The primary objective of the Well-Architected Framework is to set you up for success when you deploy your workload on Azure.

- **Successful implementation**: A well-architected design leads to successful implementation. Given the breadth and depth of coverage in concepts, you're well-equipped to make informed decisions.

- **Confidence in success**: Proven assessments, seen on numerous workloads deployed on Azure, back the tenets of the framework.

- **Understand tradeoffs and risks**: The framework helps you understand that adopting the recommendations might require making choices against the other pillars. It highlights the tradeoffs and also the potential risks that you might want to address in the short term.

- **Optimize over time**: The framework is designed for iterative use and as a tool for continuous improvement. Measure the maturity of your workload against the guidance. Treat that evaluation as a moving score that evolves with your workload, ensuring that the design remains efficient and effective in meeting your business objectives.

## Building blocks of the framework

The Well-Architected Framework is structured in a layered approach: pillars, workload, and service guides.

#### Pillars

The foundation of this framework lies in the pillars. If you don't have a comprehensive understanding of these pillars, the subsequent layers: workload layer and service guides, might not be fully comprehensible. Each pillar presents these elements:

- **Design principles**. Provide the foundation of good design, each of which has a specific goal. The principles also describe the recommended approaches.

- **Design Review Checklists**. Each item on the checklist is accompanied by one or more **Recommendation guides** that describe key strategies and how Azure helps you attain the recommendation. 

- **Cloud design patterns**. Be sure to understand the related **cloud design patterns**. They're mapped to the pillars they directly support.

- **Tradeoffs**. Each architectural decision entails a series of considerations. These **tradeoffs** represent recognized and accepted compromises that balance the various aspects of the framework. Tradeoffs are noted with this icon :::image type="icon" source="./_images/trade-off.svg"::: and risks are noted with this icon :::image type="icon" source="./_images/risk.svg":::.

- **Maturity model**. Describes phased approach to adopt the Azure Well-Architected Framework by starting with easy or essential recommendations. As business needs evolve, gradually improve systems—from early-stage workloads to mature, business-critical solutions.

For more information, see [About the Well-Architected Framework pillars](./pillars.md).

#### Workloads

The workload layer represents how the pillars apply to a specific class of workload. During the initial design phase, workload architecture is segmented based on utility, and each segment represents the prioritized or design areas. These design areas are specific to the workload class and serve as focal points for optimization. The Well-Architected Framework includes several workloads. Read the one that closely matches your business requirements. You do not need to read workload guidance for classes of workloads that are not aligned with your scenario.

Begin with **Get started** to understand the solution context. As a refresher, read the **Design principles** to understand how the workload adopts the pillar guidance. Then, dive deep into **Design areas** that focus on the technical decision points with recommendations that follow. Workload guidance also includes an assessment that helps you evaluate your readiness in production.

For more information, see [About the Well-Architected Framework workloads](./workloads.md).

#### Service guides

Service guides play a key role in making decisions about individual Azure components within a workload. They outline the key features and capabilities needed to achieve architectural excellence and provide recommended configurations to establish a strong foundation. While not exhaustive, these guides emphasize how each service can address cross-cutting concerns and support workload effectiveness.

For more information, see [the available guides](./service-guides/index.yml).

#### Design essentials

Design guides serve as focused resources that deliver prescriptive guidance for applying the framework’s key strategies. They draw directly from the foundational approaches defined in the pillar recommendations, yet intentionally extend across pillars to show how these strategies interact in practice. Instead of covering entire architectural journeys, design guides concentrate on a specific practice or choice, giving teams clear, targeted direction to put WAF principles into action.

For more information, see [Design essentials](./design.md).

#### Assessment

Microsoft Azure Well-Architected Review is offered at no charge. It's a collection of questionnaires tied to the pillar checklists to evaluate your design choices. Track your score through iterative runs to identify possible areas for enhancement.

For more information, see [Azure Well-Architected Review tool](/assessments/?id=azure-architecture-review&mode=pre-assessment).

## Suggested learning process

The Well-Architected Framework covers best practices that are applicable to any class of workload. This guidance includes not only the foundational principles of good design and trade-offs but also the application of those principles to the components of your architecture. We acknowledge that reading this guidance end-to-end can be overwhelming. Consider following this learning path:

1. **Understand all of the design principles**. Learn the design principles and the approaches for all pillars. At the start of design, understanding good architecture is more important than knowing how to build it. Within each principle, follow the approaches to craft your design strategy. These approaches aren't optional and must be taken into account.

    :::image source="_images/principles.png" alt-text="Screenshot that shows some design principles of the Well-Architected Framework.":::

   - [Reliability design principles](./reliability/principles.md)
   - [Security design principles](./security/principles.md)
   - [Cost Optimization design principles](./cost-optimization/principles.md)
   - [Operational Excellence design principles](./operational-excellence/principles.md)
   - [Performance Efficiency design principles](./performance-efficiency/principles.md)

2. **Prioritize checklist items**. Start by only addressing the checklist items relevant to your workload and business goals. Consider factors like business criticality, compliance needs, and time to market. Adjust priorities as these factors change to improve workload quality. Defer checklist items that are less relevant to your workload's success.

    :::image  source="_images/checklists.png" alt-text="Screenshot that shows a checklist of the Well-Architected Framework.":::

   - [Design review checklist for Reliability](./reliability/checklist.md)
   - [Design review checklist for Security](./security/checklist.md)
   - [Design review checklist for Cost Optimization](./cost-optimization/checklist.md)
   - [Design review checklist for Operational Excellence](./operational-excellence/checklist.md)
   - [Design review checklist for Performance Efficiency](./performance-efficiency/checklist.md)

3. **Be ready to make important tradeoffs**. Look at examples of pillar tradeoffs to see how prioritization can favor one pillar over another. Making strategic design tradeoffs is an important part of decision making.

   - [Reliability tradeoffs](./reliability/tradeoffs.md)
   - [Security tradeoffs](./security/tradeoffs.md)
   - [Cost Optimization tradeoffs](./cost-optimization/tradeoffs.md)
   - [Operational Excellence tradeoffs](./operational-excellence/tradeoffs.md)
   - [Performance Efficiency tradeoffs](./performance-efficiency/tradeoffs.md)

4. **Match workload scenarios**. Find a workload guide that matches your scenario and follow the design methodology across all technical and operational areas. These guides help highlight the most relevant considerations. For more information, see the examples listed under [Azure Well-Architected Framework workloads](./workloads.md).

5. **Select the appropriate Azure service and configure it correctly**. These service guides are designed to support your decision-making for each Azure component within a workload.

## Adopt a maturity model

Consider adopting a phased approach to consuming the Azure Well-Architected Framework. Classify the framework's recommendations by what's easy to achieve or must be achieved initially. Then, as the workload's business requirements change, incrementally evolve production-ready systems. For example, initial phases of adoption could apply to workloads early in their funding and development process, setting a solid foundation for good design. Mature phases of alignment could apply to solutions later in the development cycle, with the highest levels reserved for always-on, business-critical solutions.

The Well-Architected Framework includes a maturity model. It provides a structured curriculum and milestones for workload teams to follow. 

The phased approach was developed after reviewing how numerous Azure customers apply the framework in their solutions. This guidance is applicable to all workload teams, from startups to mature enterprises. Startups use the model to create foundational strategies that can be implemented over time. Mature enterprises, whose architecture has evolved, can also adopt the model to optimize their workloads further to have a common approach for measuring improvements across teams. Also, partners can use the model to evaluate the maturity of the workload and implement targeted recommendations.

The model is categorized by pillars and divided into five levels. While the levels in each pillar represent unique characteristics of that pillar, there are common themes across all of them:

|Maturity phase|Focus|Strategy|
|---|---|---|
|Level 1|Establish a solid foundation on Azure|Focus on leveraging Azure's core and native functionalities, while taking advantage of well-established cloud design patterns and best practices.|
|Level 2|Build workload assets|Address technical challenges on components directly owned by the workload team, including application code, deployment assets, and operational procedures.|
|Level 3|Be production-ready|Involve business stakeholders in decision-making and consider tradeoffs with other pillars. For new workloads, this is usually the final step before going to production.|
|Level 4|Learn from production|Shift focus to maintaining a stable environment, managing change, and accommodating new requirements based on business changes and production learnings.|
|Level 5|Future-proof with agility|Strive for aspirational quality. You're adept at change so you can handle new market conditions and changes to external influences like technology, business requirements, or regulatory concerns.|

These boundaries are suggested guidelines and don't need to be taken as strict rules. The actual journey will depend on your organizational goals and workload requirements.

In each level, explore the tabbed views that highlight the strategy focus for each level. 

- [Reliability maturity model](./reliability/maturity-model.md)
- [Security maturity model](./security/maturity-model.md)
- [Cost Optimization maturity model](./cost-optimization/maturity-model.md)
- [Operational Excellence maturity model](./operational-excellence/maturity-model.md)
- [Performance Efficiency maturity model](./performance-efficiency/maturity-model.md)

The guidance includes an assessment to help you pinpoint the recommendations that align with your target maturity level. Take the assessment here: [Azure Well-Architected Framework Maturity Model Assessment](/assessments/af7d9889-8cb2-4b8b-b6bb-e5a2e2f2a59c).

## Adopt AI in your operations

The Azure Well-Architected Framework includes guidance for intentionally and incrementally adopting AI in operational workflows. Its purpose is to help teams understand the costs, risks, and organizational friction associated with AI, when thinking about using AI for planning, development processes, and runtime operations. 

We recommend starting with the [Operational Excellence maturity model](./operational-excellence/maturity-model.md). This provides a practical entry point for exploring AI usage patterns, understanding trade-offs while maintaining human oversight, to drive efficiency. 

Within the recommendation guides, look for :::image type="icon" source="./_images/ai.svg"::: that annotate examples of where AI can augment tasks, such as workflow setup, process automation, monitoring, and enforcement. For more information, see the guides linked in this checklist article: [Operational Excellence checklist](./operational-excellence/checklist.md).

## Take a pragmatic approach

It's important to adopt a pragmatic approach to avoid analysis paralysis. Here are some key considerations:

- **Evaluate the value of practices**. All of the practices we recommend offer value, but that value can vary depending on your team and current maturity level. Implementing some practices too early may yield little benefit, while delaying implementation of others can increase costs, complexity, and non-strategic technical debt because you might have already optimized other practices to compensate.

    Prioritize practices that deliver immediate, meaningful benefits and enable other key practices.

- **Evaluate the cost of practices**. Each practice has a cost to implement and operate, which includes financial, effort, and complexity costs. These costs can vary based on your maturity level.

    - There will be higher implementation costs if practices are adopted before the workload team is ready. 

    - There will be higher implementation and operational costs, if practices are adopted too late, leading to rework or integration difficulties.

    - Practices may be discontinued if their operating costs exceed their value at higher maturity levels. 

    Based on the requirements, have prerequisites and exit criteria defined for your phase of maturity. Prioritize practices that would be more costly or complex to adopt later, and don't create unnecessary complexity or operational burdens. 

- **Be deliberate when choosing your implementation sequence**. Practices are interdependent and the order in which they are implemented can make a significant difference. Some practices are building blocks for others and can have a large effect on the cost, effort, and complexity of downstream practices. Consider time-to-outcomes when mapping out your journey.

- **Be realistic about your capacity**. The resources your organization can dedicate toward implementing and operating a workload is often limited.

    - Estimate the workload team's capacity to implement and operate. 

    - Costs are additive. As operational costs increase, the capacity to implement new practices declines.

    - Tradeoffs can create opportunity costs. Choosing practices to implement now means deferring others.
    

## Related links

Here are some resources to get started with using the Well-Architected Framework documentation:

- [Azure Well-Architected Framework](/azure/well-architected/)
- [Training for the Well-Architected Framework](/training/modules/azure-well-architected-introduction/)

