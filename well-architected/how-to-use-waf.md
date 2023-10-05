---
title: How to use the Azure Well-Architected Framework documentation
description: See how Azure Well-Architected Framework documents are organized. Understand the meaning of bold and italic formatting in the documentation.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 10/04/2023
ms.topic: conceptual
---

# What is Azure Well-Architected Framework

The Azure Well-Architected Framework (WAF) is a design framework that can improve the quality of a workload, by making it:

- just as resilient, available, and recoverable as it needs to be.
- just as secure as it needs to be.
- deliver a sufficient return on investment.
- get developed and operated responsibly.
- accomplish its purpose within an acceptable period.

The framework is founded on the five pillars of architectural excellence, which are mapped to those goals. They are: [**Reliability**](./reliability/index.yml), [**Security**](./security/index.yml), [**Cost Optimization**](./cost-optimization/index.yml), [**Operational Excellence**](./operational-excellence/index.yml), and [**Performance Efficiency**](./performance-efficiency/index.yml).

Each pillar provides recommended architectural practices, risk considerations, and tradeoffs. The design decisions must be balanced across all pillars, given the business requirements. The technical and actionable guidance is broad enough to be considered by all workloads and intended to be applied to a specific scenario. This guidance is centered on Azure.

Workload architecture isn’t the same as its implementation. WAF can set you up for success through architectural design, but the implementation choices depend on the business requirements and constraints of the organization. 

## Audience

WAF applies to personas who have an accountability toward improving their workload by addressing cross-cutting concerns.  

WAF can provide valuable insights and recommendations for anyone involved in the lifecycle of a workload. Irrespective of your role in a workload team, be it an architect, developer, operator, business stakeholder, if you have the authority to make decisions within the scope of a workload, you'll benefit from this content. 

This guidance is beneficial regardless of the scale of your organization. Whether you're part of a large enterprise, a small business, or an independent software vendor, you stand to gain a step toward optimal design. It’s designed to cater to a wide range of organizational structures and sizes, ensuring that all workload users can use its benefits effectively.

If you're seeking guidance for improving a portfolio of workloads through centralized controls, the applicability of this content might be limited. We recommend that you refer to [Cloud Adoption Framework](/azure/cloud-adoption-framework/). If you have no vested interest in designing workloads on Azure, this content isn’t relevant for you.

## Goals

WAF’s primary objective is to set you up for success when deploying your workload on Azure. 

- **Successful implementation**: A well-architected design leads to successful implementation. Given the breadth and depth of coverage in concepts, you’re well-equipped to make informed decisions.

- **Confidence in success**: The tenets of the framework are backed by proven assessments seen on numerous workloads deployed on Azure. 

- **Understanding tradeoffs and risks**: The framework helps you understand that adopting the recommendations may require making choices against the other pillars. It highlights the impact that you might choose to accept. It will also highlight potential risks that you might want to address in the short term.

- **Optimization over time**: It’s designed for iterative use and as a tool for continuous improvement. Measure the maturity of your workload against the guidance. Treat that evaluation as a moving score that evolves with your workload, ensuring that the design remains efficient and effective in meeting your business objectives.

## Layers of WAF

The Well-Architected Framework is structured in a layered approach: pillars, workload, and service guides. 

- **Pillars**: The foundation of this framework lies in the pillars. Without a comprehensive understanding of these pillars, the subsequent layers - the workload layer and service guides - may not be fully comprehensible. 

    At the pillar level, start your journey with the **Design principles**, each of which has a specific goal. Within each principle, follow the approaches to crafting your design strategy. These approaches aren't optional and must be taken into account. 
    
    Next, move on to the **Checklist**, which should always be your starting point for evaluation. Each item on the checklist is accompanied by one or more **Recommendation guides** that describe key strategies and how Azure helps you attain the recommendation. 

    Make sure you cover **cloud design patterns**. They're mapped to the pillars they directly support.

    Each architectural decision entails a series of considerations. These **tradeoffs** represent recognized and accepted compromises that balance the various aspects of the framework.

    > Refer to: [About WAF pillars](./pillars.md).

- **Workload**: The workload layer represents the application of these pillars to a specific class of workload. During the initial design phase, workload architecture is segmented based on their utility, which becomes your prioritized or design areas. These design areas are specific to the class of the workload and serve as focal points for optimization. WAF includes several workloads, find one that closely matches your business requirements.

    - Start with the **Get started** to understand the solution context. As a refresher, read the **Design principles** to understand how the workload adopts the pillar guidance. Then, dive deep into **Design areas** that focus on the technical decision points with recommendations to follow. Workload guidance also includes an Assessment that is intended to help you evaluate your readiness in production.

    > Refer to: [About WAF workloads](./workloads.md).

- **Service guides**: Service guides are instrumental in decision-making related to the individual Azure components that resides within the workload. They offer the core features and capabilities of each service that are necessary to attain a well-architected state. It’s important to note that these guides aren’t configuration guides. Also, they aren’t a compiled list of all features and capabilities. The intent is to highlight the utility of the features through Well-Architected pillar perspectives.

    > [Browse the available guides](./service-guides/index.yml)
 
- **Assessment**: Microsoft Azure Well-Architected Review is offered at no charge. It’s a collection of questionnaires that’s tied to the pillar checklists as a way to evaluate your design choices. Track your score through iterative runs to identify possible areas for enhancement.

    > Refer to: [Azure Well-Architected Review tool](/assessments/?id=azure-architecture-review&mode=pre-assessment).

## Resources

Here are some resources that can help you get started with using the Well-Architected Framework documentation:

- [Microsoft Azure Well-Architected Framework](/azure/well-architected/)
- [Introducing the Microsoft Azure Well-Architected Framework](https://azure.microsoft.com/blog/introducing-the-microsoft-azure-wellarchitected-framework/)
- [Training for the Well-Architected Framework](/training/modules/azure-well-architected-introduction/)
- [Azure Well-Architected | Microsoft Azure](https://azure.microsoft.com/solutions/cloud-enablement/well-architected/)
