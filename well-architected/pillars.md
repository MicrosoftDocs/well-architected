---
title: Microsoft Azure Well-Architected Framework
description: Learn about the five pillars of the Azure Well-Architected Framework and how they can produce a high- quality, stable, and efficient cloud architecture.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/15/2023
ms.topic: conceptual
---

# Microsoft Azure Well-Architected Framework pillars

The Azure Well-Architected Framework pillars drive architectural excellence at the fundamental level of a workload.

Use this matrix to familiarize yourself with the key concepts:

| Pillar | Workload concern | Apply the principles | Strike a balance
|--------|-------------|-------------------|-----------
| [**Reliability**](reliability/index.yml) | Resiliency, availability, recovery | Design for business requirements, resilience, recovery, and operations, while keeping it simple. <br> [Design principles](reliability/principles.md) | [Tradeoffs](reliability/tradeoffs.md) |
| [**Security**](security/index.yml) | Data protection, threat detection, and mitigation | Protect confidentiality, integrity, and availability. <br> [Design principles](security/principles.md)|[Tradeoffs](security/tradeoffs.md) |
| [**Cost Optimization**](cost-optimization/index.yml) | Cost modeling, budgets, reduce waste | Optimize on usage and rate utilization while keeping a cost-efficient mindset. <br> [Design principles](cost-optimization/principles.md) | [Tradeoffs](cost-optimization/tradeoffs.md) |
| [**Operational Excellence**](operational-excellence/index.yml) | Holistic observability, DevOps practices | Streamline operations with standards, comprehensive monitoring, and safe deployment practices. <br> [Design principles](operational-excellence/principles.md)|[Tradeoffs](operational-excellence/tradeoffs.md) |
| [**Performance Efficiency**](performance-efficiency/index.yml) | Scalability, load testing | Scale horizontally, test early and often, and monitor the health of the solution. <br>[Design principles](performance-efficiency/principles.md)|[Tradeoffs](performance-efficiency/tradeoffs.md) |

> [!IMPORTANT]
>
> The reference architectures available in the [**Azure Architecture Center**](/azure/architecture/browse/) are designed with the design principles in mind. The architecture articles describe a prescriptive path for applying the design principles and provide a holistic view on the [**Ten design principles for Azure applications**](/azure/architecture/guide/design-principles/).

## Assessment review tool

Assess your workload by using the core pillars to **identify and prioritize opportunities** for improving the posture of your workloads.

Start your assessment with the [**Azure Well-Architected Review**](/assessments/azure-architecture-review/).

:::image type="content" source="./_images/well-architected-review.png" alt-text="Screenshot of the Microsoft Azure Well-Architected Review." lightbox="./_images/well-architected-review.png":::

## Integrate with Azure recommendation services

- [**Azure Advisor**](/azure/advisor/) uses the **core pillars** as a basis for analyzing your resource configuration and usage telemetry and provides appropriate recommendations.

- [**Azure Advisor score**](/azure/advisor/azure-advisor-score) is a core feature of Azure Advisor that aggregates Advisor recommendations into a simple, actionable score. You can categorize the overall score into the core pillars of the Well-Architected Framework. Use the score to prioritize the actions that yield the biggest improvement.


